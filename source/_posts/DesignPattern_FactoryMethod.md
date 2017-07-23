---
layout: post
title: 设计模式--工厂方法
comments: true
tags:
	- 设计模式
categories: "iOS"
thumbnail: /imgs/pattern.png
---

### 工厂方法模式(factory method)
>工厂方法模式最初的定义出现于《设计模式》(Addison-Wesley,1994)

>__工厂方法模式：定义创建对象的接口，让子类决定实例化哪一个类。工厂方法使得一个类的实例化延迟到其子类。__

<!-- more -->

工厂方法模式的类图如下：
![工厂方法类图](/imgs/factoryMethod.png)

工厂方法也成为虚构造器(virtual constructor)。它适用于这种情况：一个类无法预期需要生成哪个类的对象，想让其子类来指定所生成的对象。

抽象的Product(产品)定义了工厂方法创建的对象的接口。ConcreteProduct实现了Product接口。Creator定义了返回Product对象的工厂方法。它也可以为工厂方法定义一个默认实现，返回默认ConcreteProduct对象。Creator的其他操作可以调用此工厂方法创建Product对象。ConcreteProduct是creator的子类。它重载了工厂方法，以返回ConcreteProduct的实例。

工厂方法的最初定义好像专注于让子类决定创建什么对象。有一种变体，抽象类使用工厂方法创建其私有子类或者任何其他类的对象(抽象工厂模式)。(接下来的文章会详细描述抽象工厂模式)

###### 何时使用工厂方法模式？
在以下情形，你自然会想到使用工厂方法模式：
1. 编译时无法确定预期要创建的对象的类；
2. 类想让其子类决定是在运行时创建什么；
3. 类有若干辅助类为其子类，而你想将返回哪个子类这一信息局部化。

###### 在Cocoa Touch框架中应用工厂方法

工厂方法在Cocoa Touch框架中几乎随处可见。大家知道常见的两步对象创建法`[[SomeClass alloc] init]`。有时，我们会注意到有一些“便利”方法返回类的实例。例如，`NSNumber`有很多`numberWith*`方法，其中有两个是`numberWithBool:`和`numbberWithChar:`。它们是类方法，也就是说，我们向`NSNumber`发送`[NSNumber numberWithBool:bool]`与`[NSNumber numbberWithChar:char]`,以获得与传入参数同类型的各种`NSNumber`实例。与如何创建`NSNumber`的具体实例有关的所有细节，都是 由`NSNumber`的类工厂方法负责。

`[NSNumber numberWithBool:bool]`的情况是，方法接受值`bool`，并把`NSNumber`的内部子类(实际上是`NSCFBoolean`类)的一个实例初始化，让他能够反映传入的值`bool`.

__附上工厂方法模式的Demo：[DesignPattern_FactoryMethod](https://github.com/RobberJJ/DesignPattern)__
