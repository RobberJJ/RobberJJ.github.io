---
layout: post
title: iOS 9.0之后NSString encode方法替换
date: 2017-07-22 11:10:08
comments: true
tags:
	- encode
	- NSString
	- iOS
categories: "iOS"
thumbnail: /imgs/iOS.png
---

在iOS 9.0之后，以前常用的`NSString`编码的方法`stringByAddingPercentEscapesUsingEncoding:`被弃用了，项目中可能会出现一堆如下⚠️：
>'stringByAddingPercentEscapesUsingEncoding:' is deprecated: first deprecated in iOS 9.0 - Use-stringByAddingPercentEncodingWithAllowedCharacters: instead, which always uses the recommended UTF-8 encoding, and which encodes for a specific URL component or subcomponent since each URL component or subcomponent has different rules for what characters are valid.


作为一个有强迫症的优秀程序猿，表示不能忍！

<!-- more -->

文档里面是这么说的：

```
- (nullable NSString *)stringByAddingPercentEscapesUsingEncoding:(NSStringEncoding)enc NS_DEPRECATED(10_0, 10_11, 2_0, 9_0, "Use -stringByAddingPercentEncodingWithAllowedCharacters: instead,which always uses the recommended UTF-8 encoding, and which encodes for a specific URL component or subcomponent since each URL component or subcomponent has different rules for what characters are valid.");
```

很明白，用`stringByAddingPercentEncodingWithAllowedCharacters:`方法替换之。

这个方法官方文档是这么说的：
>// Returns a new string made from the receiver by replacing all characters not in the allowedCharacters set with percent encoded characters. UTF-8 encoding is used to determine the correct percent encoded characters. Entire URL strings cannot be percent-encoded. This method is intended to percent-encode an URL component or subcomponent string, NOT the entire URL string. Any characters in allowedCharacters outside of the 7-bit ASCII range are ignored.

最后一句` Any characters in allowedCharacters outside of the 7-bit ASCII range are ignored.`，意思就是说，任何非7-bit ASCII字符将不被''allowed"，也就是说非ASCII字符将始终被编码，比如中文。

那么问题来了，`(NSCharacterSet *)allowedCharacters`这个参数是什么鬼？研究摸索最终闹明白了，上面方法在处理的时候不会编码url的保留字符，如果需要编码之，需要通过`(NSCharacterSet *)allowedCharacters`这个参数指定。总结如下

```
[aString stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];

//等价于
[aString stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet characterSetWithCharactersInString:@"`#%^{}\"[]|\\<> "]];
```

>注意：
>1. 字符集最后是一个空格！
>2. 这里字符集的意思就是，字符串中含有字符集里面的字符将会会被编码。

另外，URL中常用的`NSCharacterSet`类型定义在分类`NSCharacterSet (NSURLUtilities)`中，包含字符集如下：

```
URLHostAllowedCharacterSet      "#%/<>?@\^`{|}

URLFragmentAllowedCharacterSet  "#%<>[\]^`{|}

URLPasswordAllowedCharacterSet  "#%/:<>?@[\]^`{|}

URLPathAllowedCharacterSet      "#%;<>?[\]^`{|}

URLQueryAllowedCharacterSet     "#%<>[\]^`{|}

URLUserAllowedCharacterSet      "#%/:<>?@[\]^`
```

以上，还在等什么，全局搜索替换吧，消灭⚠️！
