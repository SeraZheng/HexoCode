---
layout: post
title: ZSNavigationFilterMenuView动画解析 --- 基于navigationBar的titleView创造
date: 2015-11-05
description: "简单项目的动画解析"
tags: [CoreAnimation]
categories: [iOS]
---

# 前言
之前学习了一些绘图的方式和动画的制作，于是简单地制作了一个下拉列表，我把它定位于navigationBar的titleView主要是因为基于新浪微博的Groups分组，新创的一种类似于UIActionSheet的展现方式，至于用法方面，在GitHub上可以完全看到：[SeraZheng的ZSNavigationFilterMenuView](https://github.com/SeraZheng/ZSNavigationFilterMenuView)。
<!-- more -->
这篇文章，主要是想写一些我在制作过程中用到的一些知识，还有一些想法和感受。先上图，下面是一个没有使用icon的简单效果，具体效果可以去GitHub看。
{% img /images/Animation_Title.gif %}

# 设计思路
首先，我自定义的ZSNavigationFilterMenuView是继承自UIButton的，这样我可以直接设置title和icon，免去了大部分的工作，所以在使用我自定义的ZSNavigationFilterMenuView的时候，一些UIButton的属性及方法都可以直接使用，很方便根据自己不同的需求定制个性化内容。

我使用了tableView作为主要的展示，这样有利于我定制自己的内容，同时也很方便后期的扩展。上图中的图标，都是我使用CoreGraphics画出来，以达到一些练手的目的，但是在真正的开发中，还是要直接使用现成的图片，这样可以提升很大的性能。而关于我选择的展示动画，主要是没有什么设计上的灵感，在Dribble和Capptivate上搜着一些相关内容，并没有很大的启发，因此这里暂时决定了俩种简单动画。

# 动画

## 1.UIView

其他的内容都是一些简单的内容，这里主要讲一下，我制作动画时使用到的一些知识。Apple在SDK中为我们封装好了一些直接使用的API，可以达到我们想要的简单动画的效果。其中我们可以直接使用UIView的动画，主要有三种方式。

第一种最基础的方式就是使用UIView 的扩展UIViewAnimation提供的方法：
{% codeblock lang:objc %}
[UIView beginAnimations:@"_bodyView" context:nil];

[UIView setAnimationDuration:0.5]; //设置动画延时

[UIView setAnimationRepeatCount:1]; //设置动画重复次数

[UIView setAnimationCurve:UIViewAnimationCurveLinear]; //设置动画曲线

[UIView setAnimationDelay:0.f]; //设置动画延迟

[UIView setAnimationRepeatAutoreverses:YES]; //设置动画重复时以动画形式从终点回到起点

[_bodyView setBackgroundColor:ZSSTYLEVAR(bodyViewHighlightedColor)];

[_bodyView.layer setBorderWidth:0.f];

[UIView commitAnimations];
{% endcodeblock %}

第二种比较简单的使用方式就是使用UIView的扩展UIViewAnimationWithBlocks提供的方法：
{% codeblock lang:objc %}
[UIView animateWithDuration:0.5 delay:0.f options:UIViewAnimationOptionAutoreverse animations:^(){

           [_bodyView setBackgroundColor:ZSSTYLEVAR(bodyViewHighlightedColor)];

           [_bodyView.layer setBorderWidth:0.f];

} completion:NULL];
{% endcodeblock %}

第三种就是使用UIView的扩展UIViewKeyframeAnimations提供的创建关键帧动画的方法：
{% codeblock lang:objc %}
[UIView animateKeyframesWithDuration:0.5 delay:0.f options:UIViewKeyframeAnimationOptionAutoreverse | UIViewKeyframeAnimationOptionCalculationModeLinear animations:^(){

          [_bodyView setBackgroundColor:ZSSTYLEVAR(bodyViewHighlightedColor)];

          [_bodyView.layer setBorderWidth:0.f];

} completion:NULL];
{% endcodeblock %}

关于上面三种的使用方式是大同小异，个人倾向于简单的动画使用block的方式，因为这不仅减少了我们的代码量，而且可读性也比较好。UIView的扩展UIViewAnimationWithBlocks提供了其他一些让我们可以直接创建动画效果的方法，比如可以直接创建弹簧动画效果，而我在展现tableView时，使用到的也是这种效果。

## 2.CoreAnimation

底层的实现动画的方式，就是利用QuartzCore框架，通过CAAnimation来达到我们想要的动画效果。QuartzCore框架也比较简单，主要提供了关于CALayer,CAAnimation的个性化定制的使用方法。
{% codeblock lang:objc %}
CGPoint viewPosition = self.contentView.layer.position;

_shakeAnimation = [CABasicAnimation animationWithKeyPath:@"position"];

[_shakeAnimation setTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionDefault]]; //设置动画曲线效果

[_shakeAnimation setFromValue:[NSValue valueWithCGPoint:CGPointMake(viewPosition.x - 10, viewPosition.y)]]; //设置起始值

[_shakeAnimation setToValue:[NSValue valueWithCGPoint:CGPointMake(viewPosition.x + 10, viewPosition.y)]]; //设置终点值

[_shakeAnimation setAutoreverses:YES]; //设置以动画方式回到起始值

[_shakeAnimation setRepeatCount:2]; //设置动画重复次数

[_shakeAnimation setDuration:0.05]; //设置动画时间

[self.contentView.layer addAnimation:_shakeAnimation]; //添加动画到layer
{% endcodeblock %}
关于动画基础和动画原理，我推荐一篇非常棒的博客：[objc系列译文（12.1）：动画解释](http://blog.jobbole.com/69111/)。

# 贝塞尔曲线
谈到动画，不得不说的就是贝塞尔曲线，堪称是制作精美动画效果的基础法则。苹果给我们提供了一个非常好的使用贝塞尔曲线的封装：UIBezierPath。而关于UIBezierPath的基础使用，我推荐一篇比较详细的博客：[标哥的技术博客--UIBezierPath精讲](http://www.henishuo.com/uibezierpath-draw/)。

# 反馈
关于动画的基础原理甚至是贝塞尔曲线得原理及使用，我们都可以在维基百科上搜到很好的教程。想学习的朋友可以自行查阅。关于项目中的动画的设计效果，暂时没有什么灵感，如果在Dribble和Capptivate上找到了动画效果和灵感，会及时的更新项目和这篇文章，也希望如大家有什么好的动画效果和其他方面的建议，可以多多评论给我！