---
layout: post
title: 浅谈一次练手：GitHub和CocoaPods
date: 2015-07-05
tags: [GitHub开源]
categories: [开发心得]
---

# 前言　　
在我最早接触开源社区的时候，许多的前辈都给我推荐了`GitHub`，一个开源代码库和版本控制系统，一个管理软件开发以及发现已有开源代码的首选。`GitHub`上分为`私有`和`开放`俩种项目，私有的项目是需要付费的，我想这也是`GitHub`的一种长期的经营策略。而关于`CocoaPods`,相信做iOS的开发者们都不陌生，否则就该返厂进修啦，哈哈哈。。。
<!-- more -->
# CocoaPods和GitHub
`CocoaPods`作为最近几年流行的第三方库的管理为iOS开发项目提供了大大的便利，而且`CocoaPods`也是托管在`GitHub`上的。而至于`CocoaPods`的安装和使用，我在这里推荐前辈的一篇博客，[唐巧的技术博客------用CocoaPods做iOS程序的依赖管理](http://blog.devtang.com/blog/2014/05/25/use-cocoapod-to-manage-ios-lib-dependency/)，我自认为没有前辈写的那么漂亮，就不卖弄一番啦。而关于`GitHub`的具体使用在这里也不具体的描述了，网上有太多太多地前辈们总结的帖子，我自认为没有他们写地那么详细和完整，所以就不在这里赘述了。
 
# 第一个开源项目
之前刚在GitHub上创建了第一个开源项目[MediaBar](https://github.com/SeraZheng/MediaBar),并发布了release 1.1.0版本，目的一来是熟悉一下GitHub和CocoaPods的使用，二来想根据现有项目的开发回顾一下技术。`MediaBar`是一个`UIToolBar`的子类，之前想继承自`UIView`，这样可以接受大多数人的定制需求，但是考虑到设计的全面性，暂时就先缩小了一下范围。`MediaBar`主要使用了俩个依赖框架，`DTRichTextEditor`和`CTAssetsPickerController`,提供给开发者编辑富文本和选择图片的功能。

# MediaBar　　
本篇博客的题目叫`“浅谈一次练手:GitHub和CocoaPods”`，其主要的想法是借这次练手的开源项目`MediaBar`，来谈一谈在开发过程中我总结的一点感受。之前并不了解`CocoaPods`，也是在项目中开始渐渐地熟悉使用。然后通过建立自己的Pod，才算真正熟悉了这个管理工具。通过它开发者可以便利地将一些十分有用的第三方库加到自己的项目中，极大简化了自己手动添加依赖库的步骤，相信会有越来越多的项目把它作为依赖管理工具。