---
title: Swift Tips - Defer关键字
date: 2018-12-04 10:47:44
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

前面有说到，在 *swift 2.0*  引入了 `guard` 关键字，可以让代码编写更流畅。它的优雅简洁而功能强大确实给了我们极大的方便。具体可以参见 [这里](https://robberjj.github.io/2018/11/30/Swift-Tips-if-let-var-guard-let-var-%E5%85%A8%E8%A7%A3%E6%9E%90/) 。

而，跟 `guard` 一同引入的还有一个关键字 —— `defer` 。

#### defer

一句话总结 `defer` 就是：让执行推迟。



<!-- more -->



我们知道，在错误处理方面，`guard` 和新的 `throw` 语法之间，Swift 鼓励用尽早返回错误（这也是我最喜欢的方式）来代替嵌套 if 的处理方式。尽早返回让处理更清晰了，但是已经被初始化（可能也正在被使用）的资源必须在返回前被处理干净。

`defer` 关键字为此提供了安全又简单的处理方式：声明一个 `block`，当前代码执行的闭包退出时会执行该 `block` 。

具体我们来看下面这个栗子：

#### 举个栗子

```swift
import Darwin

func currentHostName() -> String {
    let capacity = Int(NI_MAXHOST)
    let buffer = UnsafeMutablePointer<Int8>.allocate(capacity: capacity)

    //调用 gethostname(2) 的函数，用来返回当前系统的主机名称
    guard gethostname(buffer, capacity) == 0 else {
        buffer.deallocate()
        return "localhost"
    }

    let hostname = String(cString: buffer)
    buffer.deallocate()

    return hostname
}
```

这里有一个在最开始就创建的 `UnsafeMutablePointer<UInt8>` 用于存储目标数据，但是我**既要**在错误发生后销毁它，**又要**在正常流程下不再使用它时对其进行销毁。

这种设计很容易导致错误，而且不停地在做重复工作。

通过使用 `defer` 语句，我们可以排除潜在的错误并且简化代码：

```swift
func currentHostName() -> String {
    let capacity = Int(NI_MAXHOST)
    let buffer = UnsafeMutablePointer<Int8>.allocate(capacity: capacity)
    
    defer { buffer.deallocate() }

    guard gethostname(buffer, capacity) == 0 else {
        return "localhost"
    }

    return String(cString: buffer)
}
```

尽管 `defer` 紧接着出现在 `allocate(capacity:)` 调用之后，但它要等到当前区域结束时才会被执行。多亏了 `defer`，`buffer` 才能无论在哪个点退出函数都可以被释放。

考虑在任何需要配对调用的 API 上都使用 `defer`，比如 `allocate(capacity:)` / `deallocate()`、`wait()` / `signal()` 和 `open()` / `close()`。这样的话，你就可以消除一种程序员易犯的错误。

#### 多个 `defer`

如果在同一个作用域内使用多个 `defer` 语句，它们会根据出现顺序反过来执行——像栈一样。这个反序是非常重要的细节，保证了被延迟的代码块创建时作用域内存在的东西，在代码块执行同样存在。

举个例子，执行这段代码会得到下面的输出：

```swift
func procrastinate() {
    defer { print("wash the dishes") }
    defer { print("take out the recycling") }
    defer { print("clean the refrigerator") }

    print("play videogames")
}
/*  输出结果：
	play videogames
	clean the refrigerator
	take out the recycling
	wash the dishes
*/
```

那么问题来了，如果你像这样嵌套 `defer` 语句，会怎么样？

```swift
defer { defer { print("clean the gutter") } }
```

哈哈，当然你可以亲自试一下。

#### `defer` 中的变量

如果在 `defer` 语句中引用了一个变量，执行时会用到变量最终的值。换句话说：`defer` 代码块不会捕获变量当前的值。

如果你运行这段代码，你会得到下面的输出：

```swift
func flipFlop() {
    var position = "It's pronounced /ɡɪf/"
    defer { print(position) }

    position = "It's pronounced /dʒɪf/"
    defer { print(position) }
}
/*  输出结果：
	It's pronounced /dʒɪf/
	It's pronounced /dʒɪf/
*/
```

#### `defer` 跳不出作用域

需要注意的是， `defer` 代码块无法跳出它所在的作用域。因此如你尝试调用一个会 throw 的方法，抛出的错误就无法传递到其周围的上下文。

```swift
func burnAfterReading(file url: URL) throws {
    defer { try FileManager.default.removeItem(at: url) }
    // 🛑 Errors not handled

    let string = try String(contentsOf: url)
}
```

作为替代，你可以使用 `try?` 来无视掉错误，或者直接将语句移出 `defer` 代码块，将其放到函数的最后，正常的执行。



#### `defer` 并不是总是会执行

看看下面这个栗子：

```swift
func foo() {
  guard false else { return }
  defer {
    print("finally")
  }
}
```

显然，上面永远不会执行到 `defer` 这一句，这时候就不会被执行。

这个故事告诉我们，至少要执行到 `defer` 这一行，它才保证后面会触发。

#### `defer` 的坏处

虽然 `defer` 像一个语法糖一样，但也要小心使用避免形成容易误解、难以阅读的代码。在某些情况下你可能会尝试用 `defer` 来对某些值返回之前做最后一步的处理，例如说在后置运算符 `++` 的实现中：

```swift
postfix func ++(inout x: Int) -> Int {
    let current = x
    x += 1
    return current
}
```

在这种情况下，可以用 `defer` 来进行一个很另类的操作。如果能在 defer 中处理的话为什么要创建临时变量呢？

```swift
postfix func ++(inout x: Int) -> Int {
    defer { x += 1 }
    return x
}
```

这种写法确实聪明，但这样却颠倒了函数的逻辑顺序，极大降低了代码的可读性。应该严格遵循 `defer`在整个程序最后运行以释放已申请资源的原则，其他任何使用方法都可能让代码乱成一团。

一脸懵逼是不是？



**写在最后：**



「聪明的程序员明白自己的局限性」，我们必须权衡每种语言特性的好处和其成本。

类似于 `guard` 的新特性能让代码流程上更线性，可读性更高，就应该尽可能使用。

同样 `defer` 也解决了重要的问题，但是会强迫我们一定要找到它声明的地方才能追踪到其销毁的方法，因为声明方法很容易被滚动出了视野之外，所以应该尽可能遵循它出现的初衷尽可能少地使用，避免造成混淆和晦涩。