---
title: Swift Tips - 访问控制(private & fileprivate & internal & public & open)
date: 2018-11-29 09:34:30
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---
在 Swift 语言中，访问修饰符有五种，分别为 __private__，__fileprivate__，__internal__，__public__ 和 __open__。

> 其中 __fileprivate__ 和 __open__ 是 Swift 3 新添加的。由于过去 Swift 对于访问权限的控制，不是基于类的，而是基于文件的。这样会有问题，所以 Swift 3 新增了两个修饰符对原来的 __private__、__public__ 进行细分。

<!-- more -->

下面分别说说各种修饰符的范围&区别：
- __private__（_Class级别_）
_private_ 访问级别所修饰的 __属性__ 或者 __方法__ 只能在 __当前类__ 里访问。
__注意__：Swift4 中，__extension__ 里也可以访问 _private_ 的属性。


- __fileprivate__（_File级别_）
_fileprivate_ 访问级别所修饰的 __属性__ 或者 __方法__ 在 __当前的 Swift 源文件__ 里可以访问。

- __internal__（_Module级别，Default_，internal修饰符可写可不写）
  - _internal_ 访问级别所修饰的 __属性__ 或 __方法__ 在源代码所在的 __整个模块__ 都可以访问。
  - 如果是框架或者库代码，则在整个 __框架内部__ 都可以访问，框架由外部代码所引用时，则不可以访问。
  - 如果是 App 代码，也是在 __整个 App 代码__，也是在 __整个 App 内部__ 可以访问。


- __public__（_开放级别，但是有些限制_）
可以被 __任何人__ 访问。
__但__，其他 module 中不可以被 __override__ & __继承__ ，而在 __module__ 内可以被 __override__ & __继承__ 。

- __open__（_开放级别，无限制_）
可以被 __任何人__ 使用，包括 __override__ & __继承__ 。

__总结__，5种修饰符访问权限排序如下：

*__open__ > __public__ > __interal__ > __fileprivate__ > __private__*
