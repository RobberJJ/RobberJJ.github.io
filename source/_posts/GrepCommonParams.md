---
title: grep命令常用参数
date: 2018-09-26 14:39:50
tags:
    - grep
categories: "随笔"
---

`-A num, --after-context=num` - 打印出找到的匹配行后num行。这个跟-B，-C类似功能，主要用于查看上下文。

`-B num, --before-context=num` - 打印出找到的匹配行前num行。

`-C[num, --context=num]` - 打印出找到的匹配行前后num行，相当于-A num -B num。
<!-- more --> 

`-c, --count` - 统计出匹配的总行数，同一行有两个匹配也只算一行。

`--colour=[when, --color=[when]]` - 将匹配的字符标记为彩色，when可以是’never’，‘auto’或’always’。

`-e pattern, --regexp=pattern` - 制定在搜索中使用的匹配模式，多用于指定多个匹配模式的情况。

`-H` - 在每一行前面打印出匹配的文件名，在多个文件的情况下这个是默认选项。

`-h` - 在多个文件的情况下不打印出匹配的文件名。

`-n` - 打印出匹配所在行的行数。

`-i, --ignore-case` - 查找时忽略大小写。

`-R, -r, --recursive` - 递归的去查找子目录下的文件。

`-v, --invert-match` - 打印出不匹配的行。

`-w, --word-regexp` - 只匹配整个单词。