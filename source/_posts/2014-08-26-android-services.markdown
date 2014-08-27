---
layout: post
title: "Android Services"
date: 2014-08-26 17:14:33 +0800
comments: true
categories: Android
---
原文地址：  
<http://developer.android.com/guide/components/services.html>

推荐文章：  
[Android Service完全解析，关于服务你所需知道的一切](http://blog.csdn.net/guolin_blog/article/details/11952435)

本文包括以下几个部分：  
1. The Basics  
2. Creating a Started Service  
3. Creating a Bound Service  
4. Sending Notifications to the User  
5. Running a Service in the Foreground  
6. Managing the Lifecycle of a Service  
<!--more-->

Service 是 Android 提供的四大应用组件之一，它与界面无关，用在后台执行长时间的操作。无论同一个应用还是其它应用的组件都可以启动 Service。你可以通过绑定的方式来和 Service 进行交互，以及实现进程间通信（IPC）。比如，你可以使用 Service 来处理网络请求、播放音乐、读写文件等，所有这些操作都在后台进行，而无需界面支持。

有两种形式的 Service：

（1）Started  
通过 startService() 启动。这种 Service 一旦被启动，就会一直在后台运行，即使启动它的组件已经销毁。通常，它被用来执行单一的不带返回的操作，比如下载或上传文件，当下载或上传成功后，它就自动退出了。 

（2）Bound  
通过 bindService() 启动。这种 Service 用来实现“客户端-服务端”的模型——客户端发出请求，服务端执行请求并返回结果。

值得注意的是，一个 Service 可以同时被 start 和 bind，分别对应 onStartCommand() 和 onBind() 方法。无论是 start 方式还是 bind 方式，任何组件都可以使用 Intent 来启动 Service，就像启动 Activity 一样。不过，你可以在 manifest 文件中将 Service 设置为私有（`android:exported="false"`），这样就可以阻止其它应用的访问了。

注意：Service 运行在 UI 线程上，你应该创建工作线程来执行耗时操作。

##1. The Basics
要使用 Service，你应该派生 Service 的子类，并实现相关的方法：

####onStartCommand()
startService() 方式对应的方法，使用 stopSelf()（内部调用） 或 stopService()（其它组件调用） 来停止 Service。

####onBind()
bindService() 方式对应的方法。无论是否采用绑定的方式，你都必须实现该方法，如果不希望被绑定，这个方法要返回 null。

####onCreate()
在 onStartCommand() or onBind() 之前调用的方法。在 Service 生命周期内只调用一次。

####onDestroy()
销毁 Service 的时候被调用。

###（重要）Should you use a service or a thread?   
如果你只希望在界面交互过程中做一些异步操作（异步操作的时间不会长于当前界面的停留时间），就应该考虑使用线程而不是 Service。比如，你只想在一个 Activity 上播放音乐，就可以在 onCreate() 里创建播放线程，在 onStart() 里开始播放，在 onStop() 里停止播放。另外，你应该考虑使用 AsyncTask 或 HandlerThread 来代替传统的 Thread。 
 
官方这里说得太简单，我作一下补充：  
（1）很多人会笼统地去区别 Service 和 Thread，我认为这是不妥的。Service 和 Thread 不是同一个层次的概念，前者是 Android 的组件机制，后者是现代操作系统的执行机制（程序的调度单位），所有的 Service 都运行在线程上，当然，你也可以在 Service 里创建一个 Thread 来执行任务。之所以容易混淆它们，是因为它们都可以用来执行后台任务，即用法相似。  
（2）我们应该从具体使用场景上去比较 Service 和 Thread。比如上面官方给出的只在前台播放音乐的例子，使用 Service 也可以实现（Bound Service + Thread），只不过直接使用 Thread 更轻量级，而使用 Service 明显更麻烦，开销更大。  
（3）当我们需要执行一个长期的后台任务，比如即时通信中的消息接收、心跳检测，使用 “Service + Thread” 比使用 “Activity + Thread” 能获得更长的后台处理周期。因为离开 Activity 就意味着失去对 Thread 的控制，更坏的结果是，应用进入后台进程状态后有可能被杀掉，Thread 也就随之销毁了，而使用 Service 的服务进程不会轻易被杀掉。

###Declaring a service in the manifest
```
<manifest ... >
  ...
  <application ... >
      <service android:name=".ExampleService" />
      ...
  </application>
</manifest>
```
`android:name` 是必需属性，另外还有 `android:exported`、`android:permission` 和 `android:process` 属性可选。要注意的是，一旦发布了应用，就不要改动 Service 的名字。

官方建议不要为 Service 设置 intent filters，而应该使用显式 Intent 去启动，说是为了应用安全（为什么不安全？？）。如果确实需要设置 intent filters，你必须为 Intent 设置 package。

`android:exported` 属性设置 Service 是否对其它应用开放。


##2. Creating a Started Service
其它组件通过 startService() 启动 Service，同时可以通过 Intent 带上数据，接着，Service 的 onStartCommand() 会被调用。Service 启动后，执行独立的生命周期，与启动它的组件的生命周期无关。要结束 Service ，可在内部调用 stopSelf()，或其它组件调用 stopService()。

要创建 Started Service，可以继承以下两个类：

（1）Service  
Service 的基础类，运行在主线程上。

（2）IntentService  
Service 的子类，整合了一条工作线程（单线程模型），任务放在 onHandleIntent() 方法里执行。

###Extending the IntentService class
IntentService 适用于对并发要求不高的情况，它具体做了以下事情：  
（1）创建一条工作线程来执行任务。  
（2）创建一个工作队列，按顺序处理队列中的任务。  
（3）当所有任务处理完后，自动关闭服务。  
（4）提供了默认的 onBind() 实现。  
（5）提供了默认的 onStartCommand() 实现。  
由此可见，对于 IntentService 的使用，你需要做的仅仅是实现 onHandleIntent()，以及提供构造方法。

补充：  
从源码可知，IntentService 只是简单地使用了 HandlerThread 来实现这种单线程模型，任务队列就是线程的消息队列。

（具体实现略）

###Extending the Service class
如果对并发要求很高，就要用普通的 Service，你可以在 onStartCommand() 里为每个任务创建一条线程，一般情况下使用线程池会比较好。

onStartCommand() 中的返回值告诉系统怎样处理被杀掉的 Service（因内存不足被系统回收），这些返回值起作用的前提是 onStartCommand() 已经执行完毕并返回：

（1）START_NOT_STICKY  
不重建 Service，直到有新的服务请求。

（2）START_STICKY  
重建 Service，并调用 onStartCommand()，但不带有上一次的 intent。适用于长时间运行并等待执行动作的任务，如媒体播放服务。

（3）START_REDELIVER_INTENT  
重建 Service，调用 onStartCommand()，并带有上一次的 intent。适用于立即执行的后台任务，如文件下载。

（具体实现略）

###Starting a Service
（具体实现略）

要注意的是，多个任务请求会调用多次 onStartCommand()，而关闭只需要一次。

###Stopping a service
Service 的生命周期需要开发者来管理，即系统不会主动去关闭一个 Service，除非是资源回收，或者使用 IntentService（IntentService 已经在内部调用了 stopSelf()，因此也不需要开发者去关闭）
，所以，需要显式调用 stopSelf() 或 stopService()。而一旦调用了 stopSelf() or stopService()，系统就会尽快销毁这个 Service。

关闭 Service 时要注意一个问题，如果这个 Service 用于处理并发任务（Service 内部使用多线程），不要在一个任务执行完后就关闭 Service（调用 stopSelf() 或 stopService()），因为关闭的时候，可能一个新的服务请求已经进来，可能这个新请求尚未执行完成，就随着关闭操作而被终止了（stopping at the end of the first request would terminate the second one）。除非你可以很好地保证服务请求的次序，保证所有任务都完成后才关闭，或者关闭之前不会有新的请求进来，但这样可能需要在业务流程上做更多的处理。  
官方对这个问题给出了很好的解决办法，就是使用 stopSelf(int)。留意 onStartCommand(Intent intent, int flags, int startId) 中的第三个参数 startId，每次调用都会生成一个唯一的 startId，用于对应本次服务请求。我们在 Service 内部需要关闭 Service 的地方调用 stopSelf(int)，参数是最近一次已经执行完成的服务请求的 startId。如果调用 stopSelf(int) 的时候有新请求进来，那么 stopSelf 中的 startId 和 onStartCommand 中的startId 就会不一致，stopSelf 就不会调用成功。

##3. Creating a Bound Service

##4. Sending Notifications to the User
Service 一旦启动，可以通过 Toaste 或 Status Bar 来显示一些必要信息，比如告诉用户后台任务已经开始或者结束。

##5. Running a Service in the Foreground
前台服务是用户希望关注其实时状态的服务。它必须使用通知，并处于 "Ongoing" heading 下，这样，除非服务被暂停或者退出前台服务，否则不会从通知栏消失（被划掉）。例如，音乐播放应用使用前台进程处理音乐播放，用户可以在通知栏看到播放状态，并提供一些播放操作。

（具体实现略）

##6. Managing the Lifecycle of a Service
###Implementing the lifecycle callbacks
![service_lifecycle](/images/service_lifecycle.png)
