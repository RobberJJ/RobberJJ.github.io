---
layout: post
title: 一行命令搞定node.js升级
comments: true
tags:
	- 常用命令
	- node.js update
categories: "随笔"
---

node有一个模块叫n，是专门用来管理node.js的版本的。

<!-- more -->
首先安装n模块：

``` bath
    npm install -g n
```

第二步：
升级node.js到最新稳定版

``` bath
    n stable
```

完工！是不是很简单？！
n后面也可以跟随版本号比如：

``` bath
n v0.10.26
或
n 0.10.26
```

就这么简单...
