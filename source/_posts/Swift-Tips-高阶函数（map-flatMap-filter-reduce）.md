---
title: Swift Tips - 高阶函数（map & flatMap & filter & reduce）
date: 2018-12-04 17:41:43
tags:
	- Swift
	- iOS
	- Swift Tips
categories: "swift"
---

官方提供的高阶函数，不仅可以大大的简化我们的代码，而且一般来讲会比传统(我们自己实现)的实现更好更快。PS：可能是我自己能力不足，哈哈~

可能有过这样的苦恼：费劲巴拉的终于实现了，猛然发现官方原本提供了现成的方法，而且比自己实现的更好。

内心仿佛一万只草泥马飘过。。。

总之，**你用或者不用，它们就在那里！**



今天主要介绍一下 `swift` 提供的几个高阶函数 —— **_map_** & **_flatMap_** & **_filter_** & **_reduce_** 。

<!-- more -->

#### `map` 函数

`map` 方法获取一个闭包表达式作为其唯一参数。 数组中的每一个元素调用一次该闭包函数，并返回该元素所映射的值。

简单说就是数组中每个元素通过某个方法进行转换，最后返回一个新的数组。

比如下面这个栗子：

```swift
//将 Int 类型数组（各个商品的金额），转换成 String 类型的数组（在金额前加上 $ 符号）
let prices = [20,30,40]
let strs = prices.map({ "$\($0)" })
print(strs) //["$20", "$30", "$40"]
```

这样的操作是不是更让你觉的爽歪歪呢？？



#### `flatMap` 函数

`flatMap` 方法同 `map` 方法比较类似，只不过它返回后的数组中不存在 `nil`（自动把 `nil` 给剔除掉），同时它会把 `Optional` 解包。

这里我们也通过个栗子来直观的理解：

```swift
//比较 map 和 flatMap 这两个方法
let array = ["Apple", "Orange", "Grape", ""]
 
let arr1 = array.map { a -> Int? in
    let length = a.count
    guard length > 0 else { return nil }
    return length
}
print("arr1:\(arr1)")  //arr1:[Opitional(5), Opitional(6), Opitional(5), nil]
 
let arr2 = array.flatMap { a-> Int? in
    let length = a.count
    guard length > 0 else { return nil }
    return length
}
print("arr2:\(arr2)")  //arr2:[5, 6, 5]
```

除此之外，`flatMap` 还能把数组中存有数组的数组（二维数组、N维数组）一同打开变成一个新的数组。

```swift
let array = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
let arr1 = array.map{ $0 }   // [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
let arr2 = array.flatMap{ $0 } // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

是不是很优秀？？



#### `filter` 函数

`filter` 方法用于过滤元素，即筛选出数组元素中满足某种条件的元素。

来来来，举个简单的栗子：

```swift
//筛选出金额大于 25 的元素。
let prices = [20,30,40]
let result = prices.filter({ $0 > 25 })
print(result)  //[30, 40]
```

这样就不用 `for` 里面再去 `if` 判断了不是？



#### `reduce` 函数

`reduce` 方法把数组元素组合计算为一个值，并且会接受一个初始值，这个初始值的类型可以和数组元素类型不同。

可能不好理解，来个经典的求和栗子：

```swift
let prices = [20,30,40]
let sum = prices.reduce(0) { $0 + $1 }
print(sum)  // 90
```

**甚至，**上面的代码还能简化成下面这样：

```swift
let sum = prices.reduce(0, +)
```

哈哈~ 是不是有点儿意思了？？

显然，`reduce` 不仅仅是能求和，比如它还能这样：

```swift
let array = ["Apple", "Orange", "Grape"]
let str = array.reduce("", {
    return $0 == "" ? $1 : $0 + "、" + $1
})
print(str)  // Apple、Orange、Grape
```

当然，上面我们只是可以举个栗子，字符串的拼接我们可能会选择这样：

```swift
let str = array.joined(separator: "、")
```

平常的需求如此的丰富多彩，总有适合的场景，择机使用就好~~

**而，**下面要说的是：**其实在各个高阶函数之间也是并不孤单的！**



#### 组合使用

话不多说，举个 `flatMap & filter` 组合的栗子：

```swift
let collections = [[5,2,7],[4,8],[9,1,3]]
let onlyEven = collections.flatMap {
    $0.filter { $0 % 2 == 0 }
}
print(onlyEven)  //[2, 4, 8]
```

这样的栗子还有更好，等你在撸代码的过程中不断的去发现惊喜吧！！



#### 链式组合

**map** & **flatMap** & **filter** 这三个函数的返回值仍旧是一个数组，所以我们当然对其返回值继续进行一些操作，不是么？

一个栗子：

```swift
//将数组中大于 7 的所有数字进行求和操作。
let marks = [4,5,8,2,9,7]
let totalPass = marks.filter{$0 > 7}.reduce(0,+)  // 17
```

再一个栗子：

```swift
//对某一个数组里面的数字进行平方操作然后再筛选出偶数值。
let numbers = [4,5,8,2,9,7]
let evenSquares = numbers.map{$0 * $0}.filter{$0 % 2 == 0}  // [16, 64, 4]
```



以上。

希望可以对你有所帮助！！嘎嘎~