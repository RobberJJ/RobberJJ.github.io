---
layout: post
title: 设计模式--原型模式
date: 2017-07-02 15:16:06
comments: true
tags:
	- 设计模式
categories: "iOS"
thumbnail: /imgs/pattern.png
---

### 原型模式(prototype)
>原型模式最初的定义出现于《设计模式》(Addison-Wesley,1994)

>__原型模式：使用原型实例指定创建对象的种类。并通过复制这个原型创建新的对象。__

<!-- more -->

原型模式的类图如下：
![原型模式类图](/imgs/prototype.png)

原型模式是一种非常简单的设计模式。客户端知道抽象Prototype类，在运行时，抽象Prototype子类的任何对象都可以按客户端的意愿被复制。因此，无需手工创建就可以制造同意类型的多个实例。

Prototype声明了复制自身的接口。作为Prototype的子类，ConcretePrototype实现了Concrete复制自身的clone操作。

这里，客户端通过请求原型复制其自身，创建了一个新的对象。

###### 何时使用原型模式？
在以下情形，会考虑使用原型模式：
1. 需要创建的对象独立于其类型与创建的方式；
2. 实例化的类是在运行时决定的；
3. 不想要与产品层次相对应的工厂层次；
4. 不同类的实例间的差异仅是状态的若干组合，这样复制相应数量的原型比手工实例化更加方便；
5. 类不容易创建，比如每个组件可把其他组件作为子节点的组合对象，复制已有的组合对象并对副本进行修改会更加容易。

###### 使用Cocoa Touch框架中的对象复制
>__值得注意的是：__浅复制与深复制的区别

>__浅复制__只是复制了对象的指针，而同时指向相同的对象资源，也就是说只是复制了栈里面的内容，而同时指向相同的堆空间。改变其对象值得时候，复制对象间会相互影响。

>__深复制__是同时复制对象的指针跟对象本身，也就是同时复制了堆栈空间的内容。

Cocoa Touch框架为`NSObject`的派生类提供了实现深复制的协议。`NSObject`的子类需要实现`NSCopying`协议及其方法`-(id)copyWithZone:(NSZone *)zone`。`NSObject`有一个实例方法叫`-(id)copy`。默认的`copy`方法调用`[self copyWithZone:nil]`。对于采纳了`NSCopying`协议的子类，需要实现这个方法。否则将引发异常。iOS中，这个方法保持新的副本对象，然后将其返回。

__附上原型模式的Demo：[DesignPattern_Prototype](https://github.com/RobberJJ/DesignPattern)__
