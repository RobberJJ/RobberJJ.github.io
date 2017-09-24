---
layout: post
title: mac中NSScrollView自定义滑动条NSScroller
date: 2017-09-24 09:00:15
comments: true
tags:
	- mac
  - NSScrollView
  - NSScroller
  - osx
  - mac os
categories: "mac"
---

>最近在捣鼓mac上开发app，本以为iOS、mac开发本是同根生，弄起来应该差不多，上手之后才发现iOS是多么的优雅自在，而mac是干啥都觉得不太顺手，UIKit跟AppKit差别还真是不一般的大。

最近，感觉NSScrollView右侧的滑动条长得不好看，想把滑动条的背景去掉，本想着设置个某个属性就能轻而易举的搞定，谁不知捣鼓半天没弄出来，mac开发的相关资料也着实太少，在这里记一笔，加深一下印象。

<!-- more -->

>Tips：需要的同学，可以直接前往[这里](https://github.com/RobberJJ/JJScroller)指点批评，或者为我转身点亮一颗小星星。

滑动条需要瞒住一下几个方面的需求：
* 滑动页面时候，显现滑动条，跟随滑动；
* 停止滑动页面的时候，自动隐藏滑动条；
* 任何时候滑动条背景色为透明；

下面直接上一下，NSScroller子类 JJScroller的`.m`文件
``` mm
@implementation JJScroller

- (id)initWithFrame:(NSRect)frameRect
{
    self = [super initWithFrame:frameRect];
    if (self == nil) {
        return nil;
    }
    [self commonInitializer];
    return self;
}

- (void)awakeFromNib
{
    [super awakeFromNib];
    [self commonInitializer];
}

- (void)commonInitializer
{
    NSTrackingArea *trackingArea = [[NSTrackingArea alloc] initWithRect:self.bounds options:(
NSTrackingMouseEnteredAndExited | NSTrackingActiveInActiveApp | NSTrackingMouseMoved
) owner:self userInfo:nil];
    [self addTrackingArea:trackingArea];
}

- (void)drawRect:(NSRect)dirtyRect {

    // Do some custom drawing...

    // Call NSScroller's drawKnob method (or your own if you overrode it)
    [self drawKnob];
}

- (void)drawKnobSlotInRect:(NSRect)slotRect highlight:(BOOL)flag
{
    // Don't draw the background. Should only be invoked when using overlay scrollers
}


- (void)mouseExited:(NSEvent *)theEvent
{
    [super mouseExited:theEvent];
    [self fadeOut];
}

- (void)mouseEntered:(NSEvent *)theEvent
{
    [super mouseEntered:theEvent];
    [NSAnimationContext runAnimationGroup:^(NSAnimationContext *context) {
        context.duration = 0.1f;
        [self.animator setAlphaValue:1.0f];
    } completionHandler:^{
    }];
    [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(fadeOut) object:nil];
}

- (void)mouseMoved:(NSEvent *)theEvent
{
    [super mouseMoved:theEvent];
    self.alphaValue = 1.0f;
}

- (void)setFloatValue:(float)aFloat
{
    [super setFloatValue:aFloat];
    [self.animator setAlphaValue:1.0f];
    [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(fadeOut) object:nil];
    [self performSelector:@selector(fadeOut) withObject:nil afterDelay:1.5f];
}

- (void)fadeOut
{
    [NSAnimationContext runAnimationGroup:^(NSAnimationContext *context) {
        context.duration = 0.3f;
        [self.animator setAlphaValue:0.0f];
    } completionHandler:^{
    }];
}

+(CGFloat)scrollerWidthForControlSize:(NSControlSize)controlSize scrollerStyle:(NSScrollerStyle)scrollerStyle{
    return 15;
}

+ (BOOL)isCompatibleWithOverlayScrollers
{
    return self == [JJScroller class];
}

@end
```
