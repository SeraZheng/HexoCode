---
layout: post
title: 自动注释VVDocumenter-Xcode和插件管理Alcatraz
date: 2015-07-26
tags: [注释]
categories: [Xcode]
---

# 前言　
在上一篇中，我记录了对于代码注释的一些研究，然后发现有一些`第三方工具`和`Xcode插件`可以帮助我们自动地添加代码注释，这里就介绍一种Xcode的插件`VVDocmenter-Xcode`。
>引用一下官方的语言，VVDocmenter-Xcode是可以帮助你自主添加生成与appledoc、Doxygen和HeaderDoc这三种文档生成工具都匹配的注释的Xcode插件。而且VVDocmenter-Xcode也是在GitHub上开源的，因此它具有很好的活跃度。
<!-- more -->
# VVDocmenter-Xcode安装
`VVDocmenter-Xcode`支持`Xcode 5`以后的版本，而谈到`VVDocmenter-Xcode`的安装，官方给出了俩种方法，第一种是git clone命令从`GitHub`上将`VVDocmenter-Xcode`项目拷贝到本地，然后在本地build以后重启Xcode，之后你就可以在你自己的项目中使用它。

而`VVDocmenter-Xcode`的使用方法也比较简单，在你想要添加注释的位置直接输入三个“/”，`VVDocmenter-Xcode`就可以帮助你自动识别你想添加的注释的类别，然后生成注释，你只需要将自己的描述加进去就可以了。在这里我们就不在赘述了，官方给出了很好的使用指南。我们在这里重点地讲一下第二种安装方法，就是使用`Alcatraz`。

# Alcatraz  
`Alcatraz`是一个开源的`Xcode包管理助手`，集成了一些可以安装在Xcode内，提高我们开发效率的`插件`、`模板`和`配色方案`。`Alcatraz`也是支持Xcode 5以后的版本，并且需要在Mac　OS 10.9以后的版本才可以正常使用。而关于`Alcatraz`的安装，在[GitHub----Alcatraz](https://github.com/supermarin/Alcatraz)和[Alcatraz](http://alcatraz.io/)官方网站上都有详细的介绍。
　
# Alcatraz安装
{% codeblock lang:bash %}
curl -fsSL https://raw.github.com/supermarin/Alcatraz/master/Scripts/install.sh | sh
{% endcodeblock %}

# Alcatraz卸载
{% codeblock lang:bash %}
rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin
{% endcodeblock %}

# 插件卸载
卸载通过Alcatraz安装的插件、模板和配色方案：
{% codeblock lang:bash %}
rm -rf ~/Library/Application\ Support/Alcatraz/
{% endcodeblock %}

# 展示
安装成功之后，我们就可以正常使用Alcatraz来安装VVDocmenter-Xcode啦！！！
{% img /images/Alcatraz.png %}