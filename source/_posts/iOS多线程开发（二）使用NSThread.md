---
layout: post
title: iOS多线程开发（二）使用NSThread
date: 2015-08-02
tags: [多线程]
categories: [多线程]
---

# 前言　　
在上一篇博客中，我们讲述了`多线程`的基础知识，也谈到了iOS开发中三个使用多线程的技术，`NSThread`、`NSOperation`和`GCD`；我们在这一篇中，主要讲一下第一种多线程技术`NSThread`。相比于其他俩种技术，`NSThread`具有更`轻量级`的优点，也是可以完全控制，完全负责的，也是最麻烦的。我们需要手动地管理线程的生命周期和同步的问题，这里的同步指的是系统资源地分配和调度上的同步。
<!-- more -->
# NSThread创建
`NSThread`的一个实例对象就代表着一条线程，创建一条线程主要有下面几种方法：
{% codeblock lang:objc %}
1. -（instancetype)initWithTarget:(id)target selector:(SEL)selector object:(id)argument
{% endcodeblock %}
使用NSThread的实例方法来创建一条线程，创建之后，必须调用```[yourThread　start]```启动线程.使用这种方法创建线程，我们可以对线程进行控制，例如设置线程名称，优先级，栈尺寸等。
 
{% codeblock lang:objc %}
2. + (void)detachNewThreadSelector:(SEL)selector toTarget:(id)target withObject:(id)argument;
{% endcodeblock %}
使用类方法直接创建并开启一条线程，无需其他操作，但是无法对线程进行进一步的控制

{% codeblock objc %}
3. NSObject -- performSelectorInBackground:(SEL）aSelector withObject:(id)arg
{% endcodeblock %}
非显示地创建一条线程，同样是无法对线程进行控制

# 常用属性方法
NSThread还拥有一些常用的属性和方法，可以让我们对线程进行控制，例如：
 
1. `+ (NSThread *)currentThread`
顾名思义，就是获取当前线程。

2. `@property double threadPriority`
在最新的SDK中被另外一个属性NSQualityOfService替代，标识线程优先级，并且在线程启动后是只读的。

3. `+ （NSThread *）mainThread`
获取程序的主线程

4. `@property NSUInteger stackSize`
标识线程的栈空间的大小，我们可以通过setter方法来改变线程栈空间优化内存。

5. `+ （void）exit  ` 与　`- （void）cancel`
如果我们想中断一个线程的运行时，按照许多前辈地建议，我们最好先用cancel实例方法来标记一下线程，这个方法只是给线程一个判断位，然后根据属性判断```isCancelled```的值，确实之后再调用```exit```类方法彻底结束线程。后来看到一篇文档上写到，当使用```sleepForTimeInterval:```方法使得线程处于休眠状态时，这个时候调用```cancel```是没有作用的，当线程醒过来之后，依然会执行任务。

# 线程通信
一个进程中的线程并不是各自独立的，他们往往需要相互通信，例如，有时候会把占据时间的下载任务放到后台线程中，等到数据下载完之后把数据传到主线程中去更新界面。在`NSObject`的catogery　`NSThreadPerformAdditions`中就提供了几个线程之间的通信方法：
{% codeblock lang:objc %}
1. - (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait
2. - (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait
{% endcodeblock %}