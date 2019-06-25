---
layout: post
title: "Embed Disqus"
subtitle: " \"碰到了一点儿小问题\""
date: 2019-06-22 21:30:00 +0930
author: Benben
header-img: false
catalog: true
permalink: /:year/:month/embed-disqus/
tags:
    - 博客
    - Jekyll
    - Disqus
---

##### "We were unable to load Disqus."

修改完jekyll配置之后，在Disqus配置好站点，但评论模块却加载不出来。研究了一会发现有2个地方需要注意：

###### Step 1

![](/img/post/2019-06-22-1.png)

内嵌**Disqus代码**里用到的`disqus_shortname`并不是disqus的登录名，而是上图这个位置显示的shortname，在相应的项目配置里必须填对`比如在_config.yml`。实际上登录disqus管理后台之后在域名那里就能看到。
<span style="color: rgba(0,0,0,0)">本文由benben@blog.benbenrun.com原创，转载请注明来源。</span>

###### Step 2

![](/img/post/2019-06-22-2.png)

在Disqus的站点配置`Advanced=>Trusted Domains`里填上自己的域名。

###### Step 3

将修改Push到远程仓库后等几秒钟，刷新博客页面。