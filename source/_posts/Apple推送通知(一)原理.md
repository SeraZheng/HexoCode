---
layout: post
title: Apple推送通知(一)原理
date: 2015-07-12
tags: [APNS]
categories: [iOS]
---

# 前言　　
最近刚刚给项目的app添加了苹果的推送服务`APNS（Apple Push Notification Service）`,在这里记录一下自己的经历和收获。和之前一样，先给大家推荐一篇我认为非常棒的来自RayWenderlich上面的博客[Apple Push Notification Services in iOS 6 Tutorial](http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1)，APNS的相关开发，我大都是跟着这篇博客在学习。（这里也可以权当是一篇翻译。。。。哈哈哈。。。。）
<!-- more -->
# APNS原理
借用一下该博客的原理图，在这里做一下解释，这个原理图形象具体地描述了`APNS`的工作流程。

![APNS流程图](http://6567812.s21i-6.faiusr.com/2/ABUIABACGAAgjtn8rQUo573olQIw0wM49AM.jpg)

首先app必须拿到设备的`deviceToken`，这个是设备的唯一标识，然后app把`APNS`返回的设备的`deviceToken`发送注册到自己的server上，当你有想要接收的消息时，server就会把你想要的消息经由`APNS`转发到设备上。
 
# 前期条件
根据上面的流程，我们可以知道：首先为了确保开发和测试APNS，开发项目必须具有一下几个条件：

1. 一台装有iOS系统、可以连接外网的设备（APNS是simulator无法测试到的一项特性，上面的流程图可以参证）；
2. 一个开发者账号（有必要的签名文件和证书）；
3. 一台可以连接外网的服务器（或代理服务器）。