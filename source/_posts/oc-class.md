---
layout: post
title: OC追根溯源之Class
date: 2017-11-12 12:22:55
comments: true
tags:
	- iOS
  - OC
  - Class
categories: "iOS"
---

前两天碰到一面试题：
``` mm
//分辨下面res的是非
BOOL res1 = [[NSObject class] isKindOfClass:[NSObject class]];
BOOL res2 = [[NSObject class] isMemberOfClass:[NSObject class]];
BOOL res3 = [[Sark class] isKindOfClass:[Sark class]];
BOOL res4 = [[Sark class] isMemberOfClass:[Sark class]];
```

<!-- more -->

在这之前，我们先了解一下相关的定义：

__Class__

在`objc.h`中`Class`是这么定义的：
``` mm
/// An opaque type that represents an Objective-C class.
typedef struct objc_class *Class;
```

`objc_class`又是啥呢？在`runtime.h`中能看见：

``` mm
struct objc_class {
    Class isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class                                        OBJC2_UNAVAILABLE;
    const char *name                                         OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
```

__MetaClass__

上面能看到`Class`中有个`isa`参数，它是指向`Class`的`元类（MetaClass）`的指针。

注意到`isa`也是一个`Class`的结构体，也就是说`MetaClass`其本质也是一个`Class`。

>我们可以把Meta Class理解为 一个Class对象的Class。简单的说：
+ 当我们发送一个消息给一个NSObject对象时，这条消息会在对象的类的方法列表里查找
+ 当我们发送一个消息给一个类时，这条消息会在类的Meta Class的方法列表里查找

之后就有了下面的这张图：

![Class&MetaClass](/imgs/ocClass.jpg)

能看到：
+ 每个`Class`都有一个`isa`指针指向一个唯一的`MetaClass`
+ 每一个`MetaClass`的`isa`指针都指向最上层的`MetaClass`（图中的`NSObject`的`MetaClass`）
+ 最上层的`MetaClass`的`isa`指针指向自己，形成一个回路
+ 每一个`MetaClass`的`super class`指针指向它原本`Class`的 `Super Class`的`MetaClass`。
+ 最上层的`MetaClass`的 `Super Class`指向`NSObject` `Class`本身
+ 最上层的`NSObject` `Class`的`super class`指向 `nil`

接下来我们看看`objc`源码中(源码可到[这里下载](https://opensource.apple.com/source/objc4/))，在文件`Object.mm`内，上面相关方法的定义：

``` mm
+ (Class)class {  
    return self;  
}
```

``` mm
- (BOOL)isKindOf:aClass
{
	Class cls;
	for (cls = isa; cls; cls = cls->superclass)
		if (cls == (Class)aClass)
			return YES;
	return NO;
}
```

``` mm
- (BOOL)isMemberOf:aClass
{
	return isa == (Class)aClass;
}
```

那么，`BOOL res1 = [[NSObject class] isKindOfClass:[NSObject class]];`就可以这样对应起来理解了：

`[NSObject class]`拿到的是`self`，类方法的`return self`,即上图紫色的`NSObject(Class)`。

根据上面`- (BOOL)isKindOf:aClass`的实现，`for`循环首先拿到的`cls = isa`,也就是`cls`为`NSObject(Class)`的`MetaClass`。显然这时候`cls == (Class)aClass`不成立。

随后，`cls = cls->superclass`，即`cls`变成了`NSObject(Class)`的`MetaClass`的`superclass`,根据图示可以，`superclass`指向的是`NSObject(Class)`，至此`cls == (Class)aClass`成立，返回`YES`。

同样的道理，分析`BOOL res3 = [[Sark class] isKindOfClass:[Sark class]];`:

`[Sark class]`拿到的是`Sark(Class)`,而`- (BOOL)isKindOf:aClass`中`cls`拿到的值依次是`Sark Meta Class`->`NSObject Meta Class`->`NSObject Class` -> `nil`,没有相等的，`return NO`。

剩下两个，根据`- (BOOL)isMemberOf:aClass`的实现，很容易得出结论会`return NO`。
