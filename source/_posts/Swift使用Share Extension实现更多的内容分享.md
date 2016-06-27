---
layout: post
title: Swift使用Share Extension实现更多的内容分享
date: 2016-06-27
tags: [ShareExtension]
categories: [iOS]
---

# 前言
这篇文章在写作计划中原名是`iOS 使用Share Extension实现更多的内容分享`,本应该是在我的内容分享系列文章中完成的，由于之前换公司的适应和新项目的紧张，直到今天才有时间继续未完成的作业。之所以把他称之为`Swift 使用Share Extension实现APP之间的内容分享`，主要原因就是这个demo是由Swift语言写的，和OC语言非常的不同。而且Swift语言越来越普及，我们在新项目中使用的就是Swift语言，因此希望大家对Swift语言有更多的了解。
<!-- more -->
# App Extension
很多iOS新人，对`Share Extension`并不清楚，因此在进入正题之前，我觉得有必要描述一下`Share Extension`的来头。苹果在发布iOS 8时，给开发者提供了一套强大的应用扩展机制，称之为`App Extension`，这套机制不仅可以在iOS 系统上工作，在Mac系统上同样可以工作。
>苹果官方文档中，这样描述`App Extension`, "An app extension lets you extend custom functionality and content beyond your app and make it available to users while they’re interacting with other apps or the system. "，大概意思是，`App Extension`是被用来集成到系统或者其他APP中，从而扩展你自己的APP的功能和内容。

这套扩展机制提供了多个类型的App 扩展，下面有几种常用的扩展介绍：
* `Action Extension`，用在`UIActivityViewController`中的第二行的`Action List`，对这点不熟悉的可以去看我的这篇文章: [通过UIActivityViewController实现更多分享服务](http://www.jianshu.com/p/a1c9621a3f4e)
* `Custom Keyboard`，提供给开发者自定义键盘的权限，这点猜测苹果也是被逼无奈了
* `Document Provide`，提供文档的远程存取服务
* `Photo Editing`，提供照片和视频的编辑服务
* `Today`，提供在`Toady Widget`里面展示应用数据的服务
* `Share`，用在`UIActivityViewController`中的第一行的`APP List`提供iOS实体内容的分享服务

# App Extension原理
`App Extension`不同于一个APP，但是`App Extension`必须依托于一个APP，这个APP一般称之为`Container App (宿主应用)`。如果想要开发和发布一个`App Extension`，首先我们需要开发和发布一个App。尽管如此，`App Extension`也不会依赖于APP，它是一个独立的二进制包并且可以独立运行。而用户想要使用这个`App Extension`就需要在系统或者其他APP中，通过特定的操作打开，这时的APP称为`Host App(主应用)`。

`App Extension`的生命周期不同于APP，是有固定的流程，由系统启动并且关闭的，下图是一个周期图：

![app_extensions_lifecycle_2x.png](http://upload-images.jianshu.io/upload_images/1216322-80ef0376c6d824ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上面的周期图中，一个`App Extension`需要做的就是作为一个桥梁，将`Container App`和`Host App`连接起来进行交互,而这个交互流程针对`Container App`和`Host App`是不一样的。`App Extension`可以和`Host App`直接交互，因为我们需要通过`Host App`唤起`App Extension`，因此他们的交互流程是这样的：

![simple_communication_2x.png](http://upload-images.jianshu.io/upload_images/1216322-df106d7c5cee86fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而相反地，`App Extension`并不能和`Container App`直接交互，因为`App Extension`是不依赖于APP，可以独立运行的，而我们通过`App Extension`只可以通过固定的几个API来和`Container App`分享内容、代码，或者是在`Today Extension`中，我们可以通过`URL Scheme`的方式打开`Container App`,因此他们的交互流程是这样的：

![detailed_communication_2x.png](http://upload-images.jianshu.io/upload_images/1216322-3fb501c8a93ad3ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Share Extension
上面讲了`App Extension`的概念和原理，下面我们要进入今天的正题，也就是我们需要创建一个`Share Extension`来实现我们的文件分享。首先我们需要一个`Container App`，这里我们仍然使用分享系列文章中之前的Demo应用：[ZSDocumentInteractionTest](https://github.com/SeraZheng/ZSDocumentInteractionTest)，因为这个Demo中包含`UIActivityViewController`，我们可以把这个Demo APP既当做是`Container App`，也当做是`Host App`。

首先我们需要通过New一个target的方式，来创建一个`Share Extension`的模板：

![屏幕快照 2016-06-27 11.30.45.png](http://upload-images.jianshu.io/upload_images/1216322-e099781236d1ff3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择`Application Extensions`中的`Share Extension`模板，并且命名为“ZSShareExtension”, 填入名称并且创建之后，选择激活就可以了：

![屏幕快照 2016-06-27 11.33.27.png](http://upload-images.jianshu.io/upload_images/1216322-f5ffe5c084d52e97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，我们就可以在工程目录中看到创建好的标准的模板文件了，我们可以到包含了一个`info.plist`文件，一个默认的`MainInterface.storyboard`文件和一个默认的`ShareViewController`作为主要显示的UI。除此之外，我们还可以看到在`Products`目录下多了一个`ZSShareExtension.appex`的运行程序，由此我们可以更加直观的了解，`App Extension`是一个可以独立运行的程序：

![屏幕快照 2016-06-27 11.36.53.png](http://upload-images.jianshu.io/upload_images/1216322-7c8cb4703575bdd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 第一次启动
我们创建好模板文件之后，先来看一下启动`Share Extension`的效果，首先我们需要选中`ZSShareExtension`这个target，并且运行，这个时候，会提示让我们选择一个`Host App`，我们选择了哪个APP作为`Host App`，就必须使用这个APP来唤起`Share Extension`,从而达到可以调试的效果。

![屏幕快照 2016-06-27 11.55.11.png](http://upload-images.jianshu.io/upload_images/1216322-88ae1aa8d219aba3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

启动之后，进入到我的文件中，这里我有各种类型的文档,打开其中一个文档之后，选择用其他应用打开，然后就可以看到我们创建的这个`ZSShareExtension`啦！同时我们可以看出它和`Container App`显示的图标完全一样的。

![IMG_0746.PNG](http://upload-images.jianshu.io/upload_images/1216322-86e1977e1e1d9571.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而如果我们点开`ZSShareExtension`就可以看到`Share Extension`的默认UI界面，一个`Cancel`按钮，一个`Post`按钮，一个预览图，因为打开的是图片，这个预览图就是该图片, 因为我们是第一次启动，没有作任何的配置，所以`ZSShareExtension`干不了任何事情，点`Post`和`Cancel`是一个效果，这个`Share Extension`就被系统终止掉了：

![屏幕快照 2016-06-27 13.59.20.png](http://upload-images.jianshu.io/upload_images/1216322-6a912b59feaf2fd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Info.plist
我们可以试着打开png图片，pdf文件，doc文件等等，发现都可以找到`ZSShareExtension`这个`Share Extension`，而如果我只想要pdf文档或者png图片通过这个`Share Extension`上传到云服务器上，那就需要额外的配置，而这些配置就是在Info.plist文件中。

每一个`App Extension`和APP一样，都包含一个`Info.plist`文件，用来描述`Share Extension`的基本配置信息，这里我们需要用的最重要的俩个属性就是`CFBundleDisplayName`和`NSExtension`。
前者代表的`Share Extension`的显示名称，后者是一个Dictionary类型的属性，包含了`Share Extension`几个主要的配置信息。
![屏幕快照 2016-06-27 11.47.04.png](http://upload-images.jianshu.io/upload_images/1216322-0d8ad1344e11e4dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图中，我们可以看到，在`NSExtension`中，有下面几个常用属性：
* NSExtensionPointIdentifier 这个属性标明了这是哪种类型的`App Extension`,这里我们显示的是`com.apple.share-services`,表示这是一个`Share Extension`,这个属性不需要我们修改
* NSExtensionMainStoryboard 这个属性表示主程序的`storyboard`,如果我们自定义storyboard的话，需要改这个值
* NSExtensionAttributes 很显然这是一些扩展的属性集，也是我们开发者主要关注的，这里面包含了我们可以设置的几个值，下面是常用的一些值，其他的可以从官方文档中看到,地址是 [App Extension Keys](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/SystemExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW11)：
* NSExtensionActionWantsFullScreenPresentation
一个布尔值，表示是否以全屏modal的样式展示
* NSExtensionActivationDictionaryVersion
iOS 9新加的key,number值，特指一个`app extension`激活的版本号，值为1或2，1代表`app extension`可以处理所有的`host app`提供的文档类型，2代表仅支持最少一种类型，意味着可能不会在`host app`中的`UIActivityViewController`中显示
* NSExtensionActivationRule
指定`app extension`支持的数据类型，包含以下值，这是一个对开发者来说尤为重要的属性，我们在这里过滤数据类型
![屏幕快照 2016-06-27 14.44.08.png](http://upload-images.jianshu.io/upload_images/1216322-970e871273e7bdc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
同时，如果这里提供的属性并不能完全符合我们的需求，我们还可以自定义`NSPredicate`值作为过滤规则，代表性的有，如果我们只想使用pdf文档，可以用下面这个值：

{% codeblock lang:objc %}
SUBQUERY (
    extensionItems,
    $extensionItem,
    SUBQUERY (
        $extensionItem.attachments,
        $attachment,
        ANY $attachment.registeredTypeIdentifiers UTI-CONFORMS-TO "com.adobe.pdf"
    ).@count == $extensionItem.attachments.@count
).@count == 1
{% endcodeblock %}

# ShareViewController
在定义好`Info.plist`文件后，我们就需要在`ShareViewController`文件中完成我们的功能，我们可以看到`ShareViewController`是继承自`SLComposeServiceViewController`的，那我们就可以看一下`SLComposeServiceViewController`类中常用的属性和方法：
* ```public var textView: UITextView! { get }```
输入文字的textView,只读属性，默认被创建并且delegate为`ShareViewController`
* `    public func presentationAnimationDidFinish()`
`ShareViewController`展示完成时被调用，生命周期比较早，一般用来执行预加载任务
* `    public var placeholder: String!`
textView未输入文字时的占位字符
* `    public func didSelectPost()`
点击`Post`按钮时调用，这里就要做上传操作
* `    public func didSelectCancel()`
点击`Cancel`按钮时调用，一般做一些清理工作
* `    public func isContentValid() -> Bool`
决定了是否可以点击`Post`按钮，这里可以做一些内容的过滤限制等任务
* `    public var charactersRemaining: NSNumber!`
输入字符限制，一般和`isContentValid`配合使用
* `    public func configurationItems() -> [AnyObject]!`
展示页下面可以点选的item, 返回值必须是<SLComposeSheetConfigurationItem>[]！,个人理解[AnyObject]！有待改进
* ` public func reloadConfigurationItems()`
`public func pushConfigurationViewController(viewController: UIViewController!)`
`public func popConfigurationViewController()`

reload操作，push和pop操作
* `    public func loadPreviewView() -> UIView!`
显示预览视图，如果是图片和Web的话，默认展示图片
* `    public var autoCompletionViewController: UIViewController!`
一个用来替代`SLComposeSheetConfigurationItem`列表的视图，由系统控制

# App Group
尽管`App Extension`的bundle包含在`Container App`的bundle中，但是`App Extension`和`Container App`也是不能直接互相访问资源的。如果我们想要在`App Extension`中，使用一些`Container App`中的资源，比如用户名密码，或者是token,我们就必须先要创建一个共享域，这个共享域就是`App Group`。

首先我们选择`Container App`的target，并且点开`Capabilities`，可以看到`App Groups`，这时我们还没有开启

![屏幕快照 2016-06-27 15.37.12.png](http://upload-images.jianshu.io/upload_images/1216322-f567f5c15fab3993.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

右侧点击打开后，会默认启动，并添加`App Group`到entitlements文件，我们点击+号，可以新建一个`share container(共享域)`，注意名称必须以`group.`开头，而且注意`Apple ID`必须是开发者，拥有足够的权限。

![屏幕快照 2016-06-27 15.39.05.png](http://upload-images.jianshu.io/upload_images/1216322-cca30b2ef77f22b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建完成之后，我们选择`Share Extension`的target,同样是上述步骤开启`App Groups`，注意不同之处就在于这里仅仅需要选择上面创建的`share container`即可。然后我们就可以在代码中，通过下面的形式，将数据写入到共享域中：

{% codeblock lang:objc %}
NSUserDefaults(suiteName: "group.YourExtension")!.setObject(YourData,forKey:YourKey)
{% endcodeblock %}

获取共享域资源时，同样如此：

{% codeblock lang:objc %}
let shareData = NSUserDefaults(suiteName: "group.YourExtension")!.objectForKey("YourToken")
{% endcodeblock %}
