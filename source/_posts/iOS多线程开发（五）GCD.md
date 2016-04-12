---
layout: post
title: iOS多线程开发（五）GCD开发
date: 2015-08-05
tags: [多线程]
categories: [多线程]
---

# 前言
我们在这一篇继之前的文章，记录另外一种更简单得使用多线程的方式，那就是`GCD`（Grand Central Dispatch）。`GCD`是libdispatch（Apple库）的代名词，为多核设备的并发处理和应用程序的性能优化提供了良好的支持。学习GCD之前，希望大家可以先温顾一下我在“多线程基础”这篇文章中提到的一些基础概念：`队列`，`串行与并行`，`同步与异步`。GCD使用C语言编写的一套提供并发处理的函数库，对于C语言熟悉的朋友，理解和使用起来应该会更简单。
<!-- more -->
# Dispatch Queues
我们的程序进程中有一个`主队列`（main queue），所有与UI处理相关的操作都必须在主队列进行。我们可以使用`dispatch_get_main_queue`函数获取到主队列的引用，而且主队列是一个`串行队列`，所有的任务都是一个一个处理的。此外，系统还为我们提供了`并行队列`，就是`全局调度队列`（Global Dispatch Queue），按优先级不同可以分为：
{% codeblock lang:objc %}
DISPATCH_QUEUE_PRIORITY_HIGH；
DISPATCH_QUEUE_DEFAULT；
DISPATCH_QUEUE_LOW；
DISPATCH_QUEUE_PRIORITY_BACKGROUND；
{% endcodeblock %}

我们可以使用`dispatch_get_global_queue`获取全局调度队列的引用。此外，我们也可以使用`dispatch_queue_create`函数创建自己的队列：
{% codeblock lang:objc %}　
dispatch_queue_t   myQueue = dispatch_queue_create("myQueue",NULL);
{% endcodeblock %}

# Synchronous and Asynchronous
GCD提供了俩个函数分别代表了`同步`与`异步`操作：`dispatch_sync`代表`同步`执行任务，`dispatch_async`代表`异步`执行操作。这俩个函数都结合`block`进行使用的。

# 单例线程安全
`单例模式`是开发中常常应用到的一种设计模式，而单例也会遇到线程安全的问题，如果多个线程同时访问并初始化一个单例对象，就会可能破坏掉单例的安全性，即使得资源变得不可信。GCD提供了单例线程的安全处理：
{% codeblock lang:objc %}
+ (instancetype)sharedContext
{
static MyContext *sharedContext = nil;
staticdispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
sharedContext = [[MyContext alloc] init];
NSLog(@"Singleton has memory address at: %@", sharedContext);
});
return sharedPhotoManager;
}
{% endcodeblock %}

# Dispatch Group
如果当前进程中有俩个非主线程，而这俩个线程是并行执行的，现在的需求是当这俩个线程都执行完后，汇总结果再执行下面的任务。针对这种情况，我们就可以应用到`dispatch_group`的概念，将多个线程同时加入到group中，然后使用`dispatch_group_async`函数并行执行所有的线程，最后使用`dispatch_group_notify`函数来汇总结果。

# 其他用法
GCD中还提供了一些高级用法来帮助我们更好地使用多线程。例如，使用`dispatch_after`和`dispatch_time`来做一些延迟程序执行时间的事情，使用`dispatch_source`控制共享资源等等。