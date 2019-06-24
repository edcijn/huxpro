---
layout: post
title: "Rust语言会让程序员更加996？"
subtitle: " \"本文纯属娱乐\""
date: 2019-06-23 23:00:00 +0930
author: Benben
header-img: img/about-bg.jpg
catalog: true
tags:
   - Rust
   - Libra
---

#### Facebook'发币'了

这几天Libra因为被Facebook开源发布而火了起来。本来我倒是没有太注意这个，不过它突然窜上了“热门”榜因此又被github推了到了首页，作为F记React拥笃的我于是关注了一下，噢，这就是Facebook发的币啊！

浏览了一下项目目录发现底层是用Rust写的，没错就是那个最近在某个编程语言满意度排行榜上老是`莫名其妙`排第一的Rust语言。好吧，其实我对各种币圈兴趣一直不是太大，也没有太仔细研究过，`我之前基本上认为这类东西主要是收智商税的（捂脸），普通人去玩就是被割韭菜的...`，但是Facebook搞的东西总该是来头不小吧，于是决定趁机了解一下，就先从Rust开始了。

#### Rust-lang

##### 第一印象

`Rust`语言对标的是`C++`的一部分应用场景（_C++：又来？黑人问号_），各种benchmark显示Rust性能和C++不相上下，编译时间差不多，然而亮点是确保内存安全...?好像有点牛。

看了一下Libra项目里的Rust部分，给我感觉是这写法有点飘啊，不太像是跟C++吃一晚饭的，反倒是有点像Javascript。除此以外，关键字命名也太任性了，各种缩写凭空增加了大脑负担，不知道为什么会这样。

接下来看了一下官方手册。

##### 变量定义

到三章我隐约感觉似乎并没有宣传得那么美好。变量[`摘自官方文档`]：
<pre>
   fn main() {
       let x = 5;
       println!("The value of x is: {}", x);
       x = 6;
       println!("The value of x is: {}", x);
   }
</pre>

> error[E0384]: cannot assign twice to immutable variable `x`
--> src/main.rs:4:5

这里倒是没毛病，问题是下面定义constant：
<pre>
   const MAX_POINTS: u32 = 100_000;
</pre>

> First, you aren’t allowed to use mut with constants. Constants aren’t just immutable by default—they’re always immutable.

也就是说immutable还分永远immutable和**有时候**immutable？在同一个作用域都可以改变了还怎么能叫immutable呢。
比如说：
<pre>
   fn main() {
       let spaces = "   ";
       let spaces = spaces.len();
       println!("x = {}", spaces);
   }
</pre>

这样的写法被认为是完全"正确"的，spaces在已经被绑定过了对象和作用域，而且它是immutable的(`因为没有加mut关键字`)的情况下，它同时又可以被重新定义到其它对象？`黑人问号×2`

一个可变的默认不可变的对象 makes no sense...

好吧，我相信设计者的智慧应该是不会比我差的，这样设计一定有它的理由和应用场景。比如说：

_约定:_
- 一般不会进行改变的变量才用let
- 只有在必须重新绑定的情况下才再次使用let
- 如果定义可改变的变量那么就用let mut

问题是程序员<s>是很懒的</s>都太忙了，不可能这样干，为了省事最后的结果八成就是能用let的地方都直接用let了，谁管你mut不mut...编译器都管不住你，那你还会自己给自己找麻烦么。

所以实际上最后可能都当动态类型来用了。即let定义的变量明明是不可变的，但是我想改变它的时候也能改变，假如别人改变了它我也不知道它改变了，而即便是我估计到它变了也并不知道是值变了还是类型变了。

这样看起来似乎也没有太大的影响？反正动态类型语言都是这么用的（除了默认immutable的问题，就当它是句废话好了）。然而可读性和可维护性降低了一个档次，应用场景自然受限。
<span style="color: rgba(0,0,0,0)">本文由benben@blog.benbenrun.com原创，转载请注明来源。</span>

##### `borrow`和`owner`

borrow实际上就是引用，但是因为变量所有权即`owner`已经被绑定，所以在引用的地方是immutable的。比如：

<pre>
   fn main() {
       let s = String::from("hello");

       change(&s);
   }

   fn change(some_string: &String) {
       some_string.push_str(", world");
   }
</pre>

> error[E0596]: cannot borrow immutable borrowed content `*some_string` as mutable

> Just as variables are immutable by default, so are references. We’re not allowed to modify something we have a reference to.

讲得通。不过如果就是想mutate引用的变量呢，似乎变得更麻烦了。
<span style="color: rgba(0,0,0,0)">本文由benben@blog.benbenrun.com原创，转载请注明来源。</span>
##### `lifetime`

`lifetime`生命周期真是把我弄蒙圈了。本来应该是个挺好理解的概念吧，加上前面owner的定义，lifetime的出现显得还比较自然`->`反正就是你的owner挂掉你就挂掉呗。然而看了一下例子才发现远没有这么简单。比如：

<pre>
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}

fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
</pre>

函数`longest`要定义成这样才能通过编译，即在每个参数和返回值声明的地方都必须标记一个'a这样的生命周期，否则按照通常写法的话会报错：

> error[E0106]: missing lifetime specifier

我在想这么简单一个函数功能，声明起来都看着头大了，那复杂一点还能看懂么。

更要命的是，如果要在函数声明里带上生命周期，那也就是说程序员需要在一开始就安排好一切内存使用的问题，而不能是在逻辑的某个地方解决它（just works），就好像原来我脑子里是一副流程图或者关系图，而现在我还要在上面平铺一层状态图，是不是要求太高了。当项目复杂到一定程度，虽然可以减少在内存泄漏上的抓狂，但却要花更多脑力去弄明白生命周期，并且还要让编译器批准。

几乎可以想到这样几个场景：
1. 某经验不足的Rust程序员赶上项目deadline，原本可以在某个不太紧要的实现上稍微马虎一点，在程序运行期解决问题，而现在他连编译器都糊弄不过去，根本没法提交代码，只好继续加班。没办法，总有不那么天才的人吧。
2. 某经验丰富的程序员`也是经过加班`终于顺利地搞定了编译器，提交了修改，然而此时被告知需求变更了，得改一改。原本就是个很头疼的事，现在加上Rust对编写的高要求，重构的代价更大。
3. 某legacy code有问题，但是还必须用，只能打打补丁。然而由于加上了`不知道靠不靠谱的`生命周期之后新接手的人更加搞不明白它原本应该是个什么思路。咦这好像是个优点？似乎应该把这一条加到官方文档的functional language features里面去🤣
<span style="color: rgba(0,0,0,0)">本文由benben@blog.benbenrun.com原创，转载请注明来源。</span>
##### 小结

看了一会最终还是没能坚持下去，我大概不会相信Rust所宣传的安全、性能和开发效率的平衡，除非写它的都是牛人。放到一般的场景来看，Rust几乎是只有安全性稍好，而性能和开发效率很可能只能取其一。函数式编程原本写起来是比较飘逸的，比如match，但是这略微超出人脑考虑范围的内存管理，在项目大了之后可能是灾难，何况还有函数式编程和'动态'类型的副作用。难以想象一个逻辑不好但是掌握了全部Rust语法的程序员可以把它写成什么样，他可以轻松通过编译，但是只有编译器和他自己能看懂（甚至过一段时间连自己都看不懂）。不过具体还要看怎么应用，目前除了Libra似乎没有什么大的项目（`没有关注`），假如哪一天真的大火也不是特别奇怪的事。

#### 回Libra

琢磨了半天都是在说Rust，因为Libra确实除了是Facebook发的以外没什么别的吸引我的地方。我甚至觉得Facebook纯粹是跑马圈地，赌一把万一成了呢。按照目前的资料来看Libra币和一揽子真实货币直接挂钩，那意味着必须有一个平台让拿真金白银来购买Libra，而这个平台是这一百家企业和美国政府监管的，欧美老百姓会相信这样比现金更保护隐私安全么，有不少人现在连网上支付都不太愿意呢。

