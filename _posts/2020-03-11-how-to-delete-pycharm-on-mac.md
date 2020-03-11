---
layout: post
title: "如何彻底卸载PyCharm"
subtitle: "How to delete PyCharm on mac"
date: 2020-03-11 13:00:00 +0930
author: Benben
header-img: img/about-bg.jpg
catalog: true
permalink: /:year/:month/how-to-delete-pycharm-on-mac/
tags:
    - 其它
---

#### 前置

- 出于某些不可描述的原因，我们可能会偶尔需要彻底删除PyCharm，包括其它一些Jetbrains的IDE。

#### 操作步骤

<pre>
cd ~/Library/Preferences/           
rm -rf PyCharm2018.3/
</pre>         

<pre>
cd ~/Library/Logs
rm -rf PyCharm2018.3/
</pre>

<pre>
cd ~/Library/Application\ Support/
rm -rf PyCharm2018.3/
</pre>

<pre>
cd ~/Library/Caches/
rm -rf PyCharm2018.3/
</pre>

<span style="color: rgba(0,0,0,0);font-size: 6px">本文由Benben[blog.benbenrun.com]原创，转载请注明来源。</span>
