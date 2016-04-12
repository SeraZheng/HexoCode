---
layout: post
title: UIViewController解耦---浅析Three20架构
date: 2016-01-13
description: "根据经典的MVC、MVVM架构解析Three20中的架构模式"
tags: [架构]
categories: [开发心得]
---

# 前言

Three20是一款由Facebook开源的框架，由大神[Joe Hewitt](https://en.wikipedia.org/wiki/Joe_Hewitt_(programmer))创建，曾经风靡一时，被无数开发者观阅。Three20主要提供了UI模块、Network模块以及相关的一些工具。Three20自开源之初就褒贬不一，有人称赞它强大的UI工具，也有人在诟病Three20各个模块之间的耦合度太高，而且更多人在抱怨Three20极少的开发文档，我想这些大概也是Three20在苹果发布iOS6之后就停止了更新维护的原因吧。大神[Joe Hewitt](https://en.wikipedia.org/wiki/Joe_Hewitt_(programmer))创建的在Github上的源码早已删除，目前只有少数人在GitHub上为自己的项目维护。而我也是有幸在某个项目中见识到了曾经耳闻，却未目睹的Three20框架，因此才有了这篇文章。
<!-- more -->
# 架构

最近大家都在讨论MVC、MVVM以及MVP三种在移动端开发中常用到的架构模式，究竟是哪种架构最强大，最适合移动开发者使用。这里笔者也阐述一下个人意见，有句方言叫“树挪死，人挪活”，个人认为，架构是死的，开发者是活的，我们不需要局限于哪一种架构的模式之下，看到大家都在用MVVM，于是花大成本将MVC架构模式的老项目重构成了MVVM架构，这种重构个人看来其实并没有意义。更多的架构话题就不想在这里讨论了，笔者推荐几篇大神们关于架构的见解。

* [被误解的 MVC 和被神化的 MVVM](http://blog.devtang.com/blog/2015/11/02/mvc-and-mvvm/)

    这是一篇被早已被翻烂了的文章，起码我个人反复阅读了数次，由家喻户晓的**[唐巧](http://blog.devtang.com)**大神编写。

* [iOS 架构模式--解密 MVC，MVP，MVVM以及VIPER架构](http://www.cocoachina.com/ios/20160108/14916.html)

    最近在Cocoa China上发表的一篇译文，笔者之前看过俩次原文，讲的比较形象。

* [MVC，MVP 和 MVVM 的图示](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

    大神**[阮一峰](http://www.ruanyifeng.com/about.html)**的博文，以图形展示的方式使得各层结构更加清晰明了。

* [猿题库 iOS 客户端架构设计](http://gracelancy.com/blog/2016/01/06/ape-ios-arch-design/)

    `猿题库 iOS客户端`开发者**[蓝晨钰](http://gracelancy.com/about/)**的博文，以实际项目`猿题库`详解了架构设计

# UIViewController瘦身

架构模式并不是限制思维，相反应该是发散思维，我们并不应该为了架构而架构，架构应该是服务于我们的代码逻辑，打造更具有扩展性和健壮的代码结构。就比如，大多数开发者都会遇到一个同样的问题，随着项目一天天的壮大，功能越来越多，需求越来越多，而我们的UIViewController也变得越来越臃肿。在上面推荐的博文中，笔者们都或多或少的阐述了如何打造更轻量级的UIViewController，大都列举了一些共性策略:

* 将一个界面中的数据获取抽象成一个类，这里面细分一下，包括了网络请求和数据库缓存，我们可以针对这俩点再次封装成俩个类。

* 将一个界面中的数据处理逻辑抽象成一个类，这里面包含了各种数据转换和算法逻辑，比如数据检索，数据遍历等。

* 将一个界面中数据传递到UIView视图的过程抽象成一个模型类，这里面就包含了对应到UIView视图的每一个数据的传递，比如icon图标，title标题，comment评论内容等。

* 将一个界面中所有展示的UIView视图的添加和渲染抽象成一个类，这里包含了添加控件，自定义动画等。这个对视图的封装仍然可以细分，每一个自定义控件都可以单独封装，因为这样可以完美的在其他的UIViewController达到复用的目的。

而完成了上述抽象之后，就会发现我们需要在UIViewController中完成的工作仅仅是处理视图交互逻辑和数据传递逻辑，这样我们的UIViewController就比较容易维护了。

# Three20架构

每一种框架的兴起和衰落都有其相应的时势和必然性。虽然Three20饱受诟病，早已跌落神坛，但是它的存在是有一定道理的。虽然它在模块之间的耦合度较高，但是个人认为它对UIViewController的抽象和封装也是一个非常好的借鉴。在这里以Three20中对`TTTableViewController`的解耦为例，先上图看一下`TTTableViewController`包含的模块：

{% img /images/Three20.png %}

这里根据上面的结构图具体地解释一下解耦的设计方式。**`TTTableViewController`**的设计遵从了经典的**`MVC`**模式，**`TTModel`**负责数据的获取和处理逻辑，**`TTTableView`**负责视图展示,**`TTTableViewController`**负责**`TTModel`**与**`TTTableView`**之间的通信逻辑和界面的控件添加渲染。而**`TTTableViewController`**在顺应了**`MVC`**模式的前提下，也做了一些扩展，它将**`TTTableViewDatasource`**接收数据传递的逻辑抽象出来封装成了**`TTTableItem`**。而**`TTTableItem`**就是关联**`TTModel`**传递数据的过程，因而我们也可以把这一层称作是**`MVVM`**架构模式中的**`ViewModel`**

根据上面的图示，我们可以看到获取数据的逻辑都在**`TTModel`**中，而且界面控件添加和动画渲染这些逻辑仍然都在**`TTTableViewController`**中，因此我根据大神们的一些建议，对项目中的Three20进行了一下强化，先上图看一下增加的结构：

{% img /images/Three20_Advance.png %}

可以清晰地看到，我将**`TTModel`**中处理缓存数据的逻辑抽象出来，单独放在了**`TTCacheModel`**中，此外还将**`TTTableViewController`**中添加控件和渲染动画的逻辑抽象出来，放到了**`TTViewRender`**中，这样**`TTTableViewController`**就只关心界面交互以及**`TTModel`**和**`TTTableItem`**之间的数据传递逻辑。