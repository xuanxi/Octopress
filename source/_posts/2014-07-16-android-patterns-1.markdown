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

本篇分为以下几部分：  
1. New in Android  
2. Gestures  
3. App Structure  
4. Navigation with Back and Up  
5. Action Bar

##1. New in Android
新特性

A quick look at the new patterns and styles you can use to build beautiful Android apps…  

###Android 4.4 KitKat
####Your branding
你的品牌

![branding_googlemusic](/images/branding_googlemusic.png)

Consistency has its place in Android, but you also have the flexibility to customize the look of your app to reinforce your brand.

一个 Android 应用在保持与平台体验一致的同时，也可以灵活地加入品牌个性。

Use your brand color for accent by overriding the Android framework's default blue in UI elements like checkboxes, progress bars, radio buttons, sliders, tabs, and scroll indicators.

用你的品牌颜色代替 Android 默认的蓝色，以表现应用的独特。

Show your app's launcher icon and name in the action bar so that users can see it in every screen of your app.

在 action bar 上展示启动图标和应用名字。

Your Branding highlights these and other pointers on how to incorporate elements of your brand into your app's visual language — highly encouraged!

####Touch feedback
Before Android KitKat, Android's default touch feedback color was a vibrant blue. Every touch resulted in a jolt of high-contrast color, in a shade that might not have mixed well with your brand's color(s).

在 kitKat 之前，Android 的触摸反馈颜色是明亮的蓝色。每次触摸都以 a jolt of 高对比的颜色来响应，这可能与你的品牌颜色不搭配。

In Android KitKat and beyond, touch feedback is subtle: when something is touched, by default its background color slightly darkens or lightens. This provides two benefits: (1) sprinkles of encouragement are more pleasant than jolts, and (2) incorporating your branding is much easier because the default touch feedback works with whatever hue you choose. Check the updated Touch Feedback page for more details.

KitKat 之后，有了细微调整：触摸之后，默认情况下背景颜色轻微变亮或变暗。这样有两个好处：（1）...；（2）更容易与品牌颜色搭配。

![touch_feedback_reaction_response](/images/touch_feedback_reaction_response.png)

####Full screen
Android KitKat has improved support for letting your app use the entire screen, with a few different approaches to meet the varying needs of apps and content. The new Full Screen page will guide you in setting the stage for deep user engagement.

为全屏提供了更好的支持。

![fullscreen_landing](/images/fullscreen_landing.png)

####Gestures
The updated Gestures page covers new and updated gestures introduced in Android KitKat: double touch drag and double touch. These gestures are used for changing the viewing size of content.

更新两种手势：double touch drag 和 double touch。

![gesture_doubletouch](/images/gesture_doubletouch.png)

###Android 4.1 Jelly Bean
####Notifications
Notifications have received some notable enhancements in Android 4.1:  
Users can act on notifications immediately from the drawer  
Notifications are more flexible in size and layout  
A priority flag helps sort notifications by importance  
Notifications can be collapsed and expanded  
The base notification layout has not changed, so app notifications designed for versions earlier than Jelly Bean still look and work the same. Check the updated Notifications page for more details.

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
手势

![gesture_touch](/images/gesture_touch.png)

####Touch 点击  
Triggers the default functionality for a given item.  
####Action  
Press, lift

![gesture_longtouch](/images/gesture_longtouch.png)

####Long press 长按  
Enters data selection mode. Allows you to select one or more items in a view and act upon the data using a contextual action bar. Avoid using long press for showing contextual menus.  
####Action  
Press, wait, lift

![gesture_swipe](/images/gesture_swipe.png)

####Swipe or drag  
Scrolls overflowing content, or navigates between views in the same hierarchy. Swipes are quick and affect the screen even after the finger is picked up. Drags are slower and more precise, and the screen stops responding when the finger is picked up.  
####Action  
Press, move, lift

![gesture_drag](/images/gesture_drag.png)

####Long press drag 长按拖动  
Rearranges data within a view, or moves data into a container (e.g. folders on Home Screen).  
####Action  
Long press, move, lift

![gesture_doubletouch](/images/gesture_doubletouch.png)

####Double touch 双击  
Scales up a standard amount around the target with each repeated gesture until reaching maximum scale. For nested views, scales up the smallest targetable view, or returns it to its original scale. Also used as a secondary gesture for text selection.  
####Action  
Two touches in quick succession

![gesture_doubletouchdrag](/images/gesture_doubletouchdrag.png)

####Double touch drag 双击拖动  
Scales content by pushing away or pulling closer, centered around gesture.  
####Action  
A single touch followed in quick succession by a drag up or down:  
Dragging up decreases content scale  
Dragging down increases content scale  
Reversing drag direction reverses scaling.

![gesture_pinchopen](/images/gesture_pinchopen.png)

####Pinch open  
Zooms into content.  
####Action  
2-finger press, move outwards, lift

![gesture_pinchclose](/images/gesture_pinchclose.png)

####Pinch close  
Zooms out of content.  
####Action  
2-finger press, move inwards, lift

##3. App Structure
应用结构

Apps come in many varieties that address very different needs. For example:  
应用根据不同的需求有不同的表现形式：

Apps such as Calculator or Camera that are built around a single focused activity handled from a single screen  
Apps such as Phone whose main purpose is to switch between different activities without deeper navigation  
Apps such as Gmail or the Play Store that combine a broad set of data views with deep navigation  

日历应用或摄像应用使用单个 activity

Your app's structure depends largely on the content and tasks you want to surface for your users.

