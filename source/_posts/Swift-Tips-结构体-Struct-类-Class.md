---
title: Swift Tips - 结构体(Struct) & 类(Class)
date: 2018-12-29 10:37:15
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

事实上，__结构体__ & __类__ 我们并不是很陌生，在我们各种熟悉语言中均是存在，比如，C、C++、OC等。

前面我们已经知道 __Swift__ 将 __枚举__ 着实丰富了很多，具体可以参见__[这篇](https://robberjj.github.io/2018/12/19/Swift-Tips-%E6%9E%9A%E4%B8%BE-Enumerations/)__文章。

而对于 __结构体__ , **Swift** 也是对其宠爱有加。

有句话是这么说的来着：**Swift** 中，能用 **结构体** 就不要用 **类** ，除非需要 **类** 的那些特有的特性。

那么， **Swift** 中怎么定义 **结构体** 跟 **类** 呢？

<!-- more -->

#### 什么是 *结构体* & *类* ？

**结构体 & 类** 是一种用于构建程序代码块的类灵活的稳定的结构。可以使用与*定义常量*，*变量* 和 *函数* 相同的语法来定义 **属性** 和 **方法** ，以向 **结构体** 和 **类** 添加功能。（来自官网的翻译）

诚如你所见，看似还是这么熟悉，却又有一点不寻常，是的，我们可以向 **结构体** 中添加 **属性&方法** ，就跟向 **类** 里面添加一样一样的！

所以，在 **Swift** 中 **结构体** 已经扩展到跟 **类** 灰常神似的地步。

那么问题来了，这两者到底还有什么区别呢？



#### 异同 In *结构体* & *类* ？

**Swift** 中 **类** 和 **结构体** 的共同处在于：

- 定义属性用于存储值
- 定义方法用于提供功能
- 定义下标脚本用于访问值
- 定义构造器用于生成初始化值
- 通过扩展以增加默认实现的功能
- 实现协议以提供某种标准功能

**Swift** 中 **类** 和 **结构体** 的不同处在于：

- 结构体不具有继承性
- 结构体不具备运行时强制类型转换
- 结构体不具备使用析构器的能力
- 结构体不具备使用引用计数的能力

综上所述，如果我们需要构建的代码块，只是需要 **共同处** 就能满足，那么我们就选择 **结构体** ；要是还需要 **不同处** 的特有功能，再选择 **类** 。



#### 语法

**枚举**、**结构体**、**类** 的语法非常类似，分别使用 `enum`、`struct`、`class`关键字来定义：

```swift
struct SomeStructure {
    // structure definition goes here
}
class SomeClass {
    // class definition goes here
}
```

举个栗子：

```swift
//用于描述基于像素的显示分辨率。此结构有两个存储的属性，称为width和height。
struct Resolution {
    var width = 0
    var height = 0
}

//用于描述视频显示的特定视频模式。
class VideoMode {
    var resolution = Resolution()
    var interlaced = false
    var frameRate = 0.0
    var name: String?
}
```

可以像下面这样创建实例，以及访问：

```swift
let someResolution = Resolution()
let someVideoMode = VideoMode()

print("The width of someResolution is \(someResolution.width)")
// Prints "The width of someResolution is 0"

print("The width of someVideoMode is \(someVideoMode.resolution.width)")
// Prints "The width of someVideoMode is 0"

someVideoMode.resolution.width = 1280
print("The width of someVideoMode is now \(someVideoMode.resolution.width)")
// Prints "The width of someVideoMode is now 1280"
```

这里需要了解的是，所有 **结构体** 都有一个自动生成的*成员初始化程序*，可以使用它初始化新结构实例的成员属性：

```swift
let vga = Resolution(width: 640, height: 480)
```

而，**类实例**不接收默认的成员初始值设定项。



其实，**枚举&结构体** 与 **类** 之间有一个很本质的区别在于：**枚举&结构体** 是***值类型***，而 **类** 是***引用类型***。



#### 枚举&结构体 是 值类型

首先，什么是 **值类型** ？

**值类型** 是一种最简单的类型，为什么这么说了？因为常见，比如各种语言里面的Int、Float、Double之流都是。

进一步可以理解为 **值类型** 是一种没有指针概念的类型。

那么问题来了，何为没有指针概念呢？

从存储上看可以这么理解：**值类型** 都是存放在 **栈** 中，而与之对应的 **引用类型** 是在 **栈** 中存储 **指针**，数据存在 **堆** 中，且栈中的指针指向对应的数据的堆地址。

值得一说的是，**Swift** 中出了 **枚举、结构体** 是值类型，甚至连 **String**，**Array** 以及 **Dictionary** 等都是值类型的。

为什么呢？因为  **String**，**Array** 以及 **Dictionary** 其实都是通过 **结构体** 实现的。

下面举个栗子：

```swift
//实例化
let hd = Resolution(width: 1920, height: 1080)
var cinema = hd
//修改 width
cinema.width = 2048

print("cinema is now \(cinema.width) pixels wide")
// Prints "cinema is now 2048 pixels wide"

print("hd is still \(hd.width) pixels wide")
// Prints "hd is still 1920 pixels wide"
```

当`cinema`给出当前值时`hd`，存储的*值*`hd`被复制到新`cinema`实例中。最终结果是两个完全独立的实例，它们包含相同的数值。但是，因为它们是单独的实例，所以设置宽度`cinema`to `2048`不会影响存储的宽度`hd`，内存情况如下图所示：

![内存示意图](https://upload-images.jianshu.io/upload_images/6080248-ad268e394e06061c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 类 是 引用类型

 **引用类型**，与值类型不同，***引用类型*** 在分配给变量或常量时或者传递给函数时其值不会被复制。而不是副本，使用对同一现有实例的引用。

其实就是所谓的 **浅拷贝** ：只复制指针，而不复制其值；

换一种说法就是，**浅拷贝** 只是复制栈内存，而指向同一块儿堆内存。

还是举个栗子直观的理解一下：

```swift
//实例化，VideoMode是上面定义的类，hd是上面实例化的实例
let tenEighty = VideoMode()
tenEighty.resolution = hd
tenEighty.interlaced = true
tenEighty.name = "1080i"
tenEighty.frameRate = 25.0
```

`tenEighty`分配一个名为的新常量`alsoTenEighty`，并`alsoTenEighty`修改帧速率：

```swift
let alsoTenEighty = tenEighty
alsoTenEighty.frameRate = 30.0

print("The frameRate property of tenEighty is now \(tenEighty.frameRate)")
// Prints "The frameRate property of tenEighty is now 30.0"
```

内存如下图所示：

![内存示意图](https://upload-images.jianshu.io/upload_images/6080248-7f45cb21c26260ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

需要注意的是，`tenEighty` 并将 `alsoTenEighty` 其声明为***常量***，而不是 **变量**。

但是，您仍然可以更改 `tenEighty.frameRate`，`alsoTenEighty.frameRate`，因为 `tenEighty` 和 `alsoTenEighty` 本身（栈中的指针）并未更改。`tenEighty` 并且 `alsoTenEighty` 他们自己不“存储” `VideoMode`实例 （ `VideoMode`实例存储在堆中）。



那么又一个来了，**类** 既然是引用类型，怎么判断两个实例是不是同一个呢？也就是说两个不同的指针是不是指向同一块堆内存呢？

**Swift** 提供了一组操作符来方便的解决这个问题：**`===`、`!==`**  。

看着就很好理解，举个栗子：

```swift
if tenEighty === alsoTenEighty {
    print("tenEighty and alsoTenEighty refer to the same VideoMode instance.")
}
// Prints "tenEighty and alsoTenEighty refer to the same VideoMode instance."
```



