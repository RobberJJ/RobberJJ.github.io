---
title: Compile warning:Embedded binary's NSExtensionActivationRule is TRUEPREDICATE
date: 2017-09-24 09:19:58
comments: true
tags: ["mac","osx","mac os","NSExtensionActivationRule","TRUEPREDICATE"]
categories: ["Mac"]
anthor: "Ro"
---
上传AppStore的时候出现如下BUG：

>warning: Embedded binary's NSExtensionActivationRule is TRUEPREDICATE. Before you submit your containing app to the App Store, be sure to replace all uses of TRUEPREDICATE with specific predicate statements or NSExtensionActivationRule keys. If any extensions in your containing app include TRUEPREDICATE, the app will be rejected.

<!-- more -->

ShareExtnesion的plist文件中 NSExtensionActivationRule属性值为TRUEPREDICATE。

官方文档中提到：
>During development only, you can use the TRUEPREDICATE constant (which always evaluates to true) as a stub predicate statement, to test your code path before you implement your predicate statement.

>注意: development only,所以上架的时候不能用。

可以参考下图修改

![NSExtension](/imgs/NSExtension.png)

NSExtensionActivationRule的属性值可以参考[这里](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html#//apple_ref/doc/uid/TP40014214-CH21-SW12)
