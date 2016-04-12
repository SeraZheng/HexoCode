---
layout: post
title: iOS多线程开发（四）NSOperation和NSOperationQueue
date: 2015-08-04
tags: [多线程]
categories: [多线程]
---

# 前言
在前几篇中，我们讲述了多线程的基础概念和在iOS开发中应用多线程的方法。我们在这一篇继续讨论在iOS开发中使用多线程的一种方式，使用`NSOperation`和`NSOperationQueue`。`NSOperation`的实例代表一个任务，默认封装了需要执行的操作和数据，从而简化了我们很多的操作，我们不必在关心底层的线程的运行和状态，只需要把精力放在我们需要执行的操作上。
<!-- more -->
# 使用方式
`NSOperation`是一个`抽象类`，有俩种使用方式。第一种就是使用`Foundation`框架中提供的`NSOperation`的俩个子类：`NSInvocationOperation`和`NSBlockOperation`,第二种就是`自定义`类并继承NSOperation。

# NSInvocationOperation
1)封装要执行的操作

{% codeblock lang:objc %}
NSInvocationOperation *operation = [[NSInvocationOperationalloc] initWithTarget:selfselector:@selector(temp) object:nil];
{% endcodeblock %}

2)开始操作,一旦开始就会执行封装的操作，并且默认是在主线程执行

{% codeblock lang:objc %}
[operation start];
{% endcodeblock %}

# NSBlockOperation
1)创建操作对象，与Objective-C中的block机制结合使用
{% codeblock lang:objc %}
NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^(void){
    //添加要执行的操作
}];
{% endcodeblock %}

2)添加新的操作
{% codeblock lang:objc %}
[operation addExecutionBlock:^(void){
    //添加要执行的操作
}];
{% endcodeblock %}

3)开始执行操作，当添加到当前操作对象的block操作数大于1时，会异步执行操作，并发执行所有操作
{% codeblock lang:objc %}
[operation start];     
{% endcodeblock %}

# 常用方法与属性
1) Cancel
{% codeblock lang:objc %}
@property （getter=isCancelled) BOOL cancelled和　- （void）cancel
{% endcodeblock %}

cancel方法可以取消当前操作，而isCancelled方法可以检测操作是否已经被取消，实际开发中我们可能会频繁地调用isCancelled,而它本身非常地轻量级，不会对我们项目地性能产生较大地损失。

2）添加依赖
{% codeblock lang:objc %}
- （void）addDependency：（NSOperation *）op
{% endcodeblock %}

为操作对象添加依赖操作，只有依赖的操作op执行完之后才会开始执行当前的操作对象。

3）completionBlock
{% codeblock lang:objc %}
@property （copy） void （^completionBlock）(void)
{% endcodeblock %}

当前操作执行完后可执行的block，我们可以通过setCompletionBlock：来添加在操作对象执行完后我们想执行的任务。

# 自定义子类
如果上面的这些属性和方法还不满足需求的话，我们还可以自定义一个`NSOperation`的子类，封装一些我们想要的操作和数据。一个`main`函数基本上是程序的标准入口，所有的线程的开启也是通过`main`方法，所以如果我们自定义`NSOperation`子类的话，必须在子类中重写main方法。
{% codeblock lang:objc %}
-（void）main{
    //我们必须为自定义的operation提供autorelease pool，因为operation完成之后需要销毁
    @autoreleasepool{
        //添加自己的操作逻辑
    };
}
{% endcodeblock %}

除此之外，我们也需要重写一些其他的基本方法，如：```start```，```isFinished```，```isExecuting```等，并且需要实现`KVO`机制。

我们自定义的`NSOperation`子类，可以设置操作是异步执行，还是同步执行，一般默认的都是同步执行，想要我们自定义的operation异步执行，我们需要重写`isConcurrent`这个方法并且返回YES，iOS7.0以后的SDK中，`isConcurrent`被`isAsynchronous`取代了。

#使用NSOperationQueue
为了异步执行我们的操作，我们可以将operation添加到一个`NSOperationQueue`中去执行。一个`NSOperationQueue`操作队列，就相当于一个线程管理器，允许我们添加自己的operation。一旦把operation通过addOperation：方法加到操作队列中，operation就开始被处理，直到完成或者取消，然后被释放掉。

1)创建操作队列，并添加操作
{% codeblock lang:objc %}
NSOperationQueue *queue = [[NSOperationQueuealloc]init];
//添加一个operation
[queue addOperation:operation];
//添加一组operations
[queue addOperations:operations waitUntilFinished:NO];
{% endcodeblock %}

2)执行顺序
operation被添加到操作队列后，通常会按添加顺序依次得到执行，但是一个操作队列中的operation之间存在依赖关系，我们可以通过`NSOperation`的`addDependency：`方法为操作添加依赖；而且operation都有一个`NSOperationQueuePriority`优先级属性，这些都会影响操作队列中的operation的执行顺序。

3)并发任务数量
一个操作队列可以设置并发操作的数量，意思就是队列中最多可以同时运行几条线程。因为默认地，一个`NSOperationQueue`操作队列会为每一个operation提供一条线程来运行，operation之间是并发执行操作的。
{% codeblock lang:objc %}
//模拟串行队列，每次只允许执行一个操作，既只有一条线程
[queue setMaxConcurrentOperationCount:1]
{% endcodeblock %}