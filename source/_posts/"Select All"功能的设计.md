---
layout: post
title: Select All功能的设计
date: 2015-09-27
description: "介绍Select All功能的一些设计方式"
tags: [设计]
categories: [设计]
---

# 前言
在形形色色的APP设计中，`“Select All“`功能得细节设计尤为不同。移动端的设计精髓就在于将主要的功能完美得展示给用户，而不能给用户带来使用的复杂度和概念上得困惑。大多数的App将`"Select All"`这个feature与`分页功能`排斥展示，这就是细节上的体验，因而在大数据量的页面中，正常人并不会加入`"Select All"`这个feature。
<!-- more -->
# 按钮式设计
在添加"Select All"这个feature到页面之后，就要考虑展示方式和逻辑。比如，一些app将”Select All“与”Deselect All“设计成了俩个相互转换的tab,例如下面这俩种，完全展示了"Select All"与"Deselect All"的直接操作与处理。
{% img /images/SelectAll_Button.gif %}

# Checkbox式设计
同时也有其他的一些app,并没有展现"Select All"或者是"Deselect All"这样的字眼，而是通过形象地图标示例，指导用户操作，经典地就如使用checkbox，做到和web端"Select All"的效果。如下图所示：
{% img /images/SelectAll_CheckBox.png %}

# 总结
就我个人而言，无论是哪种设计方式，都要结合自己项目的特点，发挥自己项目的优势，展现项目的风格，而最首要地就是要迎合客户的需求，因为不管你做的多么精美漂亮，如果用户感觉华而不实的话，那也不能算是过关的设计。个人作为一个开发人员，未入门的设计人员，只是浅谈一下细节设计，发表一些自己的感慨，无伤大雅。