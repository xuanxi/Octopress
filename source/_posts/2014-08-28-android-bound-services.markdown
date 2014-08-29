---
layout: post
title: "Android Bound Services"
date: 2014-08-28 10:01:42 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/components/bound-services.html>

Bound Service（绑定的服务）是“客户端-服务端”模型中的服务端部分（下文中提到的客户端和使用 Service 的其它组件是一个意思），它接收其它组件的服务请求并返回处理结果。它的典型使用场景是为其它组件提供有交互的服务，而并不是在后台（像 Started Service 那样）长期运行。另外一个重要作用是 IPC。

在深入 Bound Service 之前，最好先了解 [Services](http://blog.xuanxi.me/blog/2014/08/26/android-services/) 的内容。
<!--more-->

##1. The Basics
组件通过 bindService() 方法绑定 Service，同时需要实现一个 ServiceConnection，其中的 onServiceConnected() 方法用来传递 IBinder 对象，而 IBinder 对象就是“客户端-服务端”交互的接口对象。

Android 支持多个组件同时绑定一个 Service，不过，只在第一个组件绑定时系统会调用一次 onBind()，后面的绑定会直接获得 IBinder 对象而不需要再次调 onBind()。

注意：   
Service 可以同时被 start 和 bind，如果这样，你需要同时实现 onBind() 和 onStartCommand()，并且只有在 unbind 和 stop 之后才会被销毁。常见的例子如音乐播放器，既需要在离开界面后在后台播放（start 方式），也需要在有界面的时候控制播放（bind 方式）。


##2. Creating a Bound Service
Service 通过 IBinder 来提供对外接口，以下为三种接口定义方式：  

（1）继承 Binder 类  
适用于同应用同进程内绑定 Service 的情况。Binder 类实现了 IBinder 接口。

（2）使用 Messenger  
适用于简单的 IPC，单线程执行任务。上层使用 Handler 和 Message 来运作，底层基于 AIDL。

（3）使用 AIDL  
标准的 IPC。相比 Messenger，AIDL 支持多线程任务，但要注意处理线程安全问题。

###Extending the Binder class
如果仅在同应用同进程内使用 Service，可以通过实现 Binder 来提供对外接口。

下面是具体的实现步骤：  
（1）在 Service 内部定义一个 Binder 类，类内部提供公共方法供客户端访问，可以通过这个方法返回 Service 对象，或者 Service 所持有的其它对象。    
（2）使用上一步的 Binder 类创建一个 IBinder 对象，并在 onBind() 返回这个对象。  
（3）在客户端中，通过 onServiceConnected() 方法得到 IBinder 对象，用这个 IBinder 对象就可以访问 Service 中的字段和方法了。

下面是一个 Bound Service 的实现：
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
LocalBinder 提供了 getService() 方法让客户端得到 LocalService 对象，这样，客户端就可以使用 Service 的公共接口了。

下面的代码展示了一个 Activity 绑定 LocalService，并在点击按钮时调用 getRandomNumber() 接口方法：
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
对于简单的 IPC 任务，使用 Messenger 比使用 AIDL 更简单，因为 Messenger 使用一个消息队列来处理所有服务请求，而纯粹的 AIDL 需要处理并发的服务请求。

以下是实现要点：  
（1）Service 内部定义一个 Handler 类，用来接收并处理消息。  
（2）创建一个 Messenger 对象，创建时还要使用上一步的 Handler 对象。  
（3）在 onBind() 中由 Messenger 创建 IBinder 对象，返回给客户端。  
（4）客户端使用 IBinder 实例化一个 Messenger，用来向 Service 发送消息。  
（5）Service 接收消息，并在 Handler 中处理。

可见，使用 Messenger 的话，Service 不能提供方法给客户端直接调用，而只能通过消息来交互。

Service 示例：  
```
public class MessengerService extends Service {
    /** Command to the service to display a message */
    static final int MSG_SAY_HELLO = 1;

    /**
     * Handler of incoming messages from clients.
     */
    class IncomingHandler extends Handler {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case MSG_SAY_HELLO:
                    Toast.makeText(getApplicationContext(), "hello!", Toast.LENGTH_SHORT).show();
                    break;
                default:
                    super.handleMessage(msg);
            }
        }
    }

    /**
     * Target we publish for clients to send messages to IncomingHandler.
     */
    final Messenger mMessenger = new Messenger(new IncomingHandler());

    /**
     * When binding to the service, we return an interface to our messenger
     * for sending messages to the service.
     */
    @Override
    public IBinder onBind(Intent intent) {
        Toast.makeText(getApplicationContext(), "binding", Toast.LENGTH_SHORT).show();
        return mMessenger.getBinder();
    }
}
```

客户端示例：  
```
public class ActivityMessenger extends Activity {
    /** Messenger for communicating with the service. */
    Messenger mService = null;

    /** Flag indicating whether we have called bind on the service. */
    boolean mBound;

    /**
     * Class for interacting with the main interface of the service.
     */
    private ServiceConnection mConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className, IBinder service) {
            // This is called when the connection with the service has been
            // established, giving us the object we can use to
            // interact with the service.  We are communicating with the
            // service using a Messenger, so here we get a client-side
            // representation of that from the raw IBinder object.
            mService = new Messenger(service);
            mBound = true;
        }

        public void onServiceDisconnected(ComponentName className) {
            // This is called when the connection with the service has been
            // unexpectedly disconnected -- that is, its process crashed.
            mService = null;
            mBound = false;
        }
    };

    public void sayHello(View v) {
        if (!mBound) return;
        // Create and send a message to the service, using a supported 'what' value
        Message msg = Message.obtain(null, MessengerService.MSG_SAY_HELLO, 0, 0);
        try {
            mService.send(msg);
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
    }

    @Override
    protected void onStart() {
        super.onStart();
        // Bind to the service
        bindService(new Intent(this, MessengerService.class), mConnection,
            Context.BIND_AUTO_CREATE);
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
}
```
上面的例子没有展示 Service 如何向客户端做出响应，如果你要接收响应，可参考 [MessengerService.java](http://developer.android.com/resources/samples/ApiDemos/src/com/example/android/apis/app/MessengerService.html) 和 [MessengerServiceActivities.java](http://developer.android.com/resources/samples/ApiDemos/src/com/example/android/apis/app/MessengerServiceActivities.html)。

##3. Binding to a Service
组件调用 bindService() 绑定 Service，系统随后会调用 Service 的 onBind()，并返回 IBinder 对象用于与客户端交互。

绑定操作是异步的，bindService() 方法调用后会立即返回，而且不会带回 IBinder 对象。要得到 IBinder 对象，客户端必须实现 ServiceConnection 类，并在它的回调方法中得到 IBinder。

注意：  
四大应用组件中，BroadcastReceiver 不能绑定 Service。

（bind 的具体实现略）

###补充说明
（1）在客户端调用 Service 接口的时候，应该捕获 DeadObjectException 异常，以确认与 Service 的连接是否中断，这是唯一一个远程接口会抛出的异常。  
（2）Objects are reference counted across processes.（Service 对象的引用计数是跨进程的？）    
（3）应该成对使用 bind 和 unbind 方法。可以 bind during onStart() and unbind during onStop()，或者 bind during onCreate() and unbind during onDestroy().  
（4）你不应该 bind and unbind during your activity's onResume() and onPause()，因为 onResume() 和 onPause() 这两个方法在每次生命周期改变时都会被调用，你不得不控制后台处理的时间在最小范围，并且如果多个 Activity 切换（这些 Activity 都绑定了 Service），bind 和 unbind 就会频繁执行，这明显是不好的。

##4. Managing the Lifecycle of a Bound Service

![service_binding_tree_lifecycle](/images/service_binding_tree_lifecycle.png)
