---
layout: post
title: GCD：串行/并行/主队列下，同步/异步的执行方式
comments: true
tags:
	- iOS
	- GCD
categories: "iOS"
thumbnail: /imgs/iOS.png
---

GCD中获取各种类型的队列：
``` objc
    //获取串行的队列  
    dispatch_queue_t singalQueue = dispatch_queue_create("single",DISPATCH_QUEUE_SERIAL);
    //获取并发执行的队列
    dispatch_queue_t concrtQueue = dispatch_queue_create("queue", DISPATCH_QUEUE_CONCURRENT);
    //获取主队列
    dispatch_queue_t mainQueue =  dispatch_get_main_queue();
    //获取全局的队列（并发的）
    dispatch_queue_t gobalqueue =  dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

<!-- more -->

<h6>串行队列异步执行任务</h6>

>1. 异步具有创建新线程的能力，会开辟新线程去执行任务；
2. 按照串行的方式去执行任务。

如下调用方式可参考：
``` objc
    - (void)singalAsynQueue{
        //创建串行队列
        dispatch_queue_t singalQueue =   dispatch_queue_create("singal", DISPATCH_QUEUE_SERIAL);
        //在singalQueue中异步执行任务(该方法实现在本文后续中)
        [self asynWithQueue: singalQueue];  
    }
```

<h6>串行队列同步执行任务</h6>

>1. 同步不具有创建新的线程的能力， 不会开辟新的线程去执行任务，会在当前的程序的主线程中去执行任务；
2. 按照串行的方式去执行任务。

如下调用方式可参考：
``` objc
    - (void)singalSynQueue{
        //创建串行队列
        dispatch_queue_t singalQueue =   dispatch_queue_create("singal", DISPATCH_QUEUE_SERIAL);
        //在singalQueue中同步执行任务(该方法实现在本文后续中)
        [self synWithQueue: singalQueue];
    }
```

<h6>并发队列异步执行任务（常用）</h6>

>1. 异步具有创建新的线程的能力，会开辟新的线程去执行任务，不会在当前的程序的主线程中去执行任务；
2. 按照并发的方式去执行任务。

如下调用方式可参考：
``` objc
    - (void)concrtAsynQueue{
        //创建并发执行的队列
    //    dispatch_queue_t concrtQueue = dispatch_queue_create("concrtQueue", DISPATCH_QUEUE_CONCURRENT);
        //获取全局的队列
        dispatch_queue_t concrtQueue =  dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
        //在concrtQueue中异步执行任务(该方法实现在本文后续中)
        [self asynWithQueue:concrtQueue];
    }
```

<h6>并发队列同步执行任务</h6>

>1. 同步不具有创建新的线程的能力， 不会开辟新的线程去执行任务，会在当前的程序的主线程中去执行任务；
2. 按照同步的方式去执行任务。

如下调用方式可参考：
``` objc
    - (void)concrtSynQueue{
        //创建并发执行的队列
    //    dispatch_queue_t concrtQueue = dispatch_queue_create("concrtQueue", DISPATCH_QUEUE_CONCURRENT);
        //获取全局的队列
        dispatch_queue_t concrtQueue =  dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
        //在concrtQueue中同步执行任务(该方法实现在本文后续中)
        [self synWithQueue:concrtQueue];
    }
```

<h6>主队列的同步(会造成程序的死锁)</h6>

如下：
``` objc
    - (void)mainSynQueue{
        //获取主队列
        dispatch_queue_t mainQueue =  dispatch_get_main_queue();
        //在mainQueue中同步执行任务(该方法实现在本文后续中)
        [self synWithQueue:mainQueue];
    }
```

<h6>主队列的异步(在主线程中顺序执行)</h6>

>新添加到主队列中的任务会放到队列的最尾部，等到当前主线程中的任务结束之后然后再从队列的头部取出依次执行（FIFO）先进先出。

如下调用方式可参考：
``` objc
    - (void)mainAsynQueue{
        //获取主队列
        dispatch_queue_t mainQueue =  dispatch_get_main_queue();
        //在mainQueue中异步执行任务(该方法实现在本文后续中)
        [self asynWithQueue:mainQueue];        
    }
```

<h6>异步方法的实现</h6>
``` objc
    - (void)asynWithQueue:(dispatch_queue_t)queue{
        NSLog(@"%@",[NSThread currentThread]);

        dispatch_async(queue, ^{
            NSLog(@"----1----%@",[NSThread currentThread]);
        });

        dispatch_async(queue, ^{
            NSLog(@"----2----%@",[NSThread currentThread]);
        });

        dispatch_async(queue, ^{
            NSLog(@"----3----%@",[NSThread currentThread]);
        });

        dispatch_async(queue, ^{
            NSLog(@"----4----%@",[NSThread currentThread]);
        });

        NSLog(@"--------end------------");
    }
```

<h6>同步方法的实现</h6>
``` objc
    - (void)synWithQueue:(dispatch_queue_t)queue{
        NSLog(@"%@",[NSThread currentThread]);

        dispatch_sync(queue, ^{
            NSLog(@"----1----%@",[NSThread currentThread]);
        });

        dispatch_sync(queue, ^{
            NSLog(@"----2----%@",[NSThread currentThread]);
        });

        dispatch_sync(queue, ^{
            NSLog(@"----3----%@",[NSThread currentThread]);
        });

        dispatch_sync(queue, ^{
            NSLog(@"----4----%@",[NSThread currentThread]);
        });

        NSLog(@"--------end------------");
    }
```
