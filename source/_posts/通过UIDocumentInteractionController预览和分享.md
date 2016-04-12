---
layout: post
title: 通过UIDocumentInteractionController预览和分享
date: 2015-12-05
description: "介绍UIDocumentInteractionController以及如何在iOS系统上实现跨App之间的内容分享"
tags: [UTI]
categories: [iOS]
---

# 前言
朋友分享推荐给我一本PDF格式的`史蒂夫•乔布斯传`，阅读了几篇，很受感触，于是想把他分享给大家欣赏阅读。早起闲来无事，正好就接着写篇文章来分享一下！我在“[iOS实现App之间的内容分享](http://www.jianshu.com/p/88a08d66894f)”这篇文章中详细讲解了通过注册UTI的方式让我们的App支持分享，也简单地说了一下App内部怎么处理分享。同时，我也指出了在iOS系统跨App分享内容的几种常用技术，比如`URL Scheme`,`AirDrop`, `UIDocumentInteractionController`,`UIActivityViewController`这几种。这一篇文章，我们来谈一下最基础的原始方法，怎么通过使用`UIDocumentInteractionController`来预览、操作和分享`史蒂夫•乔布斯传`。
<!-- more -->
# 简介
从iOS SDK的API文档中，我们可以找到`UIDocumentInteractionController`的声明:
{% codeblock lang:objc %}
NS_CLASS_AVAILABLE_IOS(3_2) __TVOS_PROHIBITED @interface UIDocumentInteractionController : NSObject <UIActionSheetDelegate>
{% endcodeblock %}

由此声明我们可以得知，`UIDocumentInteractionController`是从iOS 3.2的SDK开始支持的，它是直接继承的`NSObject`，而不是我们想象的`UIViewController`，因此我们需要使用`UIDocumentInteractionController`提供的方法来展示它，而且我们还可以看出它是不能在Apple TV 的开发中使用的。遍观`UIDocumentInteractionController`的属性和方法可以看出，`UIDocumentInteractionController`主要给我们提供了三种用途，我会在下面的内容中逐条的讲解`UIDocumentInteraction`的每一种用途的具体使用：

1. 展示一个可以操作我们分享的文档类型的第三方App列表
2. 在第一条展示列表的基础上添加额外的操作，比如`复制`，`打印`，`预览`，`保存`等。
3. 结合`Quick Look`框架直接展示文档内容

# 准备阶段
首先我创建了一个新的应用方便演示和截图，我把它命名为`ZSDocumentInteractionTest`，然后拖入PDF格式的`史蒂夫•乔布斯传`到`ZSDocumentInteractionTest`项目的bundle中。然后在`Storyboard`的`ViewController`中添加了一个Button作为`UIDocumentInteractionController`的触发操作(这些操作都比较简单，就不在这里用图展示啦)。运行程序，我们就可以看到Button啦，截图如下。然后我们就可以在Button的触发方法中，操作`UIDocumentInteractionController`来显示或者分享我们的`史蒂夫•乔布斯传`啦，具体的应用详情可以参考GitHub上的Demo：[ZSDocumentInteractionTest](https://github.com/SeraZheng/ZSDocumentInteractionTest)。

{% img /images/显示Button.png %}

# 初始化
不管我们使用哪种`UIDocumentInteractionController`的展示方式和用途，都需要给`UIDocumentInteractionController`指定文档的URL，所以我们通常使用下面的初始化方式，给`UIDocumentInteractionController`指定`文件的URL`。
{% codeblock lang:objc %}
- (IBAction)presentPDFDocumentInteraction:(id)sender {
    UIDocumentInteractionController *documentController = [UIDocumentInteractionController interactionControllerWithURL:[[NSBundle mainBundle] URLForResource:@"Steve" withExtension:@"pdf"]];
}
{% endcodeblock %}

# 展示第三方App列表
我们先实现`UIDocumentInteractionController`的第一个用途，展示可以操作PDF文件的第三方App列表。我们需要使用`UIDocumentInteractionController`提供的方法：
{% codeblock lang:objc %}
- (BOOL)presentOpenInMenuFromRect:(CGRect)rect inView:(UIView *)view animated:(BOOL)animated;
{% endcodeblock %}

我在Button的触发方法中添加下面的代码,意思就是让`UIDocumentInteractionController`的View在当前控制器视图上显示：
{% codeblock lang:objc %}
    [documentController presentOpenInMenuFromRect:self.view.bounds inView:self.view animated:YES];
{% endcodeblock %}

运行程序，点击Button，我们可以开始第一次展示测试啦。

# 第一次展示测试
一切准备就绪之后，我开始进行`UIDocumentInteractionController`的测试，点击Button，就可以看到下面的界面啦。这说明我们的第一步成功了!!(真棒)

{% img /images/展示图标.png %}

简单介绍一下这个界面，这个视图中的第一行列表显示`AirDrop`，是苹果在`iOS 7`提供的一种跨设备分享的技术，我会在后边的文章中讲解。视图中的第二行列表就是整个iOS系统中，可以操作PDF文档的应用程序列表，还包括了苹果在`iOS 8`提供的`Share Extension`图标，关于`Share Extension`，我会在后边的文章中讲解。视图中的第三行列表，就是现实设备可选的操作，如`Copy`,`Print`中，这里什么操作都没有，并不是说没有可执行的操作，而是我们没有让他显示出来。

接着我试着点击QQ图标，打算把`史蒂夫•乔布斯传`分享给我的好友，然而意外发生了，`ZSDocumentInteractionTest`崩溃掉啦，而且还给出我们一段错误提示：
{% codeblock lang:bash %}
2015-12-30 19:00:40.078 ZSDocumentInteractionTest[1254:344240] *** Assertion failure in -[_UIOpenWithAppActivity performActivity], /BuildRoot/Library/Caches/com.apple.xbs/Sources/UIKit/UIKit-3512.29.5/UIDocumentInteractionController.m:408
2015-12-30 19:00:40.079 ZSDocumentInteractionTest[1254:344240] *** Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'UIDocumentInteractionController has gone away prematurely!'
*** First throw call stack:
(0x248e185b 0x35fa2dff 0x248e1731 0x25672ddb 0x290638c9 0x292695bb 0x28d5aefd 0x28d5e1a1 0x28b42107 0x28a50a55 0x28a50531 0x28a5042b 0x282e05cf 0x1acd03 0x1b17c9 0x248a4535 0x248a2a2f 0x247f50d9 0x247f4ecd 0x2db6aaf9 0x28a7e2dd 0x780ad 0x366f0873)
libc++abi.dylib: terminating with uncaught exception of type NSException
{% endcodeblock %}

我看到错误提示竟然指向了`UIDocumentInteractionController.m`文件，而且错误提示是`NSInternalInconsistencyException`(内部不一致)和"UIDocumentInteractionController has gone away prematurely!"(UIDocumentInteractionController过早地被释放掉啦)。由此我想出这个应该是内存过早释放的一个错误，然后我查阅了一下Apple Developer上的文档，原来，在ARC环境下展示`UIDocumentInteractionController`时，当我的函数方法调用完毕，退栈之后，`UIDocumentInteractionController`的实例就被释放掉了，展示出来的这个View由`Quick Look`框架来操作，并不会对`UIDocumentInteractionController`产生引用。当点击View上面的Button时，内部操作仍然会继续访问这个`UIDocumentInteractionController`实例，就会报出上述错误。

错误原因找到了，那么解决原理也就清楚了，只要不让`UIDocumentInteractionController`实例过早释放就可以啦。我们可以将`UIDocumentInteractionController`声明为一个`strong`类型的实例属性，然后修改一下Button触发方法就可以啦。(仍然不理解的朋友可以去GitHub上下载Demo测试)
{% codeblock lang:objc %}
@interface ViewController ()
@property (nonatomic, strong) UIDocumentInteractionController *documentController;
@end
{% endcodeblock %}

我在Button的触发方法中添加下面方法的调用,为了方便区分和理解，我把代码封装成了私有实例方法：
{% codeblock lang:objc %}
- (void)presentOpenInMenu
{
    // display third-party apps
    [self.documentController presentOpenInMenuFromRect:self.view.bounds inView:self.view animated:YES];
}

- (IBAction)presentPGNDocumentInteraction:(id)sender {
    _documentController = [UIDocumentInteractionController interactionControllerWithURL:[[NSBundle mainBundle] URLForResource:@"Steve" withExtension:@"pdf"]];
    [self presentOpenInMenu];
}
{% endcodeblock %}

修改完之后，运行程序，然后点击Button，看到第一次测试时展示出来的图片啦。然后再点QQ图标，就可以正确地跳转到QQ程序中，选择好友就可以分享`史蒂夫•乔布斯传`啦。（QQ接收分享页面就不展示了，想试验的可以手动测试下)

# 展示可选操作
我们可以看到第一步图示里面只有App图标，第二行操作列表中只有一个`More`。所以我们来展示`UIDocumentInteractionController`的第二种用途，在第一步的基础之上，显示附加的操作选项，。这需要我们使用`UIDocumentInteractionController`提供的另外一种展示方法：
{% codeblock lang:objc %}
- (BOOL)presentOptionsMenuFromRect:(CGRect)rect inView:(UIView *)view animated:(BOOL)animated;
{% endcodeblock %}
我们在Button的触发方法中添加下面方法的调用：
{% codeblock lang:objc %}
- (void)presentOptionsMenu
{
    // display third-party apps as well as actions, such as Copy, Print, Save Image, Quick Look
    [_documentController presentOptionsMenuFromRect:self.view.bounds inView:self.view animated:YES];
}
{% endcodeblock %}
运行程序，点击Button,我们可以看到下面的界面，多了`Copy`和`Print`的操作。`Copy`操作可以将文件拷贝到系统粘贴板中，而`Print`操作则是关联打印机进行打印操作的。（在这里我就不展示这俩种操作的具体界面啦！）

{% img /images/附加操作.png %}

如果`UIDocumentInteractionController`关联的是一个图片文件，这个界面还会提供一个`Save Image`的操作，用来直接保存图片到系统的`Photos`中，此外这个界面还提供了一个`Quick Look`操作，可以让我们直接预览`乔布斯自传`PDF文档，只不过需要我们再多写点代码，为了文章的合理性和结构性，我决定在下面的标题内容中讲解。(先卖个小关子！！)

# 直接预览
`UIDocumentInteractionController`第三种预览文档内容的用途非常重要，而且也是常见的。我会详细地说一下如何通过`UIDocumentInteractionController`实现预览`史蒂夫•乔布斯传`。首先你需要为`UIDocumentInteractionController`指定一个delegate，并且实现下面的代理方法：
{% codeblock lang:objc %}
- (UIViewController *)documentInteractionControllerViewControllerForPreview:(UIDocumentInteractionController *)controller;
{% endcodeblock %}

这个代理方法主要是用来指定`UIDocumentInteractionController`要显示的视图所在的父视图容器。这样`UIDocumentInteractionController`才清楚在哪里展示`Quick Look`预览内容， 我在这里就指定Button所在的UIViewController来做`UIDocumentInteractionController`的代理对象，并且实现上面的代理方法。在Button的触发方法中添加下面的代码

{% codeblock lang:objc %}
_documentController.delegate = self;
{% endcodeblock %}
然后实现代理方法：
{% codeblock lang:objc %}
- (UIViewController *)documentInteractionControllerViewControllerForPreview:(UIDocumentInteractionController *)controller
{
    return self;
}
{% endcodeblock %}
`UIDocumentInteractionController`是继承自`NSObject`的，因而为了能够实现直接预览，我们需要用到`UIDocumentInteractionController`提供的展示预览的方法，
{% codeblock lang:objc %}
- (BOOL)presentPreviewAnimated:(BOOL)animated;
{% endcodeblock %}
这个方法是以模态窗口通过Quick Look框架全屏显示PDF的内容，所以我们在Button的触发方法中添加下面方法的调用：
{% codeblock lang:objc %}
- (void)presentPreview
{
    // display PDF contents by Quick Look framework
    [self.documentController presentPreviewAnimated:YES];
}
{% endcodeblock %}
然后运行程序，点击Button，弹出了一个新视图，可以看到`史蒂夫•乔布斯传`的内容，如下图
{% img /images/直接预览.png %}

# 展示预览操作
通过上面的操作我们就可以欣赏阅读我们想看的`史蒂夫•乔布斯传`啦，不过别忘记我们上面还卖了一个小关子，就是在展示可选操的时候，除了`Copy `，`Print`，其实我们还可以展示`Quick Look`这个预览操作。为什么我要卖关子呢，因为我是一个相信因果循环的人，我组织文章的逻辑是由浅入深，我设想通过一步步铺垫来展开`UIDocumentInteractionController`所有特性。

好啦，回归正题！我们想要实现显示`Quick Look`预览操作，其大部分的工作在`直接预览`这一小节中都做完了，比如指定代理对象，然后实现这个代理方法来指定`UIDocumentInteractionController`的父视图容器：

{% codeblock lang:objc %}
- (UIViewController *)documentInteractionControllerViewControllerForPreview:(UIDocumentInteractionController *)controller;
{% endcodeblock %}

由于我们已经做完了所有准备，在这一步，我们只需要将直接展示`史蒂夫•乔布斯传`内容的方法替换为下面这段，展示`可选操作列表`的方法,就可以啦！
{% codeblock lang:objc %}
- (void)presentOptionsMenu
{
    // display third-party apps as well as actions, such as Copy, Print, Save Image, Quick Look
    [_documentController presentOptionsMenuFromRect:self.view.bounds inView:self.view animated:YES];
}
{% endcodeblock %}
然后我们运行程序，点击Button，就可以看到`Quick Look`操作已经显示出来啦！如下图：
{% img /images/QuickLook.png %}

如果我们点击这个`Quick Look`操作，就可以看到直接预览内容时所展示的界面啦。好啦，通过`UIDocumentInteractionController`实现`史蒂夫•乔布斯传`的预览和分享就到此结束啦。我会在下面的章节中，讲解通过其他技术实现`乔布斯自传`的分享和操作。