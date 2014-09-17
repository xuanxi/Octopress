---
layout: post
title: "Android Tasks and Back Stack"
date: 2014-09-02 12:10:18 +0800
comments: true
categories: 
---
原文：  
<http://developer.android.com/guide/components/tasks-and-back-stack.html>

一个应用通常会包含多个 Activity，每个 Activity 完成特定的工作，并可以启动其它 Activity。比如，邮件应用使用一个 Activity 来展示邮件列表，点击一封邮件将会打开一个新的 Activity 来展示邮件内容。本文介绍 Android 如何通过 Task 和 Back Stack 来管理 Activity。
<!--more-->

一个 Activity 甚至可以打开其它应用的 Activity。比如，你的应用要发送邮件，你可以定义一个 Intent 来启动设备中支持这个 Intent 的邮件应用，并进入到该应用的邮件编辑页面（如果设备中有多个邮件应用，系统会以列表形式让你选择启动哪一个），当邮件发送成功后，会退回到你的应用界面，整个流程看起来，发送邮件的界面就像是你的应用界面的一部分。由此可见，尽管打开的 Activity 可能来自不同的应用，但系统会将所有 Activity 放到同一个 Task 里，以实现无缝的用户体验。

一个 Task 是许多 Activity 的集合，这些 Activity 表示用户对某项事务的一个完整的交互过程，这些 Activity 同时受 Back Stack（返回栈）的管理，以打开的顺序排列在栈中。

大多数 Task 由 Home 界面开始，当用户在 Launcher（应用启动器）或 Home 点击应用图标，该应用的 Task 将会进入前台。在启动应用时，如果该应用的 Task 不存在（即最近没有使用过，因为如果最近使用过，通常会存在后台 Task），系统会为应用创建一个新的 Task，并以起始 Activity（通常是 main Activity）作为栈的第一个 Activity。

如果从当前 Activity 启动另一个 Activity，新的 Activity 将被置于栈顶，同时获得界面焦点，而前一个 Activity 仍会保留在栈里，并处于 stopped 状态。当用户点击返回按键，新的 Activity 会出栈（被销毁），而前一个 Activity 恢复。由此可见，Back stack 其实就是一个“后进先出”的结构。

![diagram_backstack](/images/diagram_backstack.png)

如果用户继续点击返回按键，栈中的所有 Activity 都被弹出，直到返回 Home（或者 Task 启动前正在运行的 Activity），同时，这个 Task 不再存在。

每个 Task 都是独立的单元，当一个新的 Task 被启动，或者用户按 Home 键返回 Home 界面，前一个 Task 将会退到后台（看不见了），Task 中的所有 Activity 都会处于 stopped 的状态，不过，Task 的 Back Stack 仍然保持不变，当它重新回到前台时，界面将会显示 Task 中最顶端的 Activity。

![diagram_multitasking](/images/diagram_multitasking.png)

注意：  
后台可以存在多个 Task，不过，如果后台 Task 过多，系统可能会销毁一些以腾出内存空间，被销毁 Task 中的 Activity 的状态都会丢失。

如果一个 Activity 可以被应用中的多个 Activity 启动，默认情况下，每次启动都会创建一个该 Activity 的实例，并新实例放在栈顶。如下图所示：

![diagram_multiple_instances](/images/diagram_multiple_instances.png)

这里做一下总结：

（1）当 Activity A 启动 Activity B，A stopped，但系统会保存 A 的状态（比如滑动的位置和输入的信息）。当用户按返回按键从 B 返回，A 就会以上一次的状态复原。

（2）当用户通过 Home 键离开一个 Task（离开应用），当前的 Activity stopped，Task 也随之进入后台，但系统会保存 Task 中所有 Activity 的状态。当用户稍后通过 Launcher 图标再次打开应用时，Task 就回到前台，并显示栈顶的 Activity 及复原离开时的状态。

（3）如果用户点击返回按键，当前 Activity 弹出栈并被销毁，上一个 Activity 恢复显示。系统不会保存被销毁的 Activity 的状态。

（4）Activity 可以被多次实例化，甚至从其它的 Task 实例化。

##Saving Activity State
就如上面所讨论的，在 Activity stopped 的时候，系统会保存 Activity 的状态，这样，当用户返回时，界面显示会跟离开时一样。但是，在 Activity destroyed 的时候，你可以，或者说应该使用  Activity 提供回调方法 onSaveInstanceState() 保存当前状态。

补充：开发者用 onSaveInstanceState() 保存的状态通常是业务相关的，比如视频或音乐的播放进度。

##Managing Tasks
上面讨论的 Android 管理 Task 和 Back Stack 的方式适合大多数使用场景，你不需要关心一个 Activity 的入栈出栈。可是，如果你希望打破常规，比如你希望在一个新的 Task 中打开 Activity，而不是放到当前 Task 的栈顶；或者希望可以重用 Task 中已经存在的 Activity 实例，而不是再次创建新的实例；又或者希望在离开 Task 时可以保留 root Activity 等等。你可以通过设置 manifest 文件中的  `<activity>` 元素，或者为 startActivity() 方法的参数 Intent 设置 flag，来实现多种 Task 管理。 

例如，`<activity>` 元素可设置的属性有：  
taskAffinity  
launchMode  
allowTaskReparenting  
clearTaskOnLaunch  
alwaysRetainTaskState  
finishOnTaskLaunch  

以及可以设置的 intent flags 值有：  
FLAG_ACTIVITY_NEW_TASK  
FLAG_ACTIVITY_CLEAR_TOP  
FLAG_ACTIVITY_SINGLE_TOP

下面的内容将会详细讲解如何使用这些设置。

###Defining launch modes
launch modes（运行模式）用来描述一个新的 Activity 实例和当前 Task 的关系，有两种方式定义 launch modes：

使用 manifest 文件  
使用 Intent flags

比如 Activity A starts Activity B，B 可以在 manifest 中设置 launch mode 的同时，A 也可以设置 Intent flag。在 A 和 B 都设置的情况下，A 的设置会覆盖 B 的设置。

注意：某些 launch modes 可以用在 manifest 上但不能用在 Intent flag 上；同样，某些 launch modes 可以用在 Intent flag 上但不能用在 manifest 上。

####（1）Using the manifest file
设置 `<activity>` 元素的 launchMode 属性，有四种 launchMode：

"standard" (默认模式)  
系统会在当前的 Task 中创建 Activity 的新实例。Activity 可以被多次实例化，这些实例可以存在于一个 Task 中，或者分布在不同的 Task 中（Android 的组件机制，其它的应用可以使用你的 Activity）。

"singleTop"  
当一个 Activity 实例位于栈顶的时候，再通过 Intent 跳转到本身这个 Activity，将不会创建一个新的实例压入栈中（不会调用 onCreate()，而调用 onNewIntent()）。例如：现在栈的情况为：A B C D。D 的 Launch mode 设置成了 singleTop，那么在 D 中启动 Intent 跳转到 D，那么将不会新创建一个 D 的实例压入栈中，此时栈的情况依然为：A B C D。但是如果此时  B 的模式也是 singleTop，D 跳转到 B，则会新建一个 B 的实例压入栈，因为此时 B 不位于栈顶，此时栈的情况就变成了：A B C D B。

"singleTask"
The system creates a new task and instantiates the activity at the root of the new task. However, if an instance of the activity already exists in a separate task, the system routes the intent to the existing instance through a call to its onNewIntent() method, rather than creating a new instance. Only one instance of the activity can exist at a time.
Note: Although the activity starts in a new task, the Back button still returns the user to the previous activity.

"singleInstance".
Same as "singleTask", except that the system doesn't launch any other activities into the task holding the instance. The activity is always the single and only member of its task; any activities started by this one open in a separate task.



####（2）Using Intent flags

###Handling affinities
###Clearing the back stack
###Starting a task
