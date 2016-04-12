---
layout: post
title: Apple推送通知(四)silent APNS
date: 2015-07-15
tags: [APNS]
categories: [iOS]
---

# 前言　　
我在上一篇文章中介绍了传统的APNS推送的原理和配置，以及调用的方法。使用传统的APNS推送最大的限制就是字节数的限制，payload最大不能超过256 bytes。而有时往往我们需要更多的信息推送，这时我们就需要用到`silent APNS`

# 简介
`silent APNS`是iOS 7新加的一个非常好的特性，和以往传统的APNS最大的不同是，当一个`silent APNS`推送到设备时，iOS系统并不会用弹出框提示内容，也不会听到声音，看到图标上的badge，用户不会知道任何事情。而`silent APNS`与传统的APNS相同的是，当推送到设备时，如果App处于激活状态则会调用到相同的系统方法，来让App获取到推送的信息，然后我们可以在App中发起HTTP请求，获取我们想要的数据，之后再展示给用户。
<!-- more -->
# 设置
`silent APNS`和传统的APNS对于证书，签名的设置都是一样的。我们需要在Xcode设置UIBackgroundModes.

{% codeblock lang:xml%}
<key>UIBackgroundModes</key>
<array>
    <string>remote-notification</string>
</array>
{% endcodeblock %}
    
Xcode设置图：
{% img /images/Silent-Push-Notifications-in-iOS1.png %}

# Json数据
我们在传统的APNS中想要收到的消息是以JSON格式提供的，类似于下面这样:
{% codeblock lang:json %}
{
   "aps":{
       "alert": "Hello, world!",
       "sound": "default"
       "badge": "2"
   }
}
{% endcodeblock %}

而在`silent APNS`中我们需要设置推送的payload为下面的JSON格式
{% codeblock lang:json %}
{
   "aps":{
       content-available: 1
       "alert": {...}
   }
}
{% endcodeblock %}

# 注册deviceToken
我们在上一节中已经清楚讲了在我们的项目中添加注册设备的`deviceToken`，获取`deviceToken`的方法，而关于`silent APNS`最大的不同之处是我们需要注册的类型不同.

在传统的APNS中，我们使用注册`UIRemoteNotificationTypeAlert`, `UIRemoteNotificationTypeBadge`, `UIRemoteNotificationTypeSound`
{% codeblock lang:objc %} 
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary 	*)launchOptions {
    
   	[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeAlert |UIRemoteNotificationTypeBadge |UIRemoteNotificationTypeSound)];
}
{% endcodeblock %}	

而在`silent APNS`中，我们需要多注册一个`UIRemoteNotificationTypeNewsstandContentAvailability`类型

{% codeblock lang:objc %}
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                               (UIRemoteNotificationTypeNewsstandContentAvailability|
                                            UIRemoteNotificationTypeBadge |
                                            UIRemoteNotificationTypeSound |
                                            UIRemoteNotificationTypeAlert)];
}
{% endcodeblock %}

# 推送调用
当`APNS`消息推送过来的时候，传统的APNS会直接使用声音、弹出框等提示用户，通过点击消息就可以触发下面的方法，而在`silent APNS`中,当推送到达设备时，如果App处于激活状态，则会自动调用下面的方法：

{% codeblock lang:objc %}
-(void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchComplet    ionHandler:(void (^)		(UIBackgroundFetchResult))completionHandler{
    NSLog(@"Received notification: %@", userInfo);    
}
{% endcodeblock %}

我们在`silent APNS`中需要做的就是通过在上面的回调方法中加入自己的逻辑，譬如根据推送过来的信息发起HTTP请求，获取更多想要展示的数据，然后再利用`localNotification`或者是其他手段将信息展示给用户，就可以很好的通过Apple推送通知来增强用户体验。