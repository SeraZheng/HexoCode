---
layout: post
title: UIResponder中的inputView与inputAccessoryView
date: 2015-08-22
description: "描述使用inputAccessoryView完成自己的需求"
tags: [UIKit]
categories: [iOS]
---

# 前言
最近刚给app添加了类似QQ中“@”消息人的功能，其中主要是重写了`UIResponder`中的`inputAccessoryView`这个成员属性。关于`inputView`和`inputAccessoryView`的介绍和使用，我首先推荐俩篇个人认为很不错的辅助文档：[开发者文档----Custom Views for Data Input](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html)和[博客----UITextField docked like iOS Messenger](http://derpturkey.com/uitextfield-docked-like-ios-messenger/)。
<!-- more -->
# 属性介绍
`inputView`和`inputAccessoryView`是在`UIResponder`中定义的俩个`readonly`的属性。而且所有的UIKit中的控件都继承自`UIResponder`，定义这俩个属性，是为开发者改变系统键盘的视图提供便利。苹果SDK中提供了`UITextView`和`UITextField`俩种editor，而且都重定义了`inputView`和`inputAccessoryView`俩个属性为`readwrite`。

此外，我们也可以在自己定义的UIView的子类中，重定义这俩个属性。按照官方文档的说法，当`inputView不`是nil,而且inputView所属的UIView成为第一响应者时，系统就不会显示出键盘，而是显示我们自定义的`inputView`的值；当`inputAccessoryView`不是nil,而且`inputAccessoryView`所属的UIView成为第一响应者时，系统会显示键盘，并且在键盘的顶部显示出我们定义的`inputAccessoryView`。

# 触发显示
当我们点击一个UIView时，它就成为了第一响应者，我们还可以调用`becomeFirstResponder`让视图控件成为第一响应者。而在我们重定义`inputView`和`inputAccessoryView`的UIView中，我们也需要重写`canBecomeFirstResponder`这个方法，并且返回true，这样当我们的UIView成为第一响应者时，`inputAccessoryView`和`inputView`才会自动显示出来。

# 自定义显示
苹果SDK在iOS7中提供了`UIInputView`这个控件，并且在iOS8中提供了`UIInputViewController`这个视图控制器。这俩个是开发者可以很好地用来显示我们自定义的`inputView`和`inputAccessoryView`。`UIInputView`提供了`UIInputViewStyleDefault`和`UIInputViewStyleKeyboard`俩种显示形式，第二种会自动设置背景为系统键盘的背景。