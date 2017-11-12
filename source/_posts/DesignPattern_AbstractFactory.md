---
layout: post
title: 设计模式--抽象工厂
date: 2017-07-08 14:55:06
comments: true
tags:
	- 设计模式
categories: "iOS"
thumbnail: /imgs/pattern.png
---

### 抽象工厂模式(abstract factory)
>抽象工厂模式最初的定义出现于《设计模式》(Addison-Wesley,1994)

>__抽象工厂模式：提供一个创建一系列相关或相互依赖对象的接口，而无需指定他们具体的类。__

<!-- more -->

抽象工厂模式的类图如下：
![抽象工厂类图](/imgs/abstractFactory.png)

在软件设计中，如果客户端想手工创建一个类的对象，那么客户端需要首先知道这个类的细节。更糟的情况是，一组相关的对象可以在运行时按照不同的标准创建得不一样，此时客户端就需要知道全部细节才能创建他们。这种情况就可以用抽象工厂模式来解决这个问题。

抽象工厂提供一个固定的接口，用于创建一系列有关联或相互依存的对象，而不必指定其具体类或创建细节。客户端与从工厂得到的具体对象之间没有耦合。

Client只知道AbstractFactory和AbstractProduct。每个工厂类中，结构与实际操作的细节按黑箱对待。甚至产品也不知道谁将负责创建他们。只有具体工厂知道为客户端创建什么、如何创建。这个模式有趣的一点是，很多时候他都是用哪个工厂方法模式来实现。工厂方法吧实际的创建过程推迟到重载它的子类中。在类图中，方法`createProductA`和`createProductB`是工厂方法。最初的抽象方法什么也不创建。这种抽象非常通用，广泛用于任何需要抽象创建过程的场合。

抽象模式常与原型模式、单例模式、享元模式等其他设计模式一起使用。

###### 抽象工厂模式与工厂方法模式

| 抽象工厂模式 | 工厂方法模式 |
|:---:|:---:|
|通过对象组合创建抽象产品|通过类继承创建抽象产品|
|创建多系列产品|创建一种产品|
|必须修改父类才能支持新的产品|子类化创建者并重载工厂方法以创建新产品||

>__软件设计黄金法则：__ 变动需要抽象

###### 在Cocoa Touch框架中使用抽象工厂

抽象工厂模式常见于Cocoa Touch框架。有很多基础类采用了这一模式。特别常见的一个就是`NSNumber`。创建`NSNumber`实例的方式完全符合抽象工厂模式。

创建Cocoa Touch对象有两种方式：使用先`alloc`再`init`的方法(两步创建过程)，或者使用类中的`+className...`方法。在Cocoa Touch的基础框架中，`NSNumber`类有很多类方法用于创建各种类型的`NSNumber`对象，像下面这样的：
``` mm
NSNumber * boolNumber = [NSNumber numberWithBool:YES];
NSNumber * charNumber = [NSNumber numbberWithChar:'a'];
NSNumber * intNumber = [NSNumber numberWithInt:1];
NSNumber * floatNumber = [NSNumber numberWithFloat:1.0];
NSNumber * doubleNumber = [NSNumber numberWithDouble:1.0];
```

每个返回的对象属于代表最初输入值的不同私有子类。可以像这样列出他们的描述：

``` mm
NSLog(@"%@",[[boolNumber class] description]);
NSLog(@"%@",[[charNumber class] description]);
NSLog(@"%@",[[intNumber class] description]);
NSLog(@"%@",[[floatNumber class] description]);
NSLog(@"%@",[[doubleNumber class] description]);
```
将看到控制台输出：

``` mm
NSCFBollean
NSCFNumber
NSCFNumber
NSCFNumber
NSCFNumber
```

除了`boolNumber`的实际类型是`NSCFBoolean`以外，大多数实际类为`NSCFNumber`类型。尽管这些`+className...`类工厂方法返回`NSNumber`具体子类的实例，但是返回的实例确实支持`NSNumber`的公有接口。

虽然它们属于`NSNumber`的不同具体子类，但是其行为由抽象超类`NSNumber`定义，而且是公有的。若执行以下代码段，就会明白我的意思。

``` mm
NSLog(@"%@",[boolNumber intValue]);
NSLog(@"%@",[charNumber boolValue] ? @"YES" : @"NO");
```
将看到控制台输出：

``` mm
1
YES
```

`boolNumber` 内部保持布尔值`YES`，但是仍然实现了公有`intValue`方法，返回反映其内部布尔值的适当整数值。`charNumber`也是如此，它重载了`boolValue`方法，返回反映其内部布尔值的适当布尔值。

接受不同类型的参数并返回`NSNumber`实例的类方法是类工厂方法(工厂方法模式)。`NSNumber`的类工厂方法生产各种“数工厂”。`numberWithBool:`创建`NSCFBoolean`工厂的实例，而`numberWithInt:`创建`NSCFNumber`的实例。`NSNumber`中的类工厂方法定义了决定实例化何种私有具体子类(比如，`NSCFBoolean`或`NSCFNumber`)的默认行为。这一版本的工厂方法是传统工厂方法的一个变体，虽然它达成了返回抽象产品的目的，此处的抽象产品为作为工厂的具体`NSNumber`子类。`NSNumber`是抽象工厂实现的一个例子。基础框架中抽象工厂的此种特点被称为“类簇”(Class Cluster)。

类簇是基础框架中一种常见的设计模式，基于抽象工厂模式的思想，它将若干相关的私有具体工厂子类集合到一个公有的抽象超类之下。类簇是抽象工厂的一种形式。

创建抽象产品的工厂方法与创建抽象工厂的工厂方法之前有个不同点。显然，像`intValue`和`boolValue`这样的工厂方法，应该在具体工厂(`NSCFNumber`和`NSCFBoolean`)中重载以返回实际值(产品)。其它像`numberWithBool:`和`numberWithInt:`这样的工厂方法并不是为了返回产品，而是为了返回能返回产品的工厂，因此它们不应在具体工厂子类中重载。

其它实现为类簇的基础类有`NSData`,`NSArray`,`NSDictionary`和`NSString`。


__附上抽象工厂模式的Demo：[DesignPattern_AbstractFactory](https://github.com/RobberJJ/DesignPattern)__
