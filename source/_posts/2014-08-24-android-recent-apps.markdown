---
layout: post
title: "Android Recent Apps"
date: 2014-08-24 22:56:30 +0800
comments: true
categories: Android
---
最近听一些同学说到，通过最近应用界面（官方叫 Recents screen，以下简称 Recents）可以杀掉应用，这是不完全正确的说法。本文对此作一些分析，欢迎提出异议。
<!--more-->

Google 在 Android Design 文档中对 Recents 的描述如下：  
Recents provides an efficient way of switching between recently used applications. It provides a clear navigation path between multiple ongoing tasks. Switch to an app by touching it. Remove an item by swiping left or right.  
意思是通过 Recents 可以快速切换最近的应用，也可以将应用从中删除，但并没有提到删除的结果是什么。当然了，Design 文档只针对界面设计而不会介绍运行机制的内容。

而就职于 Google Android 部门的 [Dianne Hackborn](https://plus.google.com/105051985738280261832/posts) 的一篇[文章](https://plus.google.com/105051985738280261832/posts/GfwRYCC42uX)讨论到多任务的话题，他在一条评论的回复中说到：

*what specifically happens when you swipe away a recent task is it: (1) kills any background or empty processes of the application (see <http://developer.android.com/guide/topics/fundamentals/processes-and-threads.html#Lifecycle> for what this means), and (2) uses the new <http://developer.android.com/reference/android/app/Service.html#onTaskRemoved(android.content.Intent)> API to tell any services of the application about the task being removed so it can do whatever it thinks is appropriate.*

他在另一篇[文章](http://www.extremetech.com/computing/112013-how-multitasking-works-on-android-and-ios#comment-402624620)的评论中也说到：

*Actually, removing an entry in recent tasks will kill any background processes that exist for the process. It won't directly causes services to stop, however there is an API for them to find out the task was removed to decide if they want this to mean they should stop. This is so that removing say the recent task of an e-mail app won't cause it to stop checking for e-mail.  
If you really want to completely stop an app, you can long press on recent tasks to go to app info, and hit force stop there. For stop is a complete kill of the app -- all processes are killed, all services stopped, all notifications removed, all alarms removed, etc. The app is not allowed to launch again until explicitly requested.*

他的意思是以下几点：  
（1）删除应用只会杀掉与该应用相关的后台进程和空进程，而不会杀掉服务进程，关于 Android 进程的介绍点击[这里](http://blog.xuanxi.me/blog/2014/08/23/android-processes-and-threads/)；  
（2）如果想在删除过程中对 Service 做一些操作，可重写 onTaskRemoved()；  
（3）如果需要关闭整个应用，可以：长按应用->应用信息->强制停止，这样所有相关进程都被杀掉了。

我们可以验证一下：  
（1）打开一个带有 Service 组件的应用，如 QQ；  
（2）从 Recents 将 QQ 删除；  
（3）打开“设置->应用->正在运行（非缓存）”，可以看到 QQ 仍然在运行，处于服务进程状态；  
（4）再打开一个只有 Activity 组件的应用；  
（5）打开“正在运行（缓存）”，可以看到应用已经转为后台进程状态；  
（5）从 Recents 删除；  
（6）打开“正在运行（缓存）”，应用很快就消失了，说明进程已被杀掉。

验证结果证实了 [Dianne Hackborn](https://plus.google.com/105051985738280261832/posts) 的说法。

Android 有一套比较完善的内存管理机制，通常情况下，会试图让进程运行尽量长的时间，以提供更好的用户体验（如更快的启动速度），当内存确实不足的时候，就会按一定规则去杀掉进程，上述这些内存管理工作都是不需要用户直接参与的。对于 Recents，虽然通过它的确可以杀掉后台进程并释放一定空间，但这个本来就是系统自身的工作，并且已经在底层默默进行着，所以，我认为 Android 提供 Recents 界面的初衷就是让用户可以快速切换最近的应用或者说任务，只不过用户把应用删掉之后，Android 可以更确定地把这个应用的空间也释放出来。

补充：从3.1开始，被强制停止的应用处于 Stopped 状态，而 Stopped 状态是接收不到广播的，除非给广播带上 FLAG_INCLUDE_STOPPED_PACKAGES。


