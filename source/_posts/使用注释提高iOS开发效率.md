---
layout: post
title: 使用注释提高iOS开发效率
date: 2015-07-25
tags: [注释]
categories: [Xcode]
---

# 前言　　
新手在开始参与一个开发项目的时候，会把大部分的时间耗费在阅读项目的`需求文档`、`开发文档`和`代码`，一篇好的`需求文档`和`开发文档`会帮助新手很快的理解项目的目标和进度，而新手对于代码的阅读会先从代码的注释开始。拥有良好`注释`的代码可以省去团队其他的开发者好多时间，不至于让其他参与者去一行一行的阅读代码，去不断地加断点查看代码地跳转逻辑，接下来我们就谈谈iOS开发中使用的一些注释。
<!-- more -->
# \#pragma mark
确切地说，这是Xcode编译器特定得编译命令，它的作用就是在代码地编辑器中，将顶部的方法函数弹出菜单按层次分开，方便于我们的查找。一般的使用方法是在想要分层的第一个方法或函数上面加上`#pragma mark -`　和`#pragma　mark something`(你的分层定义)。此外其他常用到的，是在我们想要标识代码的地方加上`#warning`，这样运行时编译器会自动帮助我们将代码标识到`issue　navigator`。

# 自定义标示
使用自定义的特殊标识符，例如`//TODO:`或者是`//FIXME:`。使用这一类的特殊标识符，首先需要在`Xcode`中添加支持，在我们的`Target`中，选择`Build Phases`，Xcode 6将`Build Phases`从`Build Settings`分离了出来。在`Build Phases`选择`New Run Script Phase`，然后输入：
{% codeblock lang:bash %}
 KEYWORDS="TODO:|FIXME:"
 find "${SRCROOT}"  −name"∗.h"−or−name"∗."        -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($KEYWORDS).*\$" | perl -p -e "s/($KEYWORDS)/ warning: \$1/"
{% endcodeblock %}  
 　
由于本人对脚本语言不熟悉，想要深入研究的朋友们可以从网上搜索教程。在添加了上面的支持后，你就可以在代码中通过使用自定义的标识符来快速查找代码。
     
# 代码注释
上面的俩种可以算是编译器识别的命令，下面我们来说一下传统意义上的代码注释。不管在使用哪种语言，我们一般都会使用到代码注释，而不同的语言有不同的注释规范，例如一般的单行注释，我们会使用`“//”`，而多行注释则会使用`"/*......*/"`，而对于iOS开发者来说，代码注释也拥有属于它自己的一套规范。下面就是一些常用的iOS的代码注释，这些衍生的注释目的是方便文档生成：   
{% codeblock lang:objc %}   
/// Single line comment.（单行文本）
/// Single line comment spreading （多行文本）
     /// over multiple lines.
/** Single line comment. */ （单行文本）
/** Single line comment spreading （多行文本）
       * over multiple lines.
       */
/** Single line comment spreading （多行文本）
       over multiple lines. No star.
       */
/*! Single line comment. */ （单行文本）
/*! Single line comment spreading （多行文本）
       over multiple lines.
       */
{% endcodeblock %}

# 方法注释
在iOS开发代码中有一种对方法的注释 ，其中包含了特有的指令：
{% codeblock lang:objc %}
  /**
    * @brief 带字符串参数的方法.（具体描述）
    * @param  value 值.（参数描述）
    * @return 返回value.（返回值描述）
    * @exception NSException 可能抛出的异常.（抛出异常描述）
    * @see someMethod （关联描述）
    * @warning 警告: appledoc中显示为蓝色背景, Doxygen中显示为红色竖条.（警告描述）
    * @bug 缺陷: appledoc中显示为黄色背景, Doxygen中显示为绿色竖条.（缺陷描述）
    */
{% endcodeblock %}

# 生成开发文档
说起上面的这种iOS开发中的代码注释，就不得不说一下iOS开发中的文档生成，一个良好的代码文档可以帮助其他开发者很好的了解代码结构和具体内容。而时下比较流行的几个文档生成工具有下面几种，关于iOS代码文档的具体生成，我推荐朋友们观看这篇 [唐巧的技术博客-------使用Objective-C的文档生成工具](http://blog.devtang.com/blog/2012/02/01/use-appledoc-to-generate-xcode-doc/)。

1. 第三方工具[Doxygen](http://www.stack.nl/~dimitri/doxygen/index.html)
2. Xcode自带的[HeaderDoc](http://developer.apple.com/opensource/tools/headerdoc.html)
3. 默认与苹果官方文档风格类似的[AppleDoc](http://gentlebytes.com/appledoc/)