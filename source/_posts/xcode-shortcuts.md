---
title: xcode快捷键你知多少？
date: 2017-07-30 10:00:58
layout: post
comments: true
tags:
	- xcode
  - 快捷键
categories: "iOS"
---

常言道，工欲善其事必先利其器。

了解xcode常用快捷键在coding中的使用，很容易摆脱鼠标的拖累，大大提高coding的效率。


### 快速搜索跳转文件

快捷键：__cmd + shift + o__

>这个快捷键的使用频率非常高，是摆脱鼠标的一大利器。

>注意：`o`是表示惊讶的`哦..😯`，不是考试得到的零蛋`0`，`o == open`。

<!-- more -->

### 快速跳至某行

快捷键：__cmd + l__

>常用这个快捷键，首先你要留意一下行号。

>注意：字母`l`并不是大写的`I`，也不是数字`1`，是大写字母`L`的小写，`l == line`。

### 代码块上下移动

快捷键：__option + cmd + [(or ])__

>很多时候，你需要将某几行代码交换位子顺序，或者某几行代码前移/后移几行，那么这个快捷键就有了用武之地了。

>注意：移动前选中你要移动的所有行。

### .h/.m文件之间快速切换

快捷键：__ctrl + cmd + ↑(or ↓)__

>`↑`(or `↓`)效果一样

### 前进/后退

快捷键：__ctrl + cmd + ←(or →)__

> `←`(or `→`)跟浏览器`上一页`or`下一页`类似。

### 显示当前类的方法列表，可搜索

快捷键：__ctrl + 6__

>上述快捷键之后，可方向键选择后回车，也可触摸板滑动选择。

>最重要的是显示列表之后，可直接输入相关字符搜索。

### 搜索、替换

文件类搜索：__cmd + f__

文件类替换：__cmd + option + f__

全局搜索：__cmd + shift + f__

全局替换：__cmd + shift + option + f__

>注意：全局搜索替换可以指定目录，也可以选择匹配格式，这个选择在搜索框与替换框之间。

>文件类与全局都可以在搜索框最前面的`🔍`位置点击切换搜索/替换。

### 打开Related Items列表

快捷键：__ctrl + 1__

>光标停留在相应的关键词中，此快捷键可查看父类，调用者，被调用者，includes，included by等诸多信息。

### 收起/展开代码块

快捷键：__option + cmd + ←(or →)__

### 在左侧目录中快速定位当前文件位置

快捷键：__cmd + shift + j__

### 快速切换左边导航栏

快捷键：__cmd + 1~8__

>注意：`cmd + 0`是‘显示/隐藏左侧栏’，`cmd + cmd + option + 0`是‘显示/隐藏右侧栏’。

### 全局修改局部变量

快捷键：__ctrl + cmd + e__

>当你想要将button换一个变量名的时候，这个快捷键绝对能爽死你，当然你也能用鼠标或者触摸板实现这个功能，选中变量名，移动光标会出现一个小箭头，点开会有快捷菜单，选择`Edit All in Scope`。

当然还有一些基础的快捷键在这里就不一一列举了，相信大家也都知道，比如，编译(`cmd + b`)，运行(`cmd + r`)，clean(`cmd + shift + k`)，快速移动光标(`cmd + 方向键`)，快速选择代码(`cmd + shift + 方向键`)等。

值得注意的是，除了上述快捷键之外，右键快捷菜单也是很有用处的，举个栗子：

光标置于类名中，右键(或者触摸板两手指头单击)显示快捷菜单，选择`Refactor -- Rename`，全局替换类名。

### XVim

最后，我想说的是，想要快捷键运用的炉火纯青，而且不会因为软件间系统间的各种差异出现很大的变化，vim！！

这个绝对是神器，xcode可以安装vim插件[XVim](https://github.com/XVimProject/XVim)。

__注意：__ xcode 8之后，需要先[安装证书](https://github.com/XVimProject/XVim/blob/master/INSTALL_Xcode8.md).

下载下来编译不过的话，是因为类`IDEPlaygroundEditor`没实现(IDEPlaygroundEditor+XVim.h中)，加上实现`@implementation`即可，或者直接删除报错的文件以及引用此文件头文件的地方。

XVim快捷键使用见[这里](https://github.com/XVimProject/XVim/blob/master/Documents/Users/FeatureList.md)。

放出一张vim命令图解：

![vim命令图解](/imgs/vimCommod.png)
