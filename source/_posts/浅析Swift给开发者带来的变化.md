---
layout: post
title: 浅析Swift给开发者带来的变化
date: 2016-04-05
description: "简单项目的动画解析"
tags: [Swift]
categories: [Swift]
---

>细数之下，已经有三个月没有写博客做记录了，深深地对自己表示愧疚，之前定下的写作计划，打算将iOS SDK中的framework由浅及深地学习并记录，却由于春节后这次换新东家，一一搁浅了。然而，这次换工作，却也给我带来了意外之喜，那就是我在企业项目开发中，真正地开始使用Swift这门语言。自我感觉，Swift会将我带向一个新的世界。
<!-- more -->

# 前言
Swift，作为苹果设计的开发语言，自2014年WWDC会议上发布之日起，就以安全、灵活、高效定位，吸引了一大批的开发者去学习它的语法、模式和设计理念。尤其是在2015年12月4日，苹果将Swift源码开放给开发者，掀起了又一波的学习热潮。本人成为一枚iOS开发程序猿大概有俩年时间了，作为后知后觉的笨鸟，对Swift也是久慕不已，然而却没有投入更多的时间和精力去学习和了解，而每当在一些博客和论坛中，看到大家发表一些对Swift的褒贬建议、学习心得时，内心也是激动不已。换了新的工作之后，一开始也是在熟悉公司和产品，而新的项目中，底层是基于Swift语音开发的，从而给我创造了一个更深层次了解Swift的契机。这篇文章不会介绍一些Swift的语法糖，而是从设计理念和模式上阐述一下我这几个月学习的心得体会。

# 兼容Objective-C
Swift可以兼容Objective-C语言，在我看来，这是它可以迅速传播起来的一个主要原因。开发者不需要重新创建新项目，旧项目中的组件、第三方库依然可以使用。而苹果为Swift优化了编译器，这使得Swift和OC混编更容易。基本的混编规则就是：
1. Objective-C项目中的.m文件中需要引入Swift类或函数时，只需要在头文件中引入“YourProjectName-swift.h”，这个文件不需要自己手动添加，是编译器自动创建的；
2. Swift项目中的.swift文件中需要引入OC类时，需要创建一个桥接文件，这个文件名称也是可以改变的，只需要在Xcode settings中，添加一下。

![Xcode 设置.png](http://upload-images.jianshu.io/upload_images/1216322-671035ef203e76c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 特性
Swift是一门安全、灵活、高效的编程语言。之所以这么说，是因为在苹果设计Swift之初，就以打造易学、易用并且跨越各种编程风险为目的。举个例子，Swift语言对于访问权限的控制是非常简单和严格的，而且Swift语言对于初始化构造过程控制的十分严格，简单来说初始化原则就是：
1. 初始化路径必须保证对象完全初始化，这可以通过调用本类型的 designated 初始化方法来得到保证；
2. 子类的 designated 初始化方法必须调用父类的 designated 方法，以保证父类也完成初始化。
3. 对于某些我们希望子类中一定实现的 designated 初始化方法，我们可以通过添加 required 关键字进行限制，强制子类对这个方法重写实现。

# 跨平台
Swift语言另一个强大之处是，自Swift2.0之后，它可以在Linux上编译，这让使用Swift编写Server和Android平台应用成为了更大的可能，目前在Linux上开发和学习Swift的趋势已经愈演愈烈。Swift底层使用的是C++编程语言，使得Swift将来的跨平台开发，变成了一种趋势。之前Google之前对外宣布，将会使用Swift开发Android平台的一些组件，而Facebook这些大公司也纷纷加入到此行列之中。

# 编程范式
Swift是一门多范式的编程语言，面向协议，面向对象，函数式编程都在Swift设计理念中体现的非常明显。我在文章开头说了一句，“Swift会将我带向一个新的世界”，也是由此而来。作为一个标准的科班出身的`软件工程`专业学生，自接触编程起，就接触了C语言的面向过程开发、Java语言的面向对象开发。随着学习的不断深入以及在项目中的逐渐成长，慢慢理解了AOP(面向切片编程)和面向接口编程。总的来说，这些编程思想都是特点鲜明独特，各领风骚。而在接触到融合了多范式编程的Swift之后，我也是被苹果架构师们的强悍震慑到了。

>之前一段时间，微博上掀起了对于 `函数式编程`的热议，有褒有贬，更有甚者，从技术交流转成了人身攻击，个人对此是厌恶不已，由此更是暂停了半个月的微博。虽然厌恶此种氛围，但由此也可以看出国内开发者对于各种编程思想的独到见解。

# Swift发展
自苹果发布Swift编程语言，尤其是在开源之后，各大学习网站如雨后春笋般踊跃出来，而一些iOS大牛们更是或独立，或联合企业开始创办`Swfit开发者大会`,`Swift线下交流`,`Swfit学习网站`。而一些具有代表性的技术网站，都为Swift滑立了独立的版块，如[Cocoa China](http://www.cocoachina.com/cms/tags.php?/Swift/)、[Raywenderlich](https://www.raywenderlich.com/category/swift)。一些国内的开发者，纷纷协作交流，通过GitHub，博客网站的形式翻译Swift语言的技术文章。用一句话概括就是，国内企业和开发者对Swift语言是热情高涨的。

# Swift未来
每一门编程语言都会有一个从推出到趋之完善的过程。Swift和Xcode的问题虽然饱受诟病,然而，个人相信，随着时间的推移和语言自身的成长，Swift的将来必定会成为主流开发语言之一。自苹果推出Swift之后，每一次苹果发布会，都会推出Swfit的新版本。而即将到来的2016年WWDC，也是备受瞩目。不仅仅是对iOS 10的预见，更是对Swift 3.0的期待。据说，Swift3.0将会是变化最动荡的一次升级，以后的版本升级都会基于Swift3.0，这也是符合苹果大刀阔斧和快中求稳的风格。而且，随着Swift对各大平台兼容性越来越强，相信会有更多的企业和开发者投入到Swfit中去。