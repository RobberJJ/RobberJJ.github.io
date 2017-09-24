---
layout: post
title: The document  could not be autosaved. The file doesn’t exist.
date: 2017-09-24 09:32:22
comments: true
tags:
	- iOS
  - autosaved
  - exist
categories: "iOS"
---
有时候莫名其妙的`pod update`之后(可能是拖拽文件到pod子项目导致的，具体忘了，出现问题的时候已经忘了之前干啥了。。。囧~)，然后某一个或者几个文件就不能编辑保存了，编译不过，如下：
>The document  could not be autosaved. The file doesn’t exist.

![fileNotExist](/imgs/fileNotExist.jpg)

<!-- more -->

而且，这个文件并没有路径显示：

![pathError](/imgs/pathError.jpg)

从左侧栏右键`Show in Finder`，之后可以看到该文件在项目根目录下面的`ProjectRootPath/Pods/Headers/Public/IMSDK/QCIMChatRoomVC.h`

各种尝试不好使啊，重新pod也不管用，最终将子项目的该文件覆盖copy到上面的pods路径(`ProjectRootPath/Pods/Headers/Public/IMSDK/QCIMChatRoomVC.h`)下面，好使了。

以上，记一笔，出现好几次了，每次都是莫名其妙的出现了，然后莫名其妙的好了。。。
