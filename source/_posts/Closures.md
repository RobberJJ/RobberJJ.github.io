---
layout: post
title: Swift闭包--简不简洁？！优不优雅？！
comments: true
tags:
	- 闭包
	- swift
	- iOS
categories: "swift"
thumbnail: /imgs/swift.png
---
<h3>闭包(Closures)</h3>

闭包是自包含的函数代码块，可以在代码中被传递和使用。
>Closures are self-contained blocks of functionality that can be passed around and used in your code.
--摘自[官方文档](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-ID94)

相信大家都熟悉C 和 Objective-C 中的代码块(blocks)或者其他一些编程语言中的匿名函数，Swift中的闭包与之相类似。

<!-- more -->

闭包可以捕获和存储其所在上下文中任意常量和变量的引用。被称为包裹常量和变量。

>__注意:__
1. 如果你不熟悉捕获(capturing)这个概念也不用担心，在后面文章会对其进行详细介绍([我要尾随](https://robberjj.github.io))。
2. Swift会为你管理在捕获过程中涉及到的所有内存操作。(PS:类似于Objective-C,不会让你过多的经历花费在内存管理上，而应该更过的考虑其他更重要的事情，本该如此!)

闭包一般采取以下三种形式：

* 全局函数是一个有名字但不会捕获任何值的闭包
* 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包
* 闭包表达式是一个利用轻量级语法所写的可以捕获其上下文中变量或常量值的匿名闭包
<br>

Swift简洁优雅的个性在闭包中有很好的体现，主要优化如下：

* 利用上下文推断参数和返回值类型
* 隐式返回单表达式闭包，即单表达式闭包可以省略`return`关键字
* 参数名称缩写
* 尾随闭包语法
<br>

<h3>闭包表达式</h3>

闭包表达式是一种利用简洁语法构建内联闭包的方式。闭包表达式提供了一些语法优化，使得撰写闭包变得简单明了。

下面我们来看一个例子：

Swift标准库提供了名为`sorted(by:)`的方法，它会根据你所提供的用于排序的闭包函数将已知类型数组中的值进行排序。一旦排序完成，`sorted(by:)`方法会返回一个与原数组大小相同，包含同类型元素且元素已正确排序的新数组。原数组不会被`sorted(by:)`方法修改。

举个栗子：闭包表达式示例使用`sorted(by:)`方法对一个`String`类型的数组进行字母逆序排序。

通常我们会这样写：

``` swift
//初始化一个字符串类型数组
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

//定义排序闭包函数
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}

//执行排序操作
var reversedNames = names.sorted(by: backward)
// reversedNames 为 ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
```

`sorted(by:)`方法接受一个闭包(其实就是一个函数指针)，该闭包函数需要传入与数组元素类型相同的两个值，并返回一个布尔类型值来表明当排序结束后传入的第一个参数排在第二个参数前面还是后面。如果第一个参数值出现在第二个参数值前面，排序闭包函数需要返回`true`，反之返回`false`。

该例子对一个`String`类型的数组进行排序，因此排序闭包函数(`func backward`)类型需为`(String, String) -> Bool`(即函数指针类型，两个参数`String`类型，返回值是`Bool`类型的函数指针)。

如果第一个字符串(`s1`)大于第二个字符串(`s2`)，`backward(_:_:)`函数会返回`true`，表示在新的数组中`s1`应该出现在`s2`前。对于字符串中的字符来说，“大于”表示“按照字母顺序较晚出现”。这意味着字母`"B"`大于字母`"A"`，字符串`"Tom"`大于字符串`"Tim"`。该闭包将进行字母逆序排序，`"Barry"`将会排在`"Alex"`之前。

然而，以这种方式来编写一个实际上很简单的表达式(`a > b`)，确实太过繁琐了。对于这个例子来说，利用闭包 表达式语法可以更好地构造一个内联排序闭包。

_闭包表达式语法有如下的一般形式:_

``` swift
{ (parameters) -> returnType in
    statements
}
```

>闭包表达式参数可以是`in-out`参数，但不能设定默认值。也可以使用具名的可变参数(注意:但是如果可变参数不放在参数列表的最后一位的话，调用闭包的时时编译器将报错。)。元组也可以作为参数和返回值。

于是，我们可以将之前`backward(_:_:)`函数用闭包表达式优化，如下：

``` swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```

这个闭包的函数体部分如此短，以至于可以将其改写成一行代码:
``` swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

该例中`sorted(by:)`方法的整体调用保持不变，一对圆括号仍然包裹住了方法的整个参数。然而，参数现在变成了内联闭包。

写到这里，如果你认为就此结束了的话，Swift君只能说你还是太年轻。

开篇我们提到，闭包会有这样一条优化：
__利用上下文推断参数和返回值类型。__

排序闭包函数是作为`sorted(by:)`方法的参数传入的，Swift可以推断其参数和返回值的类型。`sorted(b y:)`方法被一个字符串数组调用，因此其参数必须是 `(String, String) -> Bool`类型的函数。这意味着
`(String, String)`和`Bool`类型并不需要作为闭包表达式定义的一部分。因为所有的类型都可以被正确推断，返回箭头(`->`)和围绕在参数周围的括号也可以被省略:

``` swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

实际上，通过内联闭包表达式构造的闭包作为参数传递给函数或方法时，总是能够推断出闭包的参数和返回值类型。这意味着闭包作为函数或者方法的参数时，你几乎不需要利用完整格式构造内联闭包。

其实，`return`关键词我们也是可以省略的，因为可以 __单表达式闭包隐式返回__ ,这也是闭包的主要优化之一。

如上的例子可以改写为:
```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

> `sorted(by:)`方法的参数类型明确了闭包必须返回一个`Bool`类型值。因为闭包函数体只包含了一个单一表达式(`s1 > s2`)，该表达式返回`Bool`类型值，因此这里没有歧义，`return`关键字可以省略。

开篇提到的闭包优化的几点中，还有说道 __参数名称缩写__ 功能。

Swift君有说：你可以直接通过`$0`，`$1`，`$2`来顺序调用闭包的参数，以此类推。如果你在闭包表达式中使用参数名称缩写，你可以在闭包定义中省略参数列表，并且对应参数名称缩写的类型会通过函数类型进行推断。

`in`关键字也同样可以被省略,因为...，看下面优化后的代码你明白，`in`在这里确实没什么卵用了：

``` swift
reversedNames = names.sorted(by: { $0 > $1 } )
```

其中，`$0`和`$1`表示闭包中第一个和第二个`String`类型的参数.

我次奥！！！到这里，我相信大家会有跟我一样的感叹，哈哈~

所以，最后还是有必要来一记大招：
``` swift
reversedNames = names.sorted(by: >)
```

是的，你没有看错，这才是最终的形态。

Swift的`String`类型定义了关于大于号(`>`)的字符串实现，其作为一个函数接受两个`String`类型的参数并返回`Bool`类型的值。而这正好与`sorted(by:)`方法的参数需要的函数类型相符合。因此，你可以简单地传递一个大于号，Swift可以自动推断出你想使用大于号的字符串函数实现。

贴上大于号(`>`)的定义：

``` swift
/// Returns a Boolean value indicating whether the value of the first argument
/// is greater than that of the second argument.
///
/// This is the default implementation of the greater-than operator (`>`) for
/// any type that conforms to `Comparable`.
///
/// - Parameters:
///   - lhs: A value to compare.
///   - rhs: Another value to compare.
public func ><T>(lhs: T, rhs: T) -> Bool where T : Comparable
```

当然还有更多的运算符方法，想到这里，我就想问你，简洁不简洁，优雅不优雅，刺激不刺激？！哈哈。。。
