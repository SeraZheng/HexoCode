---
layout: post
title: iOS实现App之间的内容分享
date: 2015-11-25
description: "介绍如何在iOS系统上实现跨App之间的内容分享"
tags: [UTI]
categories: [iOS]
---

# 前言
我们在iOS平台上想要实现不同App之间的内容分享一般有几种常用方式：

1. 第一种是通过`AirDrop`实现不同设备的App之间文档和数据的分享；
2. 第二种是给每个App定义一个URL Scheme，通过访问指定了URL Scheme的一个URL，实现直接访问一个APP；
3. 第三种是通过`UIDocumentInteractionController`或者是`UIActivityViewController`这俩个iOS SDK中封装好的类在App之间发送数据、分享数据和操作数据；
4. 第四种是通过`App Extension`，在iOS 8的SDK中提供的扩展新特性实现跨App的数据操作和分享；
5. 还有一种集成第三方SDK实现的有限个App的数据分享，比如社交平台(QQ,微信,新浪微博等)给我们提供的官方SDK，或者是集成了多个社交平台的ShareSDK组件和友盟分享组件等。
<!-- more -->
关于集成第三方SDK的使用，各大平台官网上都有详细的文档说明，因此我们这系列文章主要是来谈谈苹果原生提供的基于iOS SDK的分享技术，同时推荐俩篇苹果开发者中心的文档：[Inter-App Communication](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html#//apple_ref/doc/uid/TP40007072-CH6-SW2)和[Document Interaction Programming Topics for iOS](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010409-SW1)。我们的第一篇文章就谈一下如何通过UTI让我们的App支持分享。

# 原理
我在[“详解苹果提供的UTI(统一类型标识符)“](http://www.jianshu.com/p/d6fe1e7af9b6)这篇文章中，详细地讲解了一下`UTI(Uniform Type Identifier)`,一套苹果给我们提供用来在基于Cocoa和Cocoa Touch应用程序中识别实体内容类型的规范，而关于实现内容关联的技术也正是基于这套规范。在iOS和Mac OS开发中，苹果给我们提供了注册文档类型的接口，而这种注册的文档类型是全局的，系统中所有的应用程序和服务都可以侦测到。因此我们通过这个底层侦测，可以使用其他可选的`第三方App`来预览`我们的App`中不支持的文档，而且我们还可以通过这个接口在`我们的App`中打开并处理`第三方App`的文档。

如果我们的App可以处理某些类型的实体内容，那么我们就可以在我们项目中的`Info.plist`文件中进行注册。关于使用哪种类型和UTI，就要参考我在[“详解苹果提供的UTI(统一类型标识符)“](http://www.jianshu.com/p/d6fe1e7af9b6)这篇文章中的讲解。当一个第三方App通过苹果的底层侦测技术检查有哪些App可以处理它所指定的内容类型时，如果我们的App已经注册了这种类型，那么我们的App图标就会显示在其中，并且作为我们自己的App的一个入口。

# 主要技术
主要应用到这种底层侦测的技术有iOS SDK中给我们提供的`UIDocumentInteractionController`、`UIActivityViewController` 和` Quick Look 框架`。此外，在iOS 8中，苹果又给开发者提供了`App Extension`，一种更高大上的方式在App之间的实现分享内容。关于`UIDocumentInteractionController`、`UIActivityViewController`、` Quick Look 框架`以及`App Extension`的细节，我计划在后面的文章中详细讲解。这篇文章，我们主要是来谈谈`如何注册我们App可用的文档类型`以及`简单使用我们的App来处理第三方App分享的内容`。

# 注册可用类型
我们需要在`info.plist`文件中，添加一个新的属性`CFBundleDocumentTypes`(实际上输入的是`"Document types"`)，这是一个数组类型的属性，意思就是我们可以同时注册多个类型。而针对数组中的每一个元素，都有许多属性可以指定，详细的属性列表我们可以从官方文档上找到: [Core Foundation Keys ---- CFBundleDocumentTypes](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/20001431-101685)。这里列举我们在做iOS开发时常用的属性：

* CFBundleTypeName(`"Icon File Name"`)

字符串类型，指定某种类型的别名，也就是用来指代我们规定的类型的别称，一般为了保持唯一性，我们使用UTI来标识。

* CFBundleTypeIconFiles
数组类型，包含指定的png图标的文件名，指定代表某种类型的图标，而图标有具体的尺寸标识：

|Device |Sizes     |
|:------|:---------|
|iPad     |64 x 64 pixels, 320 x 320 pixels|
|iPhone and iPod touch|22 x 29 pixels, 44 x 58 pixels (high       resolution)|

* LSItemContentTypes(`"Document Content Type UTIs"`)

数组类型，包含UTI字符串，指定我们的应用程序所有可以识别的类型集合

* LSHandlerRank(`"Handler rank"`)

字符串类型，包含`Owner`,`Default`,`Alternate`,`None`四个可选值，指定对于某种类型的优先权级别，而`Launcher Service`会根据这个优先级别来排列显示的App的顺序。优先级别从高到低依次是`Owner`，`Alternate`,`Default`。`None`表示不接受这种类型。

了解了这些基本属性，我们就需要在注册App可用类型时，指定这些属性，根据每个项目的需求不同，属性值也不同。具体的注册请参照我的GitHub上的项目：[SeraZheng---ZSUTITest](https://github.com/SeraZheng/ZSUTITest/tree/master)。下图示例作为一个参照：
{% img /images/在info中添加DocumentTypes.png %}

而当我们添加完所有属性后，开始运行我们的程序，然后再回到我们的Info界面，就会看到`Document types`这个列表已经发生了变化，这就证明我们成功的注册好了App可用的类型。
{% img /images/注册成功.png %}

# 打开第三方应用
我们在上面的步骤中注册好了我们的App可以识别的类型，现在我们可以打开一个使用`UIDocumentInteractionController`或者是` Quick Look `框架来展示内容的第三方App，这里以iPhone 上的QQ程序为例。

我们在上面的注册步骤中，注册的`LSItemContentTypes`仅包含了`public.image`这个UTI。所以我们先从QQ应用程序的`我的文件`中，打开不同类型的文件进行对比，大家可以看下图`我的文件`列表中包含俩种类型的文件，一种是`.jpg`扩展名的图片文件，一种是`.pdf`扩展名的文档文件。

{% img /images/我的文件列表.png %}

当我打开一个图片文件进行预览时，点击`其他应用打开`，就可以在App列表中看到我们的App图标。简单介绍一下这个页面，第一行是苹果在iOS 7之后给我们提供的使用`AirDrop`在`iPhone`、`iPad`或`iPod Touch`设备之间通过`iCloud`共享内容的一种方式。第二行是通过文档类型关联技术识别的App的列表。第三行是通过文档关联技术识别的`Action`的列表，`Action`表示对文档可进行的操作，如复制，打印等。
{% img /images/打开图片看到图标.png %}

而如果我打开PDF文件的话，就看不到我们的App图标。
{% img /images/PDF看不到图标.png %}

# 程序回调
当我们通过上面步骤，成功地显示了`ZSUTITestDemo` 的图标之后，点击图标，我们就可以跳转到`ZSUTITestDemo`应用中，而苹果在iOS SDK中给我们提供的接收回调的方法在iOS 9之后做出了改变，因此我们需要针对不同的设备版本做出改变：

{% codeblock lang:objc %}
#if __IPHONE_OS_VERSION_MAX_ALLOWED < __IPHONE_9_0
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(nullable NSString *)sourceApplication annotation:(id)annotation
{
    UINavigationController *navigation = (UINavigationController *)application.keyWindow.rootViewController;
    ViewController *displayController = (ViewController *)navigation.topViewController;
    
    [displayController.imageView setImage:[UIImage imageWithData:[NSData dataWithContentsOfURL:url]]];
    [displayController.label setText:sourceApplication];
    
    return YES;
}

#else
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(nonnull NSDictionary<NSString *,id> *)options
{
    UINavigationController *navigation = (UINavigationController *)application.keyWindow.rootViewController;
    ViewController *displayController = (ViewController *)navigation.topViewController;
    
    [displayController.imageView setImage:[UIImage imageWithData:[NSData dataWithContentsOfURL:url]]];
    [displayController.label setText:[options objectForKey:UIApplicationOpenURLOptionsSourceApplicationKey]];
    
    return YES;
}
#endif
{% endcodeblock %}

Demo示例可以从GitHub项目上参照代码：[SeraZheng---ZSUTITest](https://github.com/SeraZheng/ZSUTITest/tree/master)。当点击`ZSUTITestDemo`程序图标回到调用代码中，我们可以在这里做各种我们想做的事，如上传图片、预览图片、操作图片等等。我只对图片做了简单的预览显示，然后显示文件的源程序的`Bundle Identifier`，示例如下图：
{% img /images/显示图片.png %}
