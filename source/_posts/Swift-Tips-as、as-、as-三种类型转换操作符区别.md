---
title: Swift Tips - as、as!、as?三种类型转换操作符区别
date: 2018-11-27 15:51:51
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

这里整理总结下 as、as!、as? 这三种类型转换操作符的异同，以及各自的使用场景。


#### as使用场合
- 从派生类转换为基类，向上转型（upcasts）

```swift
class Animal {}

class Cat: Animal {}

let cat = Cat()

let animal = cat as Animal
```

- 消除二义性，数值类型转换

```swift
let num1 = 42 as CGFloat
let num2 = 42 as Int
let num3 = 42.5 as Int
let num4 = (42 / 2) as Double
```


<!-- more -->



- switch 语句中进行模式匹配
  如果不知道一个对象是什么类型，你可以通过switch语法检测它的类型，并且尝试在不同的情况下使用对应的类型进行相应的处理。

```swift
switch animal {
case let cat as Cat:
    print("如果是Cat类型对象，则做相应处理")
case let dog as Dog:
    print("如果是Dog类型对象，则做相应处理")
default: break
}
```
#### as!使用场合
向下转型（Downcasting）时使用。由于是强制类型转换，如果转换失败会报 runtime 运行错误。
```swift
class Animal {}
class Cat: Animal {}
let animal :Animal  = Cat()
let cat = animal as! Cat
```

#### as?使用场合
as? 和 as! 操作符的转换规则完全一样。但 as? 如果转换不成功的时候便会返回一个 nil 对象。成功的话返回可选类型值（optional），需要我们拆包使用。
由于 as? 在转换失败的时候也不会出现错误，所以对于如果能确保100%会成功的转换则可使用 as!，否则使用 as?
```swift
let animal:Animal = Cat()
 
if let cat = animal as? Cat{
    print("cat is not nil")
} else {
    print("cat is nil")
}
```