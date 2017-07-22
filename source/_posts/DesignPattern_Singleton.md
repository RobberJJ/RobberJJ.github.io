---
layout: post
title: 设计模式--单例模式
comments: true
tags:
	- 设计模式
categories: "iOS"
---

### 单例模式(singleton)
>单例模式最初的定义出现于《设计模式》(Addison-Wesley,1994)

>__单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点。__

<!-- more -->

单例模式几乎是设计模式的最简单形式了，这一模式的意图是使得类的一个对象成为系统中的唯一实例。要实现这一点，可以从客户端对其进行实例化开始。因此需要一种只允许生成对象类的唯一实例的机制，“阻止”所有想要生成对象的访问。我们可以用工厂方法来限制实例化的过程。这个方法应该为静态方法(类方法)，因为我们让类的实例去生成另一个唯一实例毫无意义。


单例模式的类图如下：
![单例模式类图](/imgs/singleton.png)

`static uniqueInstance`是`Singleton`的唯一实例，`static sharedInstance`将它返回给客户端。通常，`sharedInstance`会检查`uniqueInstance`是否已经被实例化，如果没有，它会生成一个实例后返回`uniqueInstance`。

###### 何时使用原型模式？
在以下情形，会考虑使用原型模式：
1. 类只有一个实例，而且必须从一个为人熟知的访问点对其进行访问，比如工厂方法；
2. 这个唯一实例只能通过子类化进行扩展，而且扩展的对象不会破坏客户端代码。

###### 使用Cocoa Touch框架中使用单例模式

Cocoa Touch框架中使用了大量的单例类。下面讨论其中的几个：

_1.UIApplication类_

框架中使用最常用的一个类是`UIApplication`类。它提供了一个控制并协调iOS应用程序的集中点。

每个应用程序有且仅有一个`UIApplication`的实例。它由`UIApplicationMain`函数在应用程序启动时创建为单例对象。之后，对同一`UIApplication`实例可以通过其sharedApplication类方法进行访问。

`UIApplication`对象为应用程序处理许多内存管理任务(housekepping task)，包括传入事件的最初路由，以及为`UIControl`分发动作消息给核实的目标对象。它还维护应用程序中的所有`UIWindow`对象的列表。。应用程序对象总是被分配一个`UIApplicationDelegate`对象，应用程序将把重要的运行时状态通知给它，比如iOS应用程序中的应用程序启动、内存不足警告、应用程序终止和后台进行执行。这让委托(`delegate`)有机会做出适当的响应。


_2.UIAcceleremeter类_

Cocoa Touch框架中另一个常用的单例是`UIAcceleremeter`.`UIAcceleremeter`让应用程序可以注册，以接收来自iOS设备内置的加速度计的加速度的相关数据。应用程序会收到三维空间中沿主轴的线性加速度变化，可以使用这一数据检测设备的当前方向和当前方向的瞬间变化。

`UIAcceleremeter`是单例，所以不能直接生成它的对象。而是应该调用其`sharedAcceleremeter`单例类方法以访问它的唯一实例。然后设定它的`updateInterval`属性，并用自己的`delegate`对象设定`delegate`属性，以接收来自单例实例的加速度数据。

_3.NSFileManager类_

在Mac OS X v10.5和iOS2.0之前，`NSFileManager`曾经是单例模式的“严格”实现。调用它的init方法是空操作，并且唯一实例可以通过`defaultManager`类方法创建和访问。然而其单例实现并不是线程安全的。现在推荐生成新的`NSFileManager`实例以保证线程安全。这一方式被认为是更灵活的单例实现，其中工厂方法总是返回同一实例，但是也可以分配并初始化另外的实例。


__附上单例模式的Demo：[DesignPattern_Singleton](https://github.com/RobberJJ/DesignSingleton)__
