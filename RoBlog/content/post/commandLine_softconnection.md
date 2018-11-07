---
title: 命令行常用命令--软连接
date: 2017-06-23 14:36:26
comments: true
tags: ["常用命令","软连接","Open File"]
categories: ["随笔"]
author: "Ro"
---
比如，想要用命令行指定特定程序打开文件。

如果文件已被指定默认程序：

``` bath
    open httpd.conf
```

<!-- more -->

指定一个特定程序打开文件

``` bath
    # 用 sublime text 打开 httpd.conf
    open -a /Applications/Sublime\ Text.app/ httpd.conf
```

如果使用频繁，还可以添加软连接

``` bath
    sudo ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/bin/sublime
```

输入`sublime httpd.conf`就可以用 `sublime text` 打开文件

mac Mac OS X 11之后的版本，上述操作你可能会遇到下面的问题：
``` bath
    ln: /usr/bin/sublime: Operation not permitted
```

使用管理员sudo命令,依旧失败!

原来MacOS 10.11开始apple引入了Rootless机制,你再也不能随心所欲的改写很多关键路径了,就算你是Root用户也不行,除非是Apple自身签名的软件（含命令行工具）才可以!用Apple的话来说:

Rootless机制将成为对抗恶意程序的最后防线。

Mac OS X 11中的`/usr/bin` 的“Operation not permitted”

如果要关闭Rootless必须重启Mac进入恢复模式,然而关闭它意味着你的MacOS安全大门变得有些薄弱,这并不是什么好消息!!!!!

那么有没有什么办法即可以不用关闭Rootless又可以设置Sublime Text3的快捷方式呢!?

答案是:必须的!!!

我们只需要将其路径添加到`/usr/local/bin`下即可,Rootless并没有限制该目录的修改,而且bash也会从该目录中寻找可执行的bin!YES!!!
``` bath
    sudo ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/sublime
```
