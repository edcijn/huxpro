---
layout: post
title: "自动高亮重新渲染的React组件"
subtitle: "Highlight Updates with React DevTools"
date: 2019-06-24 21:30:00 +0930
author: Benben
header-img: img/about-bg.jpg
catalog: true
permalink: /:year/:month/react-devtools-highlight-updates/
tags:
    - React
---

#### 前置条件

- Chrome等浏览器
- 安装React DevTools Extension

#### 操作方法

打开Chrome开发者工具中的React DevTools，勾选工具上方的一行小字：Highlight Updates，如果找不到的话点开旁边的设置按钮，在pop up里面勾选这个属性。

如图：
![](/img/post/2019-06-24-1.png)

接下来对页面进行操作，可以看到所有被重新渲染了的组件都被高亮显示了，而且是变色的。
如果你是第一次用这个功能，可能会感到震惊，怎么重渲了如此多不想相干的东西？好吧，接下来就可以去针对渲染情况优化性能了。

参考docs：https://reactjs.org/docs/optimizing-performance.html#avoid-reconciliation
<span style="color: rgba(0,0,0,0);font-size: 6px">本文由Benben[blog.benbenrun.com]原创，转载请注明来源。</span>