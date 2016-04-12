---
layout: post
title: NSObject的load和initialize方法
date: 2015-09-06
description: "介绍load方法与initialize方法的异同"
tags: [OC基础]
categories: [Objective-C]
---

# 前言
之前在利用`runtime`给自己的class的category动态添加属性方法中，用到了load方法，于是就研究了一下load和initialize俩个方法的异同。在这我也先推荐一篇个人认为总结比较好的一篇博客:[三石·道：NSObject的load和initialize方法](http://www.molotang.com/articles/1929.html)。
<!-- more -->
# 触发阶段
load方法是app在编译阶段就会调用的方法，我们在这里可以做一些额外的初始化工作。而initialize方法是在当前的class第一次调用alloc，才会触发的，有且仅会调用一次。在“三石·道”的这篇博客中也详细地阐述了关于使用load和initialize方法的方式和优缺点，这里也就不在赘述。我推荐这篇博客最关键的一点是它给出了苹果开源地load和initialize方法的源代码，正适合我们加以更深地研究。

# 继承
每一个class都包含一个load和initialize方法，而子类并不会掩盖父类的load和initialize方法。关于这俩个方法在父类与子类的使用方式的异同上，三石·道：NSObject的load和initialize方法这篇博客也作了非常详细地说明。总体而言，子类的load和initialize不必显式调用父类的load和initialize，而父类的load和initialize方法也会被系统调用。而且子类的load和initialize方法调用顺序在父类之后，这一点与传统的其他方法重写有明显区别。

# Category
除了父类与子类的关系，三石·道：NSObject的load和initialize方法这篇博客中也尤为提出了在class的category中使用load方法，我也正是借鉴了这点加以使用。类的category中定义的load方法会在类以及所有父类子类的load方法调用完之后才会被调用。