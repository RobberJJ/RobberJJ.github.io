---
title: Swift Tips - if let/var & guard let/var 全解析
date: 2018-11-30 13:57:39
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

### 问题引出：

_swift_ 神奇的引入了一个类型——*__可选类型（optionals）__*。

但是，我们要是按照以往（比如OC）的套路在使用可选类型定义的常量的时候，又会让你崩溃抓狂。

因为 _swift_ 是类型安全的语言，所以可选类型的常量在 `if` 判断之后仍然需要 _解包(`!`)_ 。如下：

```swift
let name: String? = "老王"
let age: Int? = 10

if name != nil && age != nil {
    print(name! + String(age!))     // 输出:老王10
}
```

倘若，你不加上 `!` ，根本就不会让你编译通过，而，加上呢？又会如此的麻烦。难道优雅的 _swift_ 就没有解决办法吗？



<!-- more -->



答案，显然是有的—— *__`if let`__* 。



### `if let` 

 `if let` 的标准套路：

```swift
if let constantName = someOptional {
   //statements using 'constantName' 
} else {
// the value of someOptional is not set (or nil).
}
```

诚如上面的栗子，倘若通过 `if let` 加以实现，就不需要 _解包(`!`)_ 的操作了,甚至可以添加多个条件，`,` 隔开即可:

```swift
let name: String? = "老王"
let age: Int? = 10

// if let 连用,判断对象的值是否为'nil'
if let nameNew = name,
    let ageNew = age {
    
    // 进入分支后,nameNew 和 ageNew 一定有值
    print(nameNew + String(ageNew)) // 输出:老王10
}
```

__需要注意的是：__`if let` 转换的 _常量_ 的作用域只是在 `if let {}`中。



### `if var` 

`if var` 与 `if let` 的用法、语法都是基本相同的，唯一的区别是 `if var` 转换出来是 _变量_ ，并且，这个 _变量_  在其作用域 `{}` 内是可以修改其值的。如下：

```swift
let name: String? = "老王"
let age: Int? = 10

if var nameNew = name,
    let ageNew = age {
    // 'var'修饰,可以修改'nameNew'的值,'let'修改的不可以修改
    nameNew = "老李"
    print(nameNew + String(ageNew))     // 输出:老李10
}
```

虽然 `if var` 在其作用域 `{}` 内是可以修改其值的，但是问题是，更改的值出了作用域之后就失效了，所以通常在实际开发中，并没有太常用。

到这里，文章开始的问题已经被优雅的 `swift` 化解了，但是依旧不是很完美，正如上面提到的 `if let/var` 的缺点：__作用域太狭窄__ 。

所以， `swift` 当然不可能止步于此。

__良心推荐：__ *__`guard let/var`__*



### `guard let/var ` 

我们通常说 `guard` 是为了守护一定有值。套路如下：

```swift
guard let/var constantName = expression else { 
    return value
}
```

比如，上面的栗子我们就可以这么写了：

```swift
let name: String? = "老王"
let age: Int? = 10

guard let nameNew = name,
    let ageNew = age else {
        print("姓名 或 年龄 为nil")
        return
}
// 代码执行至此, nameNew 和 ageNew 一定有值
print(nameNew + String(ageNew))     // 输出:老王10
```

`guard` 就保证了它之后的代码相关值是肯定有的。

`guard` 仅在条件为假时运行，并且将通过诸如return，break，continue或thrown之类的控制转移语句退出代码块。它提供的这种 _提前退出_ 意味着更快的执行。

而且，语法上少了一层括号，更简洁一些，不是吗？

__更重要的是：__ `guard let/var` 弥补了 `if let/var` 的缺陷，可以在 `{}` 以外的地方继续使用甚至修改（`var`）。

```swift
let name: String? = "老王"
let age: Int? = 10

guard var nameNew = name,
    let ageNew = age else {
    // 'var'修饰,可以修改'nameNew'的值,'let'修改的不可以修改
    nameNew = "老李"
    print(nameNew + String(ageNew))     // 输出:老李10
}
print(nameNew + String(ageNew))     // 输出:老李10

nameNew = "老张"
print(nameNew + String(ageNew))     // 输出:老张10
```



搞定收工！

至此，有没有解决你的疑问了？评论告诉我。