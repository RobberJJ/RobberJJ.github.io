---
title: High Sierra bad interpreter
date: 2017-10-28 20:22:11
layout: post
comments: true
tags:
	- cocoapods
  - iOS
categories: "iOS"
---
升级到mac OS High Sierra之后，pod失败，错误如下：

>-bash: /usr/local/bin/pod: /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby: bad interpreter: No such file or directory

解决方案：

```
sudo gem update --system
sudo gem install -n /usr/local/bin cocoapods
```
