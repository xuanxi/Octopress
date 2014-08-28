---
layout: post
title: "Android Bound Services"
date: 2014-08-28 10:01:42 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/components/bound-services.html>

Bound Service（绑定的服务）是“客户端-服务端”模型中的服务端部分，它允许其它组件发送请求并接收处理结果，它的典型使用场景是为其它组件提供服务（有交互），而并不是在后台（像 Started Service 那样）长期运行。另外一个重要作用是 IPC。

在深入 Bound Service 之前，最好先了解 [Services](http://blog.xuanxi.me/blog/2014/08/26/android-services/) 的内容。
<!--more-->

##1. The Basics
A bound service is an implementation of the Service class that allows other applications to bind to it and interact with it. To provide binding for a service, you must implement the onBind() callback method. This method returns an IBinder object that defines the programming interface that clients can use to interact with the service.

A client can bind to the service by calling bindService(). When it does, it must provide an implementation of ServiceConnection, which monitors the connection with the service. The bindService() method returns immediately without a value, but when the Android system creates the connection between the client and service, it calls onServiceConnected() on the ServiceConnection, to deliver the IBinder that the client can use to communicate with the service.

Multiple clients can connect to the service at once. However, the system calls your service's onBind() method to retrieve the IBinder only when the first client binds. The system then delivers the same IBinder to any additional clients that bind, without calling onBind() again.

When the last client unbinds from the service, the system destroys the service (unless the service was also started by startService()).

When you implement your bound service, the most important part is defining the interface that your onBind() callback method returns. There are a few different ways you can define your service's IBinder interface and the following section discusses each technique.

注意：Binding to a Started Service  
Service 可以同时被 start 和 bind。你需要同时实现 onBind() 和 onStartCommand()，并且只有在 unbind 和 stop 之后才会被销毁。常见的例子如音乐播放器，既需要在离开界面后在后台播放（start 方式），也需要在有界面的时候控制播放（bind 方式）。


##2. Creating a Bound Service
###Extending the Binder class
If your service is used only by the local application and does not need to work across processes, then you can implement your own Binder class that provides your client direct access to public methods in the service.

如果仅在同应用同进程内使用，

Note: This works only if the client and service are in the same application and process, which is most common. For example, this would work well for a music application that needs to bind an activity to its own service that's playing music in the background.

Here's how to set it up:

In your service, create an instance of Binder that either:
contains public methods that the client can call
returns the current Service instance, which has public methods the client can call
or, returns an instance of another class hosted by the service with public methods the client can call
（1）在 Service 内部创建一个 Binder 实例：提供公有方法供客户端访问

Return this instance of Binder from the onBind() callback method.
（2）在 onBind() 返回 Binder 实例。

In the client, receive the Binder from the onServiceConnected() callback method and make calls to the bound service using the methods provided.
（3）在客户端，通过 onServiceConnected() 方法得到 Binder 实例，用这个 Binder 实例就可以访问 Service 中的字段和方法了。

Note: The reason the service and client must be in the same application is so the client can cast the returned object and properly call its APIs. The service and client must also be in the same process, because this technique does not perform any marshalling across processes.

下面是一个 Bound Service 的例子：
```
public class LocalService extends Service {
    // Binder given to clients
    private final IBinder mBinder = new LocalBinder();
    // Random number generator
    private final Random mGenerator = new Random();

    /**
     * Class used for the client Binder.  Because we know this service always
     * runs in the same process as its clients, we don't need to deal with IPC.
     */
    public class LocalBinder extends Binder {
        LocalService getService() {
            // Return this instance of LocalService so clients can call public methods
            return LocalService.this;
        }
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }

    /** method for clients */
    public int getRandomNumber() {
      return mGenerator.nextInt(100);
    }
}
```
LocalBinder 提供了 getService() 方法让客户端得到 LocalService 实例，这样，客户端就可以使用 Service 的公共接口了。

下面的代码展示了一个 Activity 绑定 LocalService，并在点击按钮时调用 getRandomNumber() 接口：
```
public class BindingActivity extends Activity {
    LocalService mService;
    boolean mBound = false;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
    }

    @Override
    protected void onStart() {
        super.onStart();
        // Bind to LocalService
        Intent intent = new Intent(this, LocalService.class);
        bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
    }

    @Override
    protected void onStop() {
        super.onStop();
        // Unbind from the service
        if (mBound) {
            unbindService(mConnection);
            mBound = false;
        }
    }

    /** Called when a button is clicked (the button in the layout file attaches to
      * this method with the android:onClick attribute) */
    public void onButtonClick(View v) {
        if (mBound) {
            // Call a method from the LocalService.
            // However, if this call were something that might hang, then this request should
            // occur in a separate thread to avoid slowing down the activity performance.
            int num = mService.getRandomNumber();
            Toast.makeText(this, "number: " + num, Toast.LENGTH_SHORT).show();
        }
    }

    /** Defines callbacks for service binding, passed to bindService() */
    private ServiceConnection mConnection = new ServiceConnection() {

        @Override
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // We've bound to LocalService, cast the IBinder and get LocalService instance
            LocalBinder binder = (LocalBinder) service;
            mService = binder.getService();
            mBound = true;
        }

        @Override
        public void onServiceDisconnected(ComponentName arg0) {
            mBound = false;
        }
    };
}
```

###Using a Messenger

##3. Binding to a Service
###Additional notes

##4. Managing the Lifecycle of a Bound Service