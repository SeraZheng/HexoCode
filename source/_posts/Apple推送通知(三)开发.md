---
layout: post
title: Apple推送通知(三)开发
date: 2015-07-14
tags: [APNS]
categories: [iOS]
---

# 前言　　
经过了前面的准备，我们在这一篇中终于是要开始接触代码了，首先我是一个`Objective-c`语言的支持者，所以下面会使用`Objective-c`语言来组织代码。

# Json数据
我们想要收到的消息是以JSON格式提供的，类似于下面这样:
{% codeblock lang:json %}
{
   "aps":{
       "alert": "Hello, world!",
       "sound": "default"
       "badge": "2"
   }
}
{% endcodeblock %}
<!-- more -->
# 注册deviceToken
首先我们需要在我们的项目中添加注册设备的`deviceToken`的方法：这一点在iOS 7和iOS 8中略有差异。在iOS 8以后官方SDK中新增加了一个类`UIUserNotificationSettings`，将`APNS`注册设备的设置封装了起来，并提供了更好的支持。

在iOS 8以下的SDK中，我们使用下面的注册方式：

{% codeblock lang:objc %} 
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary 	*)launchOptions {
    
   	[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeAlert |UIRemoteNotificationTypeBadge |UIRemoteNotificationTypeSound)];
}
{% endcodeblock %}	

但是在iOS 8以后的SDK中，就需要使用下面的注册方式：

{% codeblock objc %}
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) { // iOS version >= 8.x
        UIUserNotificationSettings *notificationSettings = [UIUserNotificationSettingssettingsForTypes:(UIUserNotificationTypeAlert | UIUserNotificationTypeSound | UIUserNotificationTypeBadge) categories:nil];
        [application registerUserNotificationSettings:notificationSettings];
        [application registerForRemoteNotifications];
    }
}
{% endcodeblock %}

# 获取deviceToken
当app运行以后，就可以在下面的方法中接收到`APNS`返回的设备的`deviceToken`：我们就可以在下面的方法中，把接收到的`deviceToken`发送注册到自己的Server上（需要Server端提供api）

{% codeblock lang:objc%}
-(void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken{
  	NSLog(@"My token is: %@", deviceToken);   
}
{% endcodeblock %}


{% codeblock lang:objc %}
-(void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error    {
  	NSLog(@"Failed to get token, error: %@", error);    
}
{% endcodeblock %}

# 点击通知
当`APNS`消息推送过来的时候，通过点击消息就可以触发下面的方法，在iOS 7和iOS 8不同的SDK中也会不同，iOS 8之前是：

{% codeblock lang:objc %}
-(void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo    {
   	NSLog(@"Received notification: %@", userInfo);    
}
{% endcodeblock %}

而在iOS 8之后的SDK中是：

{% codeblock lang:objc %}
-(void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchComplet    ionHandler:(void (^)		(UIBackgroundFetchResult))completionHandler{
    NSLog(@"Received notification: %@", userInfo);    
}
{% endcodeblock %}

我们通过在上面的回调方法中加入自己的逻辑，就可以很好的通过Apple推送通知来增强用户体验。