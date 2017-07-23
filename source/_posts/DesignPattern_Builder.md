---
layout: post
title: 设计模式--生成器(建造者)
comments: true
tags:
	- 设计模式
categories: "iOS"
thumbnail: /imgs/pattern.png
---

### 生成器模式(Builder)
>生成器模式最初的定义出现于《设计模式》(Addison-Wesley,1994)

>__生成器模式：将一个复杂对象的构建与它的表现分离，使得同样的构建过程可以创建不同的表现。__

<!-- more -->

生成器模式的类图如下：
![生成器类图](/imgs/builder.png)

除了客户与其所要的产品，生成器模式还包含两个重要角色：Director(指导者)和Builder(生成器)。Builder知道究竟如何在缺少某些特定信息的情况下建造产品(什么)。Director知道Builder应该建造什么，以参数向其提供缺少的信息来建造特定产品。尽管Director知道Builder应该建造什么，这并不意味着Director知道具体Builder究竟是什么。他们的静态关系如上图中的类图所示。

Builder是一个抽象接口，声明了一个`buildPart`方法，该builder方法由`ConcreteBuilder`实现，以构造实际产品(Product)。`ConcreteBuilder`有个`getResult`方法，向客户端返回构造完毕的Product。Director定义了一个`construct`方法，命令Builder的实例去`builderPart`。Director与Builder形成了一种聚合关系。这意味着Builder是一个组成部分，与Director结合，以使整个模式运转，但同时，Director并不负责Builder的生存期。

###### 何时使用生成器模式？
在以下情形，你自然会想到使用工厂方法模式：
1. 需要创建设计各种部件的负责对象。创建对象的算法应该独立于部件的装配方式。
2. 构建过程需要以不同的方式(例如，部件或者表现的不同组合)构建对象。

###### 生成器与抽象工厂对比

|生成器|抽象工厂|
|:---:|:---:|
|构建负责对象|构建简单或者复杂对象|
|以多个步骤构建对象|以单一步骤构建对象|
|以多种方式构建对象|以单一方式构建对象|
|在构建过程的最后一步返回产品|立刻返回产品|
|专注一个特定产品|强调一套产品||



__附生成器模式的Demo：[DesignPattern_Builder](https://github.com/RobberJJ/DesignPattern)__
