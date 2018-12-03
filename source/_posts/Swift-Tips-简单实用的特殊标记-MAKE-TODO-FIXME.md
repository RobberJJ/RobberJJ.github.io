---
title: Swift Tips - 简单实用的特殊标记（MAKE & TODO & FIXME）
date: 2018-12-03 14:45:23
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

通常我们在代码编写过程中，为了更好的理解，亦或是为了其他的目的，而希望可以通过简单的方式，迅速的完成标记。

在我们熟悉的 `OC` 中我们有两种标记方式：

其中一种是 `#pragram mark - xxx` 方式。

<!-- more -->

而另一种是下面这种：

```swift
// TODO: 标示处有功能代码待编写，使用方法：// TODO:
// FIXME: 标示处代码需要修正，使用方法：// FIXME:
// !!!: 标示处代码需要注意，使用方法：// !!!:
// ???: 标示处代码有疑问，使用方法：// ???:
// MARK: 标记，和#pragma mark效果相同，使用方法：// MARK:
```

当然，我们也是可以加上分割线`-`：

```swift
// TODO: - 标示处有功能代码待编写，使用方法：// TODO:
// FIXME: - 标示处代码需要修正，使用方法：// FIXME:
// !!!: - 标示处代码需要注意，使用方法：// !!!:
// ???: - 标示处代码有疑问，使用方法：// ???:
// MARK: - 标记，和#pragma mark效果相同，使用方法：// MARK:

// MARK: -
```



在Xcode 10上展示可谓是由很好的颜值&清晰度：

![6944660C-17FF-4ADB-B5FF-D4AA694C2633.png](https://upload-images.jianshu.io/upload_images/6080248-bb1ae0741489ab84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



但是，在swift中，`#pragram mark - xxx` 的方式并不能被编译器识别，万幸的是另外一种中的 `MARK:` & `TODO:` & `FIXME:` 依旧还是可以使用的。（包括可以加上分割线 `-` ）

![41F87AD0-34E6-4B0E-8308-386D5B49CABC.png](https://upload-images.jianshu.io/upload_images/6080248-9708ac3a3d3728ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，根据在代码中标记的位置，可以清晰的显示出对应的层次。



啊哈！以上。

希望以上可以对你有些帮助。