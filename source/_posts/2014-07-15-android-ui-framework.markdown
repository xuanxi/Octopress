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

##Home, All Apps, and Recents
###Home screen
Home is a customizable space that houses app shortcuts, folders and widgets. Navigate between different home screen panels by swiping left and right.  
The Favorites Tray at the bottom always keeps your most important shortcuts and folders in view regardless of which panel is currently showing.  
Access the entire collection of apps and widgets by touching the All Apps button at the center of the Favorites Tray.

Home 作为 Android 系统的主界面，用来放置应用快捷方式、文件夹和 Widgets（窗口部件），通过左右滑动来切换多个 Home 页面。  
Home 的底部是一个特殊的托盘，固定放置最常用的应用快捷方式或文件夹。托盘中间的按钮可以打开全部应用列表。

![ui_overview_home_screen](/images/ui_overview_home_screen.png)
###All apps screen
The All Apps screen lets you browse the entire set of apps and widgets that are installed on your device.  
Users can drag an app or widget icon from the All Apps screen and place it in any empty location on any Home screen.

![ui_overview_all_apps](/images/ui_overview_all_apps.png)
###Recents screen
Recents provides an efficient way of switching between recently used applications. It provides a clear navigation path between multiple ongoing tasks.  
The Recents button at the right side of the navigation bar displays the apps that the user has interacted with most recently. They are organized in reverse chronological order with the most recently used app at the bottom.  
Switch to an app by touching it. Remove an item by swiping left or right.

![ui_overview_recents](/images/ui_overview_recents.png)
##System Bars
System Bars 包含 Status Bar 和 Navigation Bar 两部分。

![ui_overview_system_ui](/images/ui_overview_system_ui.png)
####1. Status Bar
Displays pending notifications on the left and status, such as time, battery level, or signal strength, on the right. Swipe down from the status bar to show notification details.
  
Status Bar 用来展示通知信息和设备信息。下拉展示通知详情。
####2. Navigation Bar
New for phones in Android 4.0, the navigation bar is present only on devices that don't have the traditional hardware keys. It houses the device navigation controls Back, Home, and Recents, and also displays a menu for apps written for Android 2.3 or earlier. 
 
Navigation Bar 出现在4.0之后的版本，用来取代设备的物理按钮，同时提供菜单按钮以兼容2.3及以下的版本。  
从目前市面上的设备来看，大多依然具备物理导航键如三星，有的就虚拟导航和物理按键相结合如魅族MX2。
##Notifications
Notifications are brief messages that users can access at any time from the status bar. They provide updates, reminders, or information that's important, but not critical enough to warrant interrupting the user. Open the notifications drawer by swiping down on the status bar. Touching a notification opens the associated app.
  
通知用于随时提醒用户一些简单而重要信息并且不会打断当前的用户操作。点击一个通知应该进入相关的应用。

Notifications can be expanded to uncover more details and relevant actions. When collapsed, notifications have a one-line title and a one-line message.The recommended layout for a notification includes two lines. If necessary, you can add a third line. 
 
典型的通知布局包含一行标题和一行消息。需要的话也可以加入第三行。

![ui_overview_notifications](/images/ui_overview_notifications.png)

Swiping a notification right or left removes it from the notification drawer.

![notifications_dismiss](/images/notifications_dismiss.png)
##Common App UI
A typical Android app uses action bars, and many apps will include a navigation drawer.
  
一个典型的 Android 应用包含 Action Bar 和 Content Area 两部分。如果应用的结构较复杂，可以使用 Navigation Drawer 来切换主要页面。

![app_structure_drawer](/images/app_structure_drawer.png)
####Action Bar
The command and control center for your app. The action bar surfaces the most important actions for the current view, and may include simple controls for switching between views.
  
Action Bar 用来承载与当前页面相关的主要操作，也可用于页面的切换。
####Navigation Drawer
If your app's structure is more complex, the navigation drawer can display the main navigation options. The navigation drawer expands from the left edge of the screen, overlaying the content area but not the action bar.

Navigation Drawer 的弹出不覆盖 Action Bar，展示方式与目前流行在iOS应用中的侧边栏有所不同。
####Content Area
The space where the content of your app is displayed.