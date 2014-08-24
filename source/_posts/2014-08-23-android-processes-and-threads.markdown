---
layout: post
title: "Android Processes and Threads"
date: 2014-08-23 12:05:49 +0800
comments: true
categories: Android
---
原文地址：<http://developer.android.com/guide/components/processes-and-threads.html>

在一个应用组件启动的时候，如果当前这个应用没有其它组件在运行，Android 就会为该应用分配一个带有一条独立执行线程（称之为 main 线程）的 Linux 进程。默认情况下，这个应用后来启动的所有组件都会运行在相同的进程和线程中。当然，你也可以为不同的组件创建不同的进程，以及为同一个进程创建多条线程。
<!--more-->

##1. Processes
一般情况下，一个应用所有的组件都运行在同一个进程里，进程的名字是应用的根包名。如果你需要为某个组件提供独立的进程，可以在 manifest 文件中设置。

manifest 文件中的四大组件元素——`<activity>`、`<service>`、`<receiver>`、`<provider>`——都支持通过 `android:process` 属性来指定运行的进程，你可以为每个组件创建独立的进程，或者几个组件共享一个进程。也可以为不同应用的组件配置同一个进程，前提是这些应用使用相同的 `android:sharedUserId `和签名。

补充：通过为不同应用设置相同的 sharedUserId 和签名，可以共享各自沙盒内的数据文件，比如数据库和 SharedPreference。如果再给它们的组件设置同一个进程，那么就可以共享任何数据了（文件数据和内存数据）。

另外，`<application>` 元素也支持 `android:process`，可以为所有的组件设置同一个进程。

###Process lifecycle
Android 会试图让进程运行尽可能长的时间，只有在内存不足时，才会终止那些旧的进程。Android 将所有进程放入一个“重要性等级树”中，这个“重要性等级树”基于组件的运行状态，低等级的进程会首先被终止，然后是等级高一点的进程，以此类推。

重要性等级分五个级别，按重要性降低排列如下：

####（1）前台进程
前台进程是用户正在交互的进程，包括以下情况：  
1）持有一个与用户交互的 Activity（onResume() 被调用）；  
2）持有一个 Service，这个 Service 被绑定到一个正在与用户交互的 Activity 中；  
3）持有一个 Service，它调用了 startForeground()，也叫前台 Service（如在通知栏显示的音乐播放服务）；  
4）持有一个正在执行生命周期方法的 Service，如 onCreate()、onStart()、or onDestroy()；  
5）持有一个正在执行 onReceive() 的 BroadcastReceiver。

通常，在任何时间内只会存在少数的前台进程，它们只会在内存空间紧张到不能保证全部前台进程都正常运行的情况下被杀掉。那时，设备处于内存分页状态（增加内存空间的机制，见下面的解释），只能通过杀掉一些前台进程来保持用户界面的响应。

Paging：In computer operating systems, paging is one of the memory-management schemes by which a computer can store and retrieve data from secondary storage for use in main memory.

####（2）可视进程
可视进程不含有任何前台组件，但仍然对用户看见的界面有影响：  
1）持有一个 Activity，它不处于前台与用户交互，但仍然可见（调用了 onPause()），比如打开一个对话框后，这个 Activity 位于对话框下面，处于可见状态；
2）持有一个 Service，它被绑定到一个可视或前台 Activity（这里官方说到前台 Activity，那不就是前台进程了么？？）；

####（3）服务进程
服务进程中运行着一个通过 startService() 启动的 Service。虽然服务进程跟用户可见的界面没有关联，但它通常在处理用户关心的事情，比如后台音乐播放、文件下载等。

####（4）后台进程
后台进程持有一个用户不可见的 Activity（调用了 onStop() 方法，注意并不是 onDestroy()），这些进程不会对用户体验产生影响，所以系统可以在任何时候将它们杀掉。一般会有许多后台进程在运行着，系统通过 LRU （最近使用）列表来管理这里进程，以确保最常使用的进程在最后才被杀掉。

####（4）空进程
空进程中不包含任何处在生命周期中的组件，保留空进程的原因仅仅是作为缓存使用，当下次启动相关联的组件的时候，进程可以快速运行起来。

Android 会尽量将一个进程放在它能到达的最高的等级。比如，进程中持有一个 Service 和 一个可视的 Activity，这个进程将被视为可视进程，而不是服务进程。

另外，当一个进程为另一个进程提供服务时，这个进程不会比另一个进程的等级低。比如，进程 A 中的 Content Provider 为进程 B 提供服务，或者，进程 A 的 Service 被绑定到进程 B，这两个例子中的进程 A 的等级至少和进程 B 一样高。

（非常重要的总结）  
因为服务进程比后台进程的重要性高，所以如果要在 Activity 中进行一些耗时操作，最好把这些操作放到 Service 中，尤其是这些操作会比 Activity 运行时间更长的情况下。比如，对于 Activity 中的图片上传操作，应该开启一个 Service 去执行，以便在退出 Activity 后上传操作可以继续在服务进程中进行，否则，进程在转为后台进程甚至空进程后，很有可能被杀死，上传操作也随之中断。所以说，使用 Service 可以保证某些操作至少运行在“服务进程”的级别，而不管 Activity 是否已经退出。同样地，BroadcastReceiver 中的耗时操作也应该放到 Service 中进行（Android 对 onReceive() 方法有时间限制，通常为5秒）。

##2. Threads
当应用启动的时候，系统会为之创建一条主线程。主线程非常重要，它负责用户界面事件的分发和处理，包括绘图事件、UI 事件等，主线程通常也被叫做 UI 线程。所有同一个进程中的系统组件都运行在 UI 线程上，所以，系统回调方法（像 onKeyDown() 或者生命周期方法）都会在 UI 线程上执行。

例如，当用户点击按钮，应用的 UI 线程会将点击事件分发给控件，控件就会置当前状态为按下状态，同时发送 invalidate 请求到 UI 线程的事件队列，UI 线程取出请求消息并通知控件重绘。

当交互过程中要执行一些耗时操作时，比如请求网络或读取数据库，可能会阻塞 UI 线程，以致不能分发和处理其它事件，包括绘图事件。如果阻塞超过5秒，系统会弹出 ANR 对话框。这种糟糕的体验会令用户退出你的应用，甚至卸载。

另外，UI 组件不是线程安全的，你不能在工作线程上处理 UI。综上所述，有两种规则去处理 Android 的单线程模型：  
（1）不要阻塞 UI 线程；  
（2）不要在非 UI 线程上操作 UI。

###Worker threads
Android 的单线程模型要求你不能阻塞 UI 线程，所以你应该将耗时操作放在新的线程上执行，称之为后台线程或工作线程。

例如，下面的代码是一个点击事件监听器，使用工作线程下载图片并显示在 ImageView 上：  
```
public void onClick(View v) {
    new Thread(new Runnable() {
        public void run() {
            Bitmap b = loadImageFromNetwork("http://example.com/image.png");
            mImageView.setImageBitmap(b);
        }
    }).start();
}
```
但上面的代码并没有遵守第二条规则：不要在非 UI 线程上操作 UI。

Android 提供了几种方法支持从工作线程中访问 UI 线程：  
（1）Activity.runOnUiThread(Runnable)  
（2）View.post(Runnable)  
（3）View.postDelayed(Runnable, long)  

于是，上面的代码可以修改为：
```
public void onClick(View v) {
    new Thread(new Runnable() {
        public void run() {
            final Bitmap bitmap = loadImageFromNetwork("http://example.com/image.png");
            mImageView.post(new Runnable() {
                public void run() {
                    mImageView.setImageBitmap(bitmap);
                }
            });
        }
    }).start();
}
```
修改之后的代码就保证线程安全了。

可是，随着这种跨线程 UI 操作的增多，代码会变得复杂和难于维护，为了在工作线程中能够处理更复杂的交互，你可以使用 Hanlder，将 UI 操作都放到 Handler 中执行。不过，AsyncTask 也许是更好的解决方案。

###Using AsyncTask
AsyncTask 允许你在界面中执行异步工作，它将阻塞操作放在工作线程上运行，并将结果发送到 UI 线程上，这样就避免了你直接去使用线程和 Handler。

要使用 AsyncTask，你需要从 AsyncTask 类派生一个子类，并实现 doInBackground() 方法，这个方法运行在线程池中。如果要更新 UI，你还需要实现 onPostExecute() 方法，这个方法接收来自 doInBackground() 的返回，以及运行在 UI 线程上。

例如，你可以这样实现：
```
public void onClick(View v) {
    new DownloadImageTask().execute("http://example.com/image.png");
}

private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
    /** The system calls this to perform work in a worker thread and
      * delivers it the parameters given to AsyncTask.execute() */
    protected Bitmap doInBackground(String... urls) {
        return loadImageFromNetwork(urls[0]);
    }
    
    /** The system calls this to perform work in the UI thread and delivers
      * the result from doInBackground() */
    protected void onPostExecute(Bitmap result) {
        mImageView.setImageBitmap(result);
    }
}
```
现在，UI 操作安全了，代码也比较简洁，因为它将后台操作与前台 UI 操作分开了。

注意：在你使用工作线程的时候可能会遇到另一个问题，就是当前的 Activity 会因为配置的改变而重建（如改变屏幕方向），以致销毁了工作线程。要解决这个问题，可参考 Shelves 源码。

###Thread-safe methods
某些情况下，你的方法需要被多个线程调用，因此要确保这些方法是线程安全的。

##3. Interprocess Communication
Android 使用远程过程调用（RPC）来实现进程间通信（IPC），即在 Activity 或其它应用组件中调用方法，在远程（其它进程）执行方法，并将结果返回给调用者。这种过程的实现，需要将方法调用和附带的数据解析到系统可以识别的水平，将它们从本地进程和地址空间传输到远程目标进程和地址空间，然后重新组装并执行调用，返回值会按相反的方向传输回去。Android 已经提供了这种 RPC 通信过程的实现，你只需要定义和实现远程调用接口即可。

另外，要实现 IPC，你的应用必须使用 bindService() 绑定到一个（其它应用的） Service。