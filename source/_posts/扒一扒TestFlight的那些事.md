---
layout: post
title: 扒一扒TestFlight的那些事
date: 2015-07-08
tags: [TestFlight]
categories: [开发心得]
---

# 前言　　
近几天一直在做`APNS（Apple Push Notification Service）`消息推送。在使用`TestFlight`做不同证书测试的时候，添加我的账号邀请，一直收不到邀请邮件，于是深入了一下对`TestFlight`的研究。关于`TestFlight`的使用操作流程，我首先推荐朋友们看一篇我在CocoaChina上看到的一篇博客:[TestFlight被收购了，那我们怎么使用呢？](http://www.cocoachina.com/ios/20141229/10724.html)，希望正打算研究`TestFlight`操作的朋友们可以从中得到帮助。
<!-- more -->
# TestFlight
苹果在二月份收购了`TestFlight`的母公司`Burstly`，几个月之后在WWDC 2014上就宣布将`TestFlight`集成在`iOS8`的开发组件中。这次收购最明显的影响是——TestFlight终止了对Android的支持。同时中止了`TestFlight` iOS SDK的支持，除非你在之前已经是`TestFlight`的用户。目前，`TestFlight`已经和一些新特性集成进了`iTunes Connect`。而且，`TestFlight不仅支持内部人员的测试，也提供给外部人员测试的支持，预计也将会提供预览崩溃报告的功能，这些特性会给项目团队开发测试提供极大的便利。