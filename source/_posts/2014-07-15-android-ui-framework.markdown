---
layout: post
title: "Android UI Framework"
date: 2014-07-15 21:21:41 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/design/handhelds/index.html>

本文针对手机和平板，其它设备点击以下链接：  
1. 可穿戴设备 [Wear](http://developer.android.com/design/wear/index.html);  
2. 电视 [TV](http://developer.android.com/design/tv/index.html);  
3. 汽车 [Auto](http://developer.android.com/design/auto/index.html).

Android 界面框架主要分为四大部分：  
1. 应用集合：Home、All Apps、and Recents；  
2. System Bar：Status Bar、Navigation Bar；  
3. Notifications；  
4. 应用框架：Action Bar、Navigation Drawer、Content Area。
<!--more-->

##1. Home, All Apps, and Recents
###Home screen
Home 作为 Android 系统的主界面，用来放置应用快捷方式、文件夹和 Widgets（窗口部件），通过左右滑动来切换多个 Home 页面。  
Home 的底部是一个特殊的托盘，固定放置最常用的应用快捷方式或文件夹。托盘中间的按钮可以打开全部应用列表。

![ui_overview_home_screen](/images/ui_overview_home_screen.png)
###All apps screen
![ui_overview_all_apps](/images/ui_overview_all_apps.png)
###Recents screen
![ui_overview_recents](/images/ui_overview_recents.png)

##2. System Bars
System Bars 包含 Status Bar 和 Navigation Bar 两部分。

![ui_overview_system_ui](/images/ui_overview_system_ui.png)
###Status Bar
Status Bar 用来展示通知信息和设备信息。下拉展示通知详情。
###Navigation Bar
Navigation Bar 出现在4.0之后的版本，用来取代设备的物理按钮，同时提供菜单按钮以兼容2.3及以下的版本。  
从目前市面上的设备来看，大多依然具备物理导航键如三星，有的就虚拟导航和物理按键相结合如魅族MX2。

##3. Notifications
通知用于随时提醒用户一些简单而重要信息并且不会打断当前的用户操作。点击一个通知应该进入相关的应用。典型的通知布局包含一行标题和一行消息。需要的话也可以加入第三行。

![ui_overview_notifications](/images/ui_overview_notifications.png)

![notifications_dismiss](/images/notifications_dismiss.png)
##4. Common App UI
一个典型的 Android 应用包含 Action Bar 和 Content Area 两部分。如果应用的结构较复杂，可以使用 Navigation Drawer 来切换主要页面。

![app_structure_drawer](/images/app_structure_drawer.png)
###Action Bar
Action Bar 用来承载与当前页面相关的主要操作，也可用于页面的切换。
###Navigation Drawer
Navigation Drawer 的弹出不覆盖 Action Bar，展示方式与目前流行在iOS应用中的侧边栏有所不同。
###Content Area
内容区域