---
title: Development cannot be enabled while your device is locked.
date: 2018-05-16 12:15:50
tags:
    - locked
    - XCode
    - iOS
categories: "iOS"
---

有时候我们真机调试的时候，会遇到这样的报错：

>Development cannot be enabled while your device is locked.

但是！我TM手机明明解锁了啊，指纹解的锁不是？密码解的锁不是？没感应到？换个姿势再来一次？还是不行？？

哈哈，有同样经历的点个赞吧。

<!-- more --> 

估计是有这么个不经意的瞬间，手机首次插上mac的时候，苹果给你弹了个框框，让你`trust (信任)`它，然后我们手欠的点了`不trust (信任)`，然后它生气了，不让你用了，就是上面的局面。

关键的是，它从此以后就不再弹了。。。

我TM错了，还不行么？你回来再给我弹一次啊！！

后悔药：

>设置 --> 通用 --> 还原  --> 还原位置和隐私 

按照上面的操作之后，重插，就会弹了~~~ 不用谢我~

后遗症：
>位置和隐私被重置了，之前已经设置位置和隐私的APP们，你再次进去的时候，都开始积极的弹框让你赏口饭了。

【/笑哭】🤣

这块儿官网其实是有说明的：
https://support.apple.com/en-us/HT202778

划重点:
![](https://upload-images.jianshu.io/upload_images/6080248-2b5548a95f804bac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
