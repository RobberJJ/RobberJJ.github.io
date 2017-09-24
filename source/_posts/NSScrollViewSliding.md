---
layout: post
title: mac中NSScrollView滑动监测
date: 2017-09-24 08:30:33
comments: true
tags:
	- mac
  - NSScrollView
  - osx
  - mac os
categories: "mac"
---

不同于iOS中的UIScrollView，mac中的NSScrollView并没有一堆靠谱好用的delegate来帮助我们方便的监控ScrollView的状态。

iOS中我们可以通过UIScrollView的`contentOffset`属性来获知滑动的位置，而在mac中我们是通过NSScrollView的`contentView`(NSClipView的实例)的`documentVisibleRect.origin`来获知滑动的相对位置。

<!-- more -->

在iOS中我们可以通过UISCrollView的`delegate`方法`- (void)scrollViewDidScroll:(UIScrollView *)scrollView; `来在滑动的过程中做一些我们爱做的事儿呢。

那么在mac中NSScrollView我们有没有同样的东西来做我们爱做的事儿呢？
当然是有的，只是跟iOS中有些许的区别，NSScrollView是通过通知来实现的，直接上代码：

``` mm
//找个合适的地儿，注册通知
NSNotificationCenter *center = [NSNotificationCenter defaultCenter] ;
    [center addObserver: self
               selector: @selector(boundsDidChangeNotification:)
                   name: NSViewBoundsDidChangeNotification
                 object: [self.scrollView contentView]];
```

需要注意的是 ，在上面通知注册之前，你需要加上这么一句：

``` mm
[[self.scrollView contentView] setPostsBoundsChangedNotifications: YES];
```

然后在NSScrollView滑动的时候，你就能在你实现下面这个方法的地方，做你想做的爱做的事儿了：

``` mm
- (void) boundsDidChangeNotification: (NSNotification *) notification
{
    // 在这里进行处理
    NSClipView *changedContentView=[notification object];

    // get the origin of the NSClipView of the scroll view that
    // we're watching

    NSPoint changedBoundsOrigin = [changedContentView documentVisibleRect].origin;


}
```

以上，欢迎典藏。
