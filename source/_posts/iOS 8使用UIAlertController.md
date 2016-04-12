---
layout: post
title: iOS 8使用UIAlertController
date: 2015-08-28
description: "介绍UIAlertController的概念和使用注意"
tags: [UIKit]
categories: [iOS]
---

# 前言
`UIAlertController`是iOS　8之后提供的一个继承自`UIViewController`的视图控制器，主要是修改了`UIAlertView`和`UIActionSheet`的显示以及处理逻辑，摒弃了旧形式，提供了更加强大的操作方法。它将`UIAlertView`和`UIActionSheet`整合到一起，并且添加了对`block`的支持。本篇文章我们就具体讲一下`UIAlertControlelr`的使用。
<!-- more -->
# 介绍
`UIAlertController`作为`UIViewController`的子类，因此显示工作需要我们提供，而不像之前的`UIAlertView`和`UIActionSheet`单独作为一个视图,提供了显示的方法。`UIAlertController`提供了俩种形式:
{% codeblock lang:objc %}
UIAlertControllerStyleActionSheet
UIAlertControllerStyleAlert
{% endcodeblock %}
上面这俩种形式，就应对了我们之前使用的`UIAlertView`和`UIActionSheet`。

# UIAlertAction
在`UIAlertController`中，一个事件就叫一个`UIAlertAction`的实例，这里面就是添加对block机制的支持。而`UIAlertController`就是通过添加`UIAlertAction`的实例来添加操作的。下面是添加方法：
{% codeblock lang:objc %}
- (void)addAction:(UIAlertAction *)action;
{% endcodeblock %}

而每一个`UIAlertAction`代表了一个操作，比如`Cancel`操作等。一个`UIAlertAction`有`title`,`style`属性，`style`属性是`UIAlertControllerStyle`类型的，这决定着是添加一个`UIAlertView`的操作，还是`UIActionSheet`的操作。实例初始化方法如下：
{% codeblock lang:objc %}
+ (instancetype)actionWithTitle:(nullable NSString *)title style:(UIAlertActionStyle)style handler:(void (^ __nullable)(UIAlertAction *action))handler;
{% endcodeblock %}


# 展示
谈到`UIAlertController`的展示之前，我们必须先说一下`UIPresentationController`。`UIAlertController`与传统的`UIAlertView`,`UIActionSheet`最大不同之处在于，在大宽屏幕上是以`UIPopoverViewController`来显示的，首先推荐一篇比较全面的博客：[iOS8新特性 UIPresentationController](http://www.15yan.com/story/jlkJnPmVGzc/)。

`UIPresentationController`主要是为开发者不用再手动计算视图的位置以及适配各种分辨率的屏幕而设计。在iOS 8以后，系统会自动匹配各种宽度的屏幕，而`UIPresentationController`就是提供了适配的一些操作。在`iPad`等大宽屏幕设备上，如果使用了`UIAlertController`，那么系统就会以`popoverPresentationController`的形式将视图弹出，所以此时，我们必须设置`popoverPresentationController`的`sourceView`，`sourceRect`或者是`barButtonItem`，这样系统才会根据我们的设置弹出popover视图。使用方法如下：
{% codeblock lang:objc %}

 UIPopoverPresentationController *popover = alertController.popoverPresentationController;
 if (popover) {
     popover.barButtonItem = self.navigationItem.rightBarButtonItem;
 }

 [self presentViewController:alertController animated:YEScompletion:^{
     
 }];
{% endcodeblock %}