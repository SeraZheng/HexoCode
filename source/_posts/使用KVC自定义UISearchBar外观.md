---
layout: post
title: 使用KVC自定义UISearchBar外观
date: 2015-10-25
description: "介绍'UISearchBar'定制外观的一些方式"
tags: [UIKit]
categories: [iOS]
---

# 前言
在iOS8中，Apple在UIKit框架中给我们提供了`UISearchController`来代替之前的`UISearchDisplayController`，在`UISearchController`中，我们无需再自己初始化`UISearchBar`，只需要提供searchResult展示的视图。然而在开发中，我们往往需要根据项目的风格来改变`UISearchBar`的外观，通过继承的方式，我们可以完全定制符合项目风格的外观，然而有些情况下我们很难短时间内完成全部的外观定制工作，譬如我们项目用的好几个旧框架，代码中充斥着各种写好的`UISearchBar`的展示，而改动底层框架并不是一个较好地实践。于是我开始搜索并总结出了几个不通过继承的方式来更改UISearchBar外观的方法。
<!-- more -->
# 获取子view
我们在`UISearchController`或者是`UISearchDisplayController`中都可以直接获取到`UISearchBar`的实例，我们可以从这里改变一些UISearchBar的属性来改变外观显示。同时我们也可以直接获取UISearchBar的subViews,UISearchBar的subView是一个UIView的实例，这个UIView包含了所有在UISearchBar上可以展示的子视图，iOS SDK提供的UISearchBar，在iOS7之前是分为`UISearchBarBackground`、`UISearchBarTextField`、`UIButton`这几个类的实例组成，而在iOS7之后，是将UIButton转换为了`UINavigationButton`的实例。

#### 1.我们可以通过循环遍历出UISearchBar上所有展示出来的子视图
{% codeblock lang:objc %}
for(UIView*viewin[[[_searchController.searchBar subviews]lastObject]subviews] ) {
	if([view isKindOfClass:NSClassFromString(@"UISearchBarBackground")]) {}
	if([view isKindOfClass:NSClassFromString(@"UISearchBarTextField")]) {}
	if([view isKindOfClass:NSClassFromString(@"UINavigationButton")]) {}
}
{% endcodeblock %}

#### 2.通过KVC获取子视图
{% codeblock lang:objc %}
UIView *backgroundView = [_searchController.searchBar valueForKey:@"_background"];
UITextField *searchField = [_searchController.searchBar valueForKey:@"_searchField"];
UIButton *cancelButton = [_searchController.searchBar valueForKey:@"_cancelButton"];
{% endcodeblock %}

#### 3.当我们获取cancelButton时，一定要确保cancelButton包含在了UISearchBar中，必要时可以提前调用：
{% codeblock lang:objc %}
[_searchController.searchBar setShowsCancelButton:YES animated:NO];
{% endcodeblock %}

# 去掉搜索框背景
{% codeblock lang:objc %}
for(UIView*viewin[[[_searchController.searchBar subviews]lastObject]subviews] ) {
	if([view isKindOfClass:NSClassFromString(@"UISearchBarBackground")]) {
		[view removeFromSuperview];
	}
}
{% endcodeblock %}

# 去掉搜索框边框
{% codeblock lang:objc %}
[_searchController.searchBar setBackgroundImage:[UIImage new]];
{% endcodeblock %}

# 改变输入框文本
{% codeblock lang:objc %}
//提示文本颜色

UITextField*searchField = [_searchController.searchBar valueForKey:@"_searchField"];

[searchFieldsetTextColor:[UIColorblackColor]];

[searchFieldsetValue:[UIColorgrayColor]forKeyPath:@"_placeholderLabel.textColor"];

[searchFieldsetFont:[UIFontsystemFontOfSize:14]];

[searchFieldsetBackgroundColor:[UIColorwhiteColor]];
{% endcodeblock %}

# 改变取消按钮的title
{% codeblock lang:objc %}
UIButton*cancelButton = [_searchController.searchBar valueForKey:@"_cancelButton"];

[cancelButtonsetTitle:@"Close"forState:UIControlStateNormal];
{% endcodeblock %}