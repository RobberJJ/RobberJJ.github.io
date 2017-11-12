---
title: This app was unable to be installed.
date: 2017-10-28 20:31:36
layout: post
comments: true
tags:
	- xcode
  - iOS
categories: "iOS"
---
今天升级XCode 9.1 beta，编译项目正常，模拟器运行出现以下错误弹框：

![UnableInstalled](/imgs/UnableInstalled.jpg)

<!-- more -->

APP无法安装到模拟器上，也看不见任何提示，由于mac中存在多个XCode 版本，尝试下面命令：

> sudo xcode-select -s /Applications/Xcode-beta.app

并不好使。

这种问题看不见任何其他的问题提示，有点无从查起的赶脚。

`stackoverflow`上找到下面的方法：

```
xcrun simctl spawn booted log show

//或者可以标出这个错误提示相关的，很容易看到相关的错误提示
xcrun simctl spawn booted log show | grep "This app was unable to be installed.
```

然后，基本就能定位问题了，我这里出现的问题是：
![reason](/imgs/UnableInstalled2.jpg)

Info.plist文件中缺少`CFBundleExecutable`，加上即可。
