---
layout: post
title: 重写prepareForReuse来重用UITableViewCell
date: 2015-08-15
description: "描述为什么重写prepareForReuse以及如何重用UITableViewCell"
tags: [UIKit]
categories: [iOS]
---

# 前言
借用一下Apple官方的话,`"出于性能考虑，一个表视图的单元必须是可复用的"`。重用cell的机制是利用缓冲池，将可重用的cell保存起来，显示cell时，先从缓冲池中取，如果缓冲池中没有此类的cell，也就是没有可重用的cell，此时就会重新初始化一份cell，并且加到缓冲池中。
<!-- more -->
# 获取可重用的cell
而取出缓冲池中的cell，需要用到`dequeueReusableCellwithIdentifier:`方法：
{% codeblock lang:objc %}
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath(NSIndexPath*)indexPath  {
    UITableViewCellStyle style = UITableViewCellStyleSubtitle;
    staticNSString *cellID = @"cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellID];
    if (cell == nil) {
        cell = [[[UITableViewCellalloc] initWithStyle:style reuseIdentifier:@"cell"] autorelease];
        cell.detailTextLabel.text = [NSString stringWithFormat:@"Cell %d",++count]; //当分配内存时标记
    }
    cell.textLabel.text = [NSString stringWithFormat:@"Cell %d",[indexPath row] + 1];  //当新显示一个Cell时标记
    return cell;
}
{% endcodeblock %}

# prepareForReuse调用时机
在重用cell的时候，如果每个cell中都有不同的子视图或者是需要发送不同的网络请求，此时在应用`dequeueReusableCellWithIdentifier:`方法时就会出现视图重叠的情况，针对于此种情况，我们就需要在自定义的cell中重写`prepareForReuse`方法。因为当屏幕滚动导致一个cell消失，另外一个cell显示时，系统就会发出`prepareForReuse`的通知，此时，我们需要在重载的`prepareForReuse`方法中，将所有的子视图隐藏，并且将内容置空。这样就不会出现重叠现象。