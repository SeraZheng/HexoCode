---
layout: post
title: 详解苹果提供的UTI(统一类型标识符)
date: 2015-11-20
description: "解释UTI的概念和相关内容"
tags: [UTI]
categories: [iOS]
---

# 前言
最近项目中有个需求，在iOS设备上使用iOS系统提供的内容分享功能，从第三方App应用直接分享实体内容到我们的应用中。其大概的原理是这样的，首先为我们的iOS应用注册可以打开document types(文档类型)，然后在第三方应用中，如果它们使用了iOS提供的分享功能，那么就会看到我们的应用程序，点击进行分享。

而关于需求的设计和实现的具体思路，我会在下一篇博客中详细讲解。这篇文章是来讲一下在iOS系统中为了更好的进行类型标识，而提供的一套共用的规范，也就是标题中提到的“Uniform Type Identifier(UTI)”，我把它翻译成“统一类型标识符”,下面统一简称为“UTI”。
<!-- more -->
# 官方教程
网上关于UTI的使用教程少之又少，所以我只是参考了苹果官方文档提供的讲解，这篇博客权当是我对于官方文档的一个理解吧！！自认为很重要的部分，我会贴出来官方文档原文，以便于大家学习理解，不至于被我的歪词所误导，同时也推荐大家从开发者中心上搜一些文档来看，这里推荐几篇：

1.[Cocoa Core Competencies -- Uniform Type Identifier‍](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/UniformTypeIdentifier.html)

这篇文档提供了一个视图来说明UTI是什么，怎么工作和被谁使用，是个非常好的新手指南。
   
2.[Uniform Type Identifier介绍和使用‍](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_conc/understand_utis_conc.html#//apple_ref/doc/uid/TP40001319-CH202-BCGCDHIJ)

这篇文档详细得描述了UTI的基础概念和属性，还有它们的使用方法，内容非常丰富，本文主要参考的就是这篇

3.[System-Declared Uniform Type Identifiers‍](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html#//apple_ref/doc/uid/TP40009259-SW1)

这篇文档提供了在OS X系统中定义的一个UTI的列表，我们可以查看每一种官方提供的UTI的定义和涵义。

4.[UTType Reference‍](https://developer.apple.com/library/ios/documentation/MobileCoreServices/Reference/UTTypeRef/index.html#//apple_ref/doc/uid/TP40008771)

这篇文档提供了对UTI字符串直接操作的函数方法

5.[一步一步为iOS应用添加自定义的document type和新的UTI‍](https://developer.apple.com/library/ios/qa/qa1587/_index.html#//apple_ref/doc/uid/DTS40012659)

顾名思义，这篇文档，讲解的是如何在iOS应用中导入新的UTI和添加自定义的document type。

# 为什么会有UTI
为什么会有UTI，打个比方，它就像是如今世界各国作为官方语言统讲得英文。为什么这么比喻呢，因为中国人讲母语汉语，法国人讲母语法语，但是如果一个中国人到了法国，而又不懂法语，碰到的法国人不懂汉语，那么他们如何交流沟通呢，这就是英文的用武之地了。而相对而言，苹果操作系统相当于整个世界，各个不同的程序或者服务相当于各个国家，俩个不同的程序想要互通交流，就比如互相发送文件，可是一个使用文件扩展名，一个使用MIME类型，俩者的数据类型不同，无法解析，都互相不认识，那么怎么交流沟通呢?在这样的情景下，UTI就有了用武之地啦，它就充当的是现实世界的英文这个角色。

# UTI概念
Uniform type identifiers(UTIs)提供了在整个系统里面标识数据的一个统一的方式，比如documents(文档)、pasteboard data(剪贴板数据)和bundles(包)。

>而具体到UTI的定义，官方文档是这么说的：“A uniform type identifier is a string that uniquely identifies a class of entities considered to have a ‘type’.”。其大概意思是说，一个统一类型标识符是一个唯一标识一种拥有"类型"属性实体的字符串。而且，针对这个“type”，官方文档还给我们提出了例子解释，对于一个文件或者是字节流来说，“type”指的的数据类型；而对于packages和bundles来说，“type”指的就是它们内部的目录层级结构。

# UTI用途
大多数情况下，一个UTI提供的是系统中所有程序和服务都能够识别并且依赖的一个唯一的标识，这么讲可能有些太抽象，我们使用一下官方文档中给出的例子，比如一个JPEG类型的图片文件，在不同的环境下，可以有下面几种不同的标识方法：

>1. ‘JPEG’, OSType表示,
2. '.jpg', 文件扩展名
3. '.jpeg', 文件扩展名
4. 'image/jpeg', MIME(多用途互联网邮件扩展类型)中的一种类型

而UTI则是用‘public.jpeg’这个字符串标识，完全代替了这些不一致的标签，这个字符串和其他任何一个旧标签都是完全兼容的，而且他们之间可以相互转换。由于UTI可以标识任何类型的实体，所以他们相对于旧标签来说灵活性更强了；使用UTI我们可以表示下面这些实体：

>* Pasteboard data
* Folders (directories)
* Bundles
* Frameworks
* Streaming data
* Aliases and symbolic links

# 用法

### 1. 简介

Apple给我们提供了在iOS和Mac应用中通用的UTI字符串集合，比如，'public.data'、'public.item'、'public.image'等，这些我们都可以在官方文档中进行查阅他们的涵义。除此之外，我们也可以在应用程序中自定义自己的UTI字符串，比如我们可以定义一个标识特殊文档格式的UTI字符串叫'io.github.serazheng'，如果其他的应用程序想要支持我们这种格式的文档，他们就可以用'io.github.serazheng'来标识我们的文档。

### 2. 字符集
看到我们上边的举例了，那么我们来说一下定义UTI字符串时所用到的字符集。通常一个UTI字符串是一个包含ASCII字符的Unicode字符串，同时也可以加入罗马字母和阿拉伯数字，如（A-Z）,（a-z），（0-9）还有点号（"."）和连接符("-")。而任何包含非法字符的字符串，如包含下划线'_'，都无法作为UTI来标识内容，而且Apple不会有任何错误反馈。

### 3. 语法
就像我上面的例子一样，UTI的定义和我们开发iOS程序时填写organization时一样，采取的是反域名规则。如下面这几种：

>* com.apple.quicktime-movie
* com.mycompany.myapp.myspecialfiletype
* public.html
* com.apple.pict
* public.jpeg

而UTI中的域名，如‘com’、‘public’这些，仅仅是用来表示这个UTI字符串在域名层级中的位置，它不会影响任何相似类型的分组。比如，‘public’域名就是大部分应用程序用来标识标准类型的，而目前仅仅只有Apple可以创建‘public’域名的UTI。

另外，我们可能会碰到的是一种‘dyn’域名，是动态域名，意思就是我们使用中，不会指定这种类型的UTI为某一个字符串，然后系统运行过程中，会自动识别帮我们处理。针对这种动态标识，我们是看不到的，但是我们可以通过UTI字符串的操作方式转换成我们的常用类型，比如OSType,MIME类型等。

>官方文档中，对动态标识有个比喻:“You can think of a dynamic identifier as a UTI-compatible wrapper around an otherwise unknown filename extension, MIME type, OSType, and so on”，大概意思就是我们可以把这种动态标识当做是针对普通类型进行了重写包装的，而且是兼容UTI的一种标识。

最后一种就是可以自定义的域名，代表性的就是‘com’域名，Apple也给我们提供了一些他们定义的'com'域名的UTI。

# 顺应性
UTI相对于其他那些旧标签的一个关键优势就是在于，它可以在一个顺应结构中声明。而用我们面向对象的方式说，UTI就是可继承的，而且是多继承方式。先上图：
![UTI继承结构](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/art/conformance_hierarchy.gif)
 

如上图所示，‘public.html’这个UTI就是继承于‘public.text’这个UTI，因为‘public.html’标识的是HTML文本格式，也属于是文本格式的一种，而文本、图片等等这些内容又都属于是数据的一种，所以他们继承于'public.data'这个UTI。

上面这个UTI继承结构图，指的是UTI中的内容形式的继承结构，此外，原则上来说，指定UTI层次的时候，即可以指定它的功能结构，也可以指定它的物理结构，上图是就是一个内容形式的功能结构图.物理结构指的就是这个UTI的物理实质，比如它标识一个目录，一个文件等，而功能结构指的就是这个UTI的用图，比如同样是文件，它标识的可以是图片、视频等等。 而官方文档也给出了一般指定UTI层次结构的规则：

1. 一个UTI在物理层次上需要继承‘public.item’
2. 一个UTI在功能层次需要继承非'public.item'之外的UTI。

然而，指定UTI的功能层次并不是强制的，但是这样做是考虑到可以更好地将UTI集成到系统一些特性中，就比如Spotlight应用，就可以把我们指定的功能性UTI和命名属性联系起来。下面是一个UTI功能顺应结构和物理顺应结构图：
 ![UTI功能结构和物理结构](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/art/physical_vs_functional.gif)
 
这个顺应性使得我们的UTI在决定类型上拥有更高的灵活性，不仅避免了大量的条件判断的使用，而且还可以关联你想不到的一些类型。

# 使用UTI

### 1. 应用场景
在Mac OS中我们开发应用时我们可以经常使用到UTI，但是在开发iOS应用程序时，我们应用到UTI的场景不是很多，这也是现在网上教程偏少得原因。而在iOS开发中，一般我们使用UTI来标识剪贴板的类型。而在具体使用到Apple给我们提供的UTI字符串的时候，我们必须使用在UTCoreTypes.h文件中定义的常量来代替直接使用字符串。关于UIPasteboard的详细使用，大家可以去这篇博客中详细学习一下：[精通UIPasteboard粘贴板](http://blog.csdn.net/zhangao0086/article/details/7580654)。

### 2. 操作方法
现在我们来看一下苹果提供的一些直接操作UTI的函数方法，简单列举几个。我们可以在MobileCoreServices这个framework中的UIType.h文件中找到，我们也可以仔细的看一下这个framework中的其他文件，都是对UTI的一些定义和声明。

* UITypeEqual

判断俩个UTI是否完全一样，后者是一个动态标签说明是否是另外一个UTI标签说明的子集。

* UITypeConformsTo

判断俩个标签的顺应性，用面向对象的角度理解，就是判断是否是子类。

* UTTypeCreatePreferredIdentifierForTag

通过其他类型标识符，如MIME标识符，转换成UTI，当可以创建多个UTI字符串时，一般返回'public'域名的UTI。

* UTTypeCreateAllIdentifiersForTag

通过其他类型标识符，如MIME标识符，转换成UTI，当可以创建多个UTI字符串时，返回所有的UTIs,让你自己选

* UTTypeCopyPreferredTagWithClass

交换UTI字符串标识

# 自定义UTI

### 1. 用法

苹果允许Mac开发者为他们的Mac App中独有的数据格式自定义新的UTI。它们一般被声明在下面几个文件中

>* Info.plist
* Application bundles
* Spotlight Importer bundles
* Automator action bundles

使用官方给我们的一个UTI声明的例子，Public.jpeg声明：
{% codeblock lang:xml %}
<key>UTExportedTypeDeclarations</key>
        <array>
            <dict>
                <key>UTTypeIdentifier</key>
                <string>public.jpeg</string>
                <key>UTTypeReferenceURL</key>
                <string>http://www.w3.org/Graphics/JPEG/</string>
                <key>UTTypeDescription</key>
                <string>JPEG image</string>
                <key>UTTypeIconFile</key>
                <string>public.jpeg.icns</string>
                <key>UTTypeConformsTo</key>
                <array>
                    <string>public.image</string>
                    <string>public.data</string>
                </array>
                <key>UTTypeTagSpecification</key>
                <dict>
                    <key>com.apple.ostype</key>
                    <string>JPEG</string>
                    <key>public.filename-extension</key>
                    <array>
                        <string>jpeg</string>
                        <string>jpg</string>
                    </array>
                    <key>public.mime-type</key>
                    <string>image/jpeg</string>
                </dict>
            </dict>
        </array>
{% endcodeblock %}

一个UTI声明的属性列表：


<section>
    <div style="margin-top:1.667em;margin-bottom:1.667em;">
        <table border="0" cellspacing="0" cellpadding="5">
            <tbody>
                <tr>
                    <th scope="col" style="font-weight:400;background-color:#93A5BB;padding:0.3em 0.667em;font-size:13px;color:#FFFFFF;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:#9BB3CD;border-right-width:1px;border-right-style:solid;border-right-color:#9BB3CD;">
                        <p style="font-weight:700;line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;margin-bottom:0.33em;">
                            Key
                        </p>
                    </th>
                    <th scope="col" style="font-weight:400;background-color:#93A5BB;padding:0.3em 0.667em;font-size:13px;color:#FFFFFF;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:#9BB3CD;border-right-width:1px;border-right-style:solid;border-right-color:#9BB3CD;">
                        <p style="font-weight:700;line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;margin-bottom:0.33em;">
                            Value type
                        </p>
                    </th>
                    <th scope="col" style="font-weight:400;background-color:#93A5BB;padding:0.3em 0.667em;font-size:13px;color:#FFFFFF;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:#9BB3CD;border-right-width:1px;border-right-style:solid;border-right-color:#9BB3CD;">
                        <p style="font-weight:700;line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;margin-bottom:0.33em;">
                            Description
                        </p>
                    </th>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTExportedTypeDeclarations</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            array of dictionaries
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            An array of exported UTI declarations (that is, identifiers owned by the bundle’s publisher).
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTImportedTypeDeclarations</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            array of dictionaries
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            An array of imported UTI declarations (that is, identifiers owned by another company or organization).
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTTypeIdentifier</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            string
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            The UTI for the declared type. This key is required for UTI declarations.
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTTypeTagSpecification</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            dictionary
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            A dictionary defining one or more equivalent type identifiers.
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTTypeConformsTo</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            array of strings
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            The UTIs to which this identifier conforms.
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTTypeIconFile</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            string
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            The name of the bundle icon resource to associate with this UTI.
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTTypeDescription</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            string
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            A user-visible description of this type. You can localize this string by including it in an <code style="font-family:Courier, Consolas, monospace;color:#666666;">InfoPlist.strings</code> file.
                        </p>
                    </td>
                </tr>
                <tr>
                    <td scope="row">
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            <code style="font-family:Courier, Consolas, monospace;color:#666666;">UTTypeReferenceURL</code>
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            string
                        </p>
                    </td>
                    <td>
                        <p style="line-height:normal;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;">
                            The URL of a reference document describing this type.
                        </p>
                    </td>
                </tr>
            </tbody>
        </table><br />
    </div>
</section>
<section>
    <a name="//apple_ref/doc/uid/TP40001319-CH204-SW4" title="Recommendations for Declaring new Uniform Type Identifiers" style="color:#3366CC;font-family:'Lucida Grande', 'Lucida Sans Unicode', Helvetica, Arial, Verdana, sans-serif;font-size:13px;background-color:#FFFFFF;"></a>
</section>
<p>
    <br />
 </p>

而且自定义的UTI必须指定UTExportedTypeDeclarations或者UTImportedTypeDeclarations，这样如果是你定义的UTI，第三方应用程序和服务都可以使用，而如果是其他人定义的UTI，那么加入到你的项目中后，你就可以看到这种类型的数据。


>而官方文档中有一句话:“If both imported and exported declarations for a UTI exist, the exported declaration takes precedence over imported one”，我的理解是，被导出去得UTI声明所有人是定义UTI的发布者，也就是你的项目，而被导入的UTI声明所有人就是其他人，所以就是说如果对于一个UTI来说，俩种类型的声明都存在，则自己定义的UTI声明优先使用。

### 2. 自定义UTI的建议
1. 你的UTI字符串必须是唯一的。以‘com.’开头的反域名命名方式是确保唯一性的简单有效的方法。
2. 如果你的代码依赖于第三方App，UTI类型或许不会在系统中展示，你应该在bundle中声明为导入类型
3. 如果你的UTI类型是一个或多个已存在的UTI类型的子类，则必须给它添加顺应性，让它继承于某个父类。最好是继承于‘public’类型。