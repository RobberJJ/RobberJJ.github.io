---
layout: post
title: UIImageView添加圆角的几种方法
date: 2017-06-22 15:26:06
comments: true
tags:
	- iOS
	- UIImageView
categories: "iOS"
thumbnail: /imgs/iOS.png
---
创建一个UIImageView对象：
``` mm
    UIImageView * poImgView = [[UIImageView alloc] init];
```

<!-- more -->

<h6>第一种方法</h6>

``` mm
    poImgView.layer.cornerRadius = poImgView.frame.size.width/2.0;
    poImgView.layer.masksToBounds = YES;
    （或者 poImgView.clipsToBounds = YES;）
```

>clipsToBounds是对view的切割，masksToBounds是对layer的切割。

性能消耗：
这个是离屏渲染（off-screen-rendering），对性能消耗比较大。
fps大致在45帧左右（每个cell 做2个imageview）（正常fps是60帧，越小，用户体验越差）。

>离屏渲染，指的是GPU在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作。由上面的一个结论视图和圆角的大小对帧率并没有什么卵影响，数量才是伤害的核心输出啊。可以知道离屏渲染耗时是发生在离屏这个动作上面，而不是渲染。为什么离屏这么耗时？原因主要有创建缓冲区和上下文切换。创建新的缓冲区代价都不算大，付出最大代价的是上下文切换。

<h6>第二种方法</h6>

``` mm
    CAShapeLayer *layer = [CAShapeLayer layer];
    UIBezierPath *aPath = [UIBezierPath bezierPathWithOvalInRect:aImageView.bounds];
    layer.path = aPath.CGPath;
    poImgView.layer.mask = layer;
```
性能消耗：
测试fps大致在20帧左右，比方案A的消耗更大。

<h6>第三种方法</h6>

``` mm
    - (UIImage *)imageWithCornerRadius:(CGFloat)radius {
        CGRect rect = (CGRect){0.f, 0.f, self.size};
        UIGraphicsBeginImageContextWithOptions(self.size, NO, UIScreen.mainScreen.scale);
        CGContextAddPath(UIGraphicsGetCurrentContext(),[UIBezierPath bezierPathWithRoundedRect:rect cornerRadius:radius].CGPath);
        CGContextClip(UIGraphicsGetCurrentContext());
        [self drawInRect:rect];
        UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();
        return image;
    }
```

性能消耗：
这个是on-screen-rendering
相当于时时去做渲染，相比于A.B方案的离线渲染，此方法对性能消耗最低，推荐用此方案。

<h6>值得一提</h6>
第一种跟第二种方法都是比较常见的方式，但是都不推荐，对此的优化方案是是用第三种方式，但是如果非要使用第一种跟第二种，补救措施是：

``` mm
    self.layer.shouldRasterize = YES;
    self.layer.rasterizationScale = [UIScreen mainScreen].scale;
```

>当shouldRasterize设成true时，layer被渲染成一个bitmap，并缓存起来，等下次使用时不会再重新去渲染了。

>实现圆角本身就是在做颜色混合（blending），如果每次页面出来时都blending，消耗太大，这时shouldRasterize = yes，下次就只是简单的从渲染引擎的cache里读取那张bitmap，节约系统资源。

>如果在滚动tableView时，每次都执行圆角设置，肯定会阻塞UI，设置这个将会使滑动更加流畅。
