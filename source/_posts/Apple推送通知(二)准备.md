---
layout: post
title: Apple推送通知(二)准备
date: 2015-07-13
tags: [APNS]
categories: [iOS]
---

# 前言　　
在上一章节中，我们谈到了Apple推送通知的原理流程和必要的三个条件，由于我所从事的主要是iOS客户端的开发，所以关于Server端的就不在这里赘述，在[Apple Push Notification Services in iOS 6 Tutorial](http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1)这篇博客的第二部分也给了一个具体的实例参考；而且按照惯例呢，我也推荐给朋友们一个用Node JS编写的后台应用[Push Server](https://github.com/jazzychad/PushServer)。我们在这一篇主要讲一下如何利用`Apple　ID`生成`签名文件`和`证书`。
<!-- more --> 
# Apple ID
如果没有`Apple ID`的朋友呢，可以通过注册`Apple ID`拥有自己的一个`Apple ID`,操作流程详情见[怎么注册Apple ID](https://appleid.apple.com/cn/)。如果在企业中，企业具有企业开发者账号和团队关系可以添加自己的`Apple　ID`来获取创建证书的权利，如果是个人开发者，那么只能花99刀注册成为个人开发者，在这里我就假设你已经拥有了一个具有团队关系、可以使用的`Apple ID`。
 
# 证书与签名
证书是分俩种，一种是安装在Server端的，一种是随app安装设备中的，而俩种证书都是区分`Development`和`Distribution`俩种版本的。

`Development`版本的证书对应真机测试的app，而`Distribution`版本的证书对应已经发布Apple Store上线的app。关于制作俩种证书和生成签名文件的流程，[Apple Push Notification Services in iOS 6 Tutorial](http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1)这篇博客中已经给出了详细的图形展示。
>我在这里着重点出的就是，我们安装在Server端的证书和签名文件和随app安装在设备中的必须是一致。