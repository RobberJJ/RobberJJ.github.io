---
title: Swift Tips - 枚举(Enumerations)
date: 2018-12-19 16:11:50
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

#### 何谓枚举(Enumerations)？

通常来说，枚举是为一组有限中可能性的相关值提供的通用类型。

在 `C/C++/Objective C` 中，这种 *通用集合* 被限定为一组整型常数。

而 `Swift` 却将它真正的发扬了光大，灵活且强大：

- *通用集合* 不再被限定为一组整型的常数，它一个字符，或是一串字符，或是浮点数，甚至无需必须指定一个值，因为这个值(官方称为 __*原始值「raw value」*__ )可能并不需要。
- 在上面的基础上，枚举甚至可以指定一种关系，或者叫关联（官方称为 __*关联值「associated value」*__）。
- 你可曾有一种枚举叫做 __*递归枚举（Recursive Enumerations）*__ ？！
- 传统意义上仅有类支持的一些特性，枚举将能够同样实现，比如：计算属性用来提供关于枚举当前值的额外信息；实例方法提供与枚举表示值相关的功能；定义初始化器来初始化成员值；而且能够遵循协议来提供标准功能等等。（后面文章会详细说明这块儿）



<!-- more -->



#### 枚举语法

类似于 `Struct` & `Class` ，枚举通过关键字 `enum` 引入，并将整个定义放在一对大括号中：

```swift
enum SomeEnumeration {
    // enumeration definition goes here
}
```

举个官方文档的栗子：

```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}

//当然我们也可以写在一行中，只是需要以 `,` 分隔。
enum CompassPoint {
    case north, south, east, west
}
```

需要注意的是：

- 通过 `case` 关键字引入新的枚举类别；
- 与 `C` 和 `Objective-C` 不同，`Swift` 枚举情况在创建时未分配默认整数值。在`CompassPoint`上面的例子，`north`，`south`，`east`和`west`不等于隐式`0`，`1`，`2`和`3`。事实上，它们的 *原始值* 与它们名称是 __一致的__ 。



当然，我们可以使用 `var` 或是 `let` 存储枚举值：

```swift
var directionToHead = CompassPoint.west
//或者
let directionToHead = CompassPoint.west
```

如果你想要看起来更简洁一些：

```swift
var directionToHead = .west
//或者
let directionToHead = .west
```

甚至，有时候我们需要可以获取枚举值的数量，我们可以参照下面这样：

```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// Prints "3 beverages available"
```

或者可以通过 `for` 循环来获取所有枚举值：

```swift
for beverage in Beverage.allCases {
    print(beverage)
}
// coffee
// tea
// juice
```



#### 枚举的原始值

枚举情况可以预先填充默认值（称为*原始值*），它们都是相同的类型。

前面我们已经提到了， _原始值_ 可以是一个字符，或是一串字符，或者数字等等。

比如下面这个栗子，存储了原始ASCII值和命名枚举情况：

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

话说，`swift` 中枚举值各种牛逼之后，它的前身的基本功能还能不能好使了？

答案是必须好使的，栗子来了：

```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

上面这样，我们只需要为第一个设置原始值，后面的就会自动 `+1` 了。

另外，我们仍然还是可以通过原始值初始化枚举值的：

```swift
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet is of type Planet? and equals Planet.uranus
```



在此，需要重申的是，如果不设置原始值，其原始值跟其名是 __一致的__ ，比如本文前面提到的 `CompassPoint` ：

```swift
let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection is "west"
```



#### 枚举的相关值

与 *原始值* 不同的是，*相关值* 的是枚举绑定的一组特征值，`swift` 中用 __元组__ 来简洁的表示，下面我们还是举个栗子来说明：

用枚举定义一种产品识别码的类型（包含条形码和二维码），其中条形码通过一组整型数据的元组来表示：`(Int, Int, Int, Int)` ，二维码则通过一串字符来表示：`(String)` 。

那么我们这种枚举类型就可以定义如下：

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}
```

这样，可以这样创建新的条形码：

```swift
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
```

当我们需要更换其值为二维码时候，就可以直接这样：

```swift
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")
```

通过 `switch-case` 判断的时候就能如下实现：

```swift
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

有没有感觉 枚举 能做的事儿多了很多呢？



#### 递归枚举

我们对于递归的理解通常来自于 __函数__ 。那么枚举的递归是什么鬼？

*__递归枚举__ 是拥有另一个枚举作为枚举成员关联值的枚举。*

似乎不管是函数还是枚举只对于递归的理解是一样的。

`swift` 通过 `indirect` 关键字来指明 __*递归*__ 。

这里还是举个简单的栗子：

```swift
//一个存储简单算数表达式的枚举
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
//或者也是可以这样的
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

上面存储了三种算术表达式：普通数字、两个表达式相加、两个表达式相乘。

那么，我们来尝试表示一下这个表达式：`(5 + 4) * 2`

```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
```

假如我们通过一个递归函数来处理上面的递归枚举，则显然是很合适的：

```swift
func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}
```



那么，我们计算上面表达式的值就能这样表示了：

```swift
print(evaluate(product))
// Prints "18"
```

看到这里，你的脑海中，是不是已经有了一些场景在浮现呢？

Go And Do It !