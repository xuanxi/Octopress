---
layout: post
title: "Android Patterns (1)"
date: 2014-07-16 22:43:39 +0800
comments: true
categories: Android
---
> Design apps that behave in a consistent, predictable fashion.

原文：  
<http://developer.android.com/design/patterns/index.html>

本篇包括以下内容：  
1. New in Android  
2. Gestures  
3. App Structure  
4. Navigation with Back and Up  
5. Action Bar

##1. New in Android

新版本系统中更新的设计风格和设计模式介绍。 

###Android 4.4 KitKat
####Your branding
你的品牌

![branding_googlemusic](/images/branding_googlemusic.png)

KitKat 更加强调应用的品牌，提供了展示品牌 logo 和品牌颜色的建议。（似乎也不是什么新特性，因为大部分应用早就这么做了）

####Touch feedback
新的触摸反馈风格

在 kitKat 之前，Android 的触摸反馈颜色是明亮的蓝色。每次触摸都以高对比的颜色来响应，这可能与你的品牌颜色不搭配。

KitKat 之后，有了细微调整：触摸之后，默认情况下背景颜色轻微变亮或变暗。这样有两个好处：（1）sprinkles of encouragement are more pleasant than jolts；（2）更容易与品牌颜色搭配。

![touch_feedback_reaction_response](/images/touch_feedback_reaction_response.png)

####Full screen
为全屏提供了更好的支持。

![fullscreen_landing](/images/fullscreen_landing.png)

####Gestures
更新两种手势：double touch drag 和 double touch。

###Android 4.1 Jelly Bean
####Notifications
4.1 版本的通知有较大增强：  
1. ...  
2. 在大小和布局上更灵活  
3. 提供优先排序  
4. 可以折叠和展开  
通知的基本布局不变，兼容老版本。

####Resizable Application Widgets
####Accessibility

###Android 4.0 Ice Cream Sandwich
####Navigation bar
![whats_new_nav_bar](/images/whats_new_nav_bar.png)
####Action bar
![whats_new_action_bar](/images/whats_new_action_bar.png)
####Multi-pane layouts
![whats_new_multipanel](/images/whats_new_multipanel.png)
####Selection
![whats_new_multiselect](/images/whats_new_multiselect.png)

##2. Gestures
8种手势

Touch 点击  
Long press 长按  
Swipe or drag 轻触快速滑动或慢速拖动  
Long press drag 长按拖动（Home 界面的拖动应用图标）

![gesture_touch](/images/gesture_touch.png)  ![gesture_longtouch](/images/gesture_longtouch.png)  ![gesture_swipe](/images/gesture_swipe.png)  ![gesture_drag](/images/gesture_drag.png)  

Double touch 双击  
Double touch drag 双击拖动（地图的缩放）  
Pinch open 张指放大  
Pinch close 并指缩小  

![gesture_doubletouch](/images/gesture_doubletouch.png)  ![gesture_doubletouchdrag](/images/gesture_doubletouchdrag.png)  ![gesture_pinchopen](/images/gesture_pinchopen.png)  ![gesture_pinchclose](/images/gesture_pinchclose.png)

##3. App Structure
应用结构
 
针对不同需求的应用有不同的界面表现形式，如 Android 官方的日历或摄像使用一个窗口即可完成大部分操作，而 Gamil 使用导航抽屉区域来切换不同的功能窗口。

