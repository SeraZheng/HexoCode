---
layout: post
title: 使用谓词(NSPredicate)来提高集合遍历与过滤查找的效率
date: 2015-10-12
description: "介绍'NSPredicate'功能的一些设计方式"
tags: [OC基础]
categories: [Objective-C]
---

# 前言
在开发中，我们经常会遇到一些需要，让我们从集合中查找某个值，从集合中过滤想要的内容等等，因而我们就需要`遍历`集合，加条件判断，然后获取符合条件的值。而关于`集合的遍历`是所有软件开发从业人员经常打交道的一些事情。

把范围缩小到iOS开发中，关于集合地遍历的方法就有好多种，人们一直在讨论和争辩，想寻找出一种最快最有效的方法，是用`for循环`，还是`block`，是用`并发操作`，还是`顺序操作`，等等。甚至有人不惜使用`大数据量`来测试各种遍历方式的`效率`以及`精确度`。

>然而我认为寻找并选择一种自己认为合适的操作是最好的，简单地几个数据的集合，就用到普通的for循环，基于大数据量的遍历就需要用到并发操作。
<!-- more -->
# NSPredicate
而我并不会在这里展示如何遍历集合，而是提示一种在iOS开发中，用一种类似于SQL语句来过滤集合内容的方式从而避免了自己进行集合遍历的方法，就是`NSPredicate`。苹果在Cocoa touch框架给我们提供了`NSPredicate`这个类，封装了一些让我们可以直接对集合设置过滤条件的方法，而至于苹果是如何在SDK中进行数据查找地，我们并不需要关心，因为我相信它做的一定比我们好。学过`SQL语法`的人，使用`NSPredicate`会十分容易。我会在下面的内容中详细的讲述`NSPredicate`的语法规则。

# 符号表达式
如<, >, == , !=, 等等这些数学符号表达式，在NSPredicate的format中依然有效
{% codeblock lang:objc %}
NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"SELF > 10"];
{% endcodeblock %}
"SELF"代表的时集合中的对象本身，此时集合对象是整型数据，在iOS中的集合可以是nil之外的任何数据类型。

# 范围表示
如`IN`,`BETWEEN`等等这种代表范围区间的格式字符串，可以形象地称之为关键字
{% codeblock lang:objc %}
NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"age BETWEEN {1,5}"];
{% endcodeblock %}
"age"代表了集合中对象的一个实例属性，此时集合中的对象是一个个的实体。

# 字符串区间
如`BEGINSWITH`，`ENDSWITH`，`CONTAINS`，顾名思义，我们可以很容易理解他们的过滤条件
{% codeblock lang:objc %}
NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name CONTAINS[cd] %@",text];
{% endcodeblock %}
在格式化语言中，我们仍然可以自如地使用”%@“等符号表示变量。[cd]中的c表示不区分大小写，d表示不区分发音符号。
 
# 通配符
如`LIKE`,这些与SQL语义中的关键字定义十分相像。
{% codeblock lang:objc %}
NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name LIKE[cd] '*er'"];
{% endcodeblock %}
在NSPredicate格式串中，是自动给字符串加上引号的，所以我们自定义的字符串必须加上引号（单/双）
 
# 正则匹配
如`MATCHES`，诸如其他的查找语言，都是需要匹配正则表达式的
{% codeblock lang:objc %}
NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name MATCHES 'Z.+e$'"];
 {% endcodeblock %}

# 组合查询
如`AND`，在设置过滤条件时，可能单一条件并不能满足我们的需要，所以我们就需要设置组合条件
{% codeblock lang:objc %}
NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name LIKE[cd] '*er'" AND age > 10];
{% endcodeblock %}