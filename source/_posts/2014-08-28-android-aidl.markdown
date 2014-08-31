---
layout: post
title: "Android AIDL"
date: 2014-08-28 10:13:19 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/components/aidl.html>

AIDL 与其它 IDLs（接口定义语言）类似，它允许你定义客户端和服务端都能够识别的编程接口，然后通过 IPC 就可以实现两端的通信。不过，在使用 AIDL 之前，你应该思考是否一定要采用 AIDL：如果你不需要跨进程通信，那么直接实现 Binder 即可；如果你需要扩进程通信，但不要求处理多线程任务，那么使用 Messenger 即可。

在你开始设计 AIDL 接口之前，要意识到接口调用就是函数调用，这涉及到执行线程的问题，你不能假设调用在什么线程上执行，而事实上，这取决于调用源自本地进程还是远程进程：  
（1）在本地进程调用的话，接口在调用者所在线程上执行。比如，如果在 UI 线程调用，就在 UI 线程执行。不过，这样的话就没必要用到 AIDL 了，而应该直接用 Binder。（这样说来，AIDL 其实也可以用在非 IPC 场景？）  
（2）从远程进程调用的话，系统会在本地进程中建立一个线程池，并在线程池中取出线程来执行。要注意处理线程安全。  
（3）The oneway keyword modifies the behavior of remote calls. When used, a remote call does not block; it simply sends the transaction data and immediately returns. The implementation of the interface eventually receives this as a regular call from the Binder thread pool as a normal remote call. If oneway is used with a local call, there is no impact and the call is still synchronous.
<!--more-->

##1. Defining an AIDL Interface
###1.1 Create the .aidl file
AIDL 使用 Java 语法来声明接口，接口可以是一个或多个带参数和返回值的方法，这些参数和返回值可以是任何类型，甚至是其他 AIDL 生成的接口。

AIDL 默认支持以下数据类型：  
（1）所有 Java 基本类型（such as int, long, char, boolean, and so on）  
（2）String  
（3）CharSequence  
（4）List  
All elements in the List must be one of the supported data types in this list or one of the other AIDL-generated interfaces or parcelables you've declared. A List may optionally be used as a "generic" class (for example, List<String>). The actual concrete class that the other side receives is always an ArrayList, although the method is generated to use the List interface.  
（5）Map  
All elements in the Map must be one of the supported data types in this list or one of the other AIDL-generated interfaces or parcelables you've declared. Generic maps, (such as those of the form Map<String,Integer> are not supported. The actual concrete class that the other side receives is always a HashMap, although the method is generated to use the Map interface.

如果不是上面所列举的类型，你就需要使用 import 声明。

下面是一个 .aidl 例子：
```
// IRemoteService.aidl
package com.example.android;

// Declare any non-default types here with import statements

/** Example service interface */
interface IRemoteService {
    /** Request the process ID of this service, to do evil things with it. */
    int getPid();

    /** Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);
}
```
只要将定义好的 .aidl 文件放入 src/ directory 路径下，SDK 就会在构建工程时自动生成 IBinder 接口文件在 gen/ directory，生成的文件名与 .aidl 文件名一样，并以 .java 为扩展名。

如果你使用 Eclipse，并打开了自动构建选项，IBinder 接口文件会立即生成。
 
###1.2 Implement the interface
在上一步生成的接口文件中包含了一个 Stub 子类，它是 .aidl 接口的抽象实现。Stub 同时定义了一些辅助方法，尤其是 asInterface()，这个方法在客户端会使用到，用于返回 IBinder 对象。

接下来是实现 Stub 类：
```
private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
    public int getPid(){
        return Process.myPid();
    }
    public void basicTypes(int anInt, long aLong, boolean aBoolean,
        float aFloat, double aDouble, String aString) {
        // Does nothing
    }
};
```
在实现 AIDL 接口时还有几点要注意的：  
（1）接口不保证会在（Service 所在的本地进程的）主线程上执行，因此，你一开始就应该考虑多线程的问题，以确保线程安全。  
（2）默认情况下，RPC（远程过程调用，是 IPC 的一种形式）是同步的，如果你确定调用是耗时的，就不应该在主线程上调用，而应该在工作线程上调用。  
（3）远程接口上的异常不会传递到调用方法上。

###1.3 Expose the interface to clients
一旦实现了 Stub，你就可以将它暴露给客户端了。在 Service 的 onBind() 方法返回 Stub 对象。
```
public class RemoteService extends Service {
    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Return the interface
        return mBinder;
    }

    private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
        public int getPid(){
            return Process.myPid();
        }
        public void basicTypes(int anInt, long aLong, boolean aBoolean,
            float aFloat, double aDouble, String aString) {
            // Does nothing
        }
    };
}
```
同时，需要将 .aidl 文件放在客户端工程的 src/ directory 中，以便生成 Java 形式的接口文件。

现在，当客户端调用 bindService() 去连接 Service 时，客户端的 onServiceConnected() 回调方法里会得到 Service 在 onBind() 中返回的 mBinder 对象。

当客户端从 onServiceConnected() 中得到 IBinder 对象后，还需要用 YourServiceInterface.Stub.asInterface(service) 方法将 IBinder 转换为 YourServiceInterface 类型（也就是上面生成的 Java 类），这样才能调用定义的接口。如以下代码：

```
IRemoteService mIRemoteService;
private ServiceConnection mConnection = new ServiceConnection() {
    // Called when the connection with the service is established
    public void onServiceConnected(ComponentName className, IBinder service) {
        // Following the example above for an AIDL interface,
        // this gets an instance of the IRemoteInterface, which we can use to call on the service
        mIRemoteService = IRemoteService.Stub.asInterface(service);
    }

    // Called when the connection with the service disconnects unexpectedly
    public void onServiceDisconnected(ComponentName className) {
        Log.e(TAG, "Service has unexpectedly disconnected");
        mIRemoteService = null;
    }
};
```

##2. Passing Objects over IPC
通过 IPC，你可以将一个对象传输到另一个进程。Android 提供了 Parcelable 来实现这个传输过程：Parcelable 将对象拆解为有序的基本类型，装载到 Parcel 上，然后在其它进程重新组装为对象。

创建支持 Parcelable 协议的类，要做以下几点：

（1）实现 Parcelable 接口。  
（2）实现 writeToParcel 方法，将数据写入 Parcel。  
（3）增加一个静态字段——CREATOR，实现 Parcelable.Creator 接口。  
（4）最后，创建 .aidl 文件，在其中声明这个 Parcelable 类。

下面的 Rect.aidl 文件中声明了一个 Parcelable 类：
```
package android.graphics;

// Declare Rect so AIDL can find it and knows that it implements
// the parcelable protocol.
parcelable Rect;
```

下面是一个实现 Parcelable 协议的例子：
```
import android.os.Parcel;
import android.os.Parcelable;

public final class Rect implements Parcelable {
    public int left;
    public int top;
    public int right;
    public int bottom;

    public static final Parcelable.Creator<Rect> CREATOR = new
Parcelable.Creator<Rect>() {
        public Rect createFromParcel(Parcel in) {
            return new Rect(in);
        }

        public Rect[] newArray(int size) {
            return new Rect[size];
        }
    };

    public Rect() {
    }

    private Rect(Parcel in) {
        readFromParcel(in);
    }

    public void writeToParcel(Parcel out) {
        out.writeInt(left);
        out.writeInt(top);
        out.writeInt(right);
        out.writeInt(bottom);
    }

    public void readFromParcel(Parcel in) {
        left = in.readInt();
        top = in.readInt();
        right = in.readInt();
        bottom = in.readInt();
    }
}
```
这个 Recent 类要处理的数据比较简单（都是整型），你可以查看 [Parcel](http://developer.android.com/reference/android/os/Parcel.html) 支持的其它数据类型。

##3. Calling an IPC Method
下面是调用远程接口的步骤：

（1）将 .aidl 文件放置到源码目录下。  
（2）声明 IBinder 对象。  
（3）实现 ServiceConnection。  
（4）调用 Context.bindService()。  
（5）在 onServiceConnected() 中调用 YourInterfaceName.Stub.asInterface((IBinder)service) 得到 IBinder 对象。  
（6）到这步，你就可以使用接口方法了。注意，你应该捕获 DeadObjectException。  
（7）要断开连接，调用 Context.unbindService()。

A few comments on calling an IPC service:  
Objects are reference counted across processes.  
You can send anonymous objects as method arguments.

下面是使用 AIDL 接口的例子：
```
public static class Binding extends Activity {
    /** The primary interface we will be calling on the service. */
    IRemoteService mService = null;
    /** Another interface we use on the service. */
    ISecondary mSecondaryService = null;

    Button mKillButton;
    TextView mCallbackText;

    private boolean mIsBound;

    /**
     * Standard initialization of this activity.  Set up the UI, then wait
     * for the user to poke it before doing anything.
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.remote_service_binding);

        // Watch for button clicks.
        Button button = (Button)findViewById(R.id.bind);
        button.setOnClickListener(mBindListener);
        button = (Button)findViewById(R.id.unbind);
        button.setOnClickListener(mUnbindListener);
        mKillButton = (Button)findViewById(R.id.kill);
        mKillButton.setOnClickListener(mKillListener);
        mKillButton.setEnabled(false);

        mCallbackText = (TextView)findViewById(R.id.callback);
        mCallbackText.setText("Not attached.");
    }

    /**
     * Class for interacting with the main interface of the service.
     */
    private ServiceConnection mConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // This is called when the connection with the service has been
            // established, giving us the service object we can use to
            // interact with the service.  We are communicating with our
            // service through an IDL interface, so get a client-side
            // representation of that from the raw service object.
            mService = IRemoteService.Stub.asInterface(service);
            mKillButton.setEnabled(true);
            mCallbackText.setText("Attached.");

            // We want to monitor the service for as long as we are
            // connected to it.
            try {
                mService.registerCallback(mCallback);
            } catch (RemoteException e) {
                // In this case the service has crashed before we could even
                // do anything with it; we can count on soon being
                // disconnected (and then reconnected if it can be restarted)
                // so there is no need to do anything here.
            }

            // As part of the sample, tell the user what happened.
            Toast.makeText(Binding.this, R.string.remote_service_connected,
                    Toast.LENGTH_SHORT).show();
        }

        public void onServiceDisconnected(ComponentName className) {
            // This is called when the connection with the service has been
            // unexpectedly disconnected -- that is, its process crashed.
            mService = null;
            mKillButton.setEnabled(false);
            mCallbackText.setText("Disconnected.");

            // As part of the sample, tell the user what happened.
            Toast.makeText(Binding.this, R.string.remote_service_disconnected,
                    Toast.LENGTH_SHORT).show();
        }
    };

    /**
     * Class for interacting with the secondary interface of the service.
     */
    private ServiceConnection mSecondaryConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // Connecting to a secondary interface is the same as any
            // other interface.
            mSecondaryService = ISecondary.Stub.asInterface(service);
            mKillButton.setEnabled(true);
        }

        public void onServiceDisconnected(ComponentName className) {
            mSecondaryService = null;
            mKillButton.setEnabled(false);
        }
    };

    private OnClickListener mBindListener = new OnClickListener() {
        public void onClick(View v) {
            // Establish a couple connections with the service, binding
            // by interface names.  This allows other applications to be
            // installed that replace the remote service by implementing
            // the same interface.
            bindService(new Intent(IRemoteService.class.getName()),
                    mConnection, Context.BIND_AUTO_CREATE);
            bindService(new Intent(ISecondary.class.getName()),
                    mSecondaryConnection, Context.BIND_AUTO_CREATE);
            mIsBound = true;
            mCallbackText.setText("Binding.");
        }
    };

    private OnClickListener mUnbindListener = new OnClickListener() {
        public void onClick(View v) {
            if (mIsBound) {
                // If we have received the service, and hence registered with
                // it, then now is the time to unregister.
                if (mService != null) {
                    try {
                        mService.unregisterCallback(mCallback);
                    } catch (RemoteException e) {
                        // There is nothing special we need to do if the service
                        // has crashed.
                    }
                }

                // Detach our existing connection.
                unbindService(mConnection);
                unbindService(mSecondaryConnection);
                mKillButton.setEnabled(false);
                mIsBound = false;
                mCallbackText.setText("Unbinding.");
            }
        }
    };

    private OnClickListener mKillListener = new OnClickListener() {
        public void onClick(View v) {
            // To kill the process hosting our service, we need to know its
            // PID.  Conveniently our service has a call that will return
            // to us that information.
            if (mSecondaryService != null) {
                try {
                    int pid = mSecondaryService.getPid();
                    // Note that, though this API allows us to request to
                    // kill any process based on its PID, the kernel will
                    // still impose standard restrictions on which PIDs you
                    // are actually able to kill.  Typically this means only
                    // the process running your application and any additional
                    // processes created by that app as shown here; packages
                    // sharing a common UID will also be able to kill each
                    // other's processes.
                    Process.killProcess(pid);
                    mCallbackText.setText("Killed service process.");
                } catch (RemoteException ex) {
                    // Recover gracefully from the process hosting the
                    // server dying.
                    // Just for purposes of the sample, put up a notification.
                    Toast.makeText(Binding.this,
                            R.string.remote_call_failed,
                            Toast.LENGTH_SHORT).show();
                }
            }
        }
    };

    // ----------------------------------------------------------------------
    // Code showing how to deal with callbacks.
    // ----------------------------------------------------------------------

    /**
     * This implementation is used to receive callbacks from the remote
     * service.
     */
    private IRemoteServiceCallback mCallback = new IRemoteServiceCallback.Stub() {
        /**
         * This is called by the remote service regularly to tell us about
         * new values.  Note that IPC calls are dispatched through a thread
         * pool running in each process, so the code executing here will
         * NOT be running in our main thread like most other things -- so,
         * to update the UI, we need to use a Handler to hop over there.
         */
        public void valueChanged(int value) {
            mHandler.sendMessage(mHandler.obtainMessage(BUMP_MSG, value, 0));
        }
    };

    private static final int BUMP_MSG = 1;

    private Handler mHandler = new Handler() {
        @Override public void handleMessage(Message msg) {
            switch (msg.what) {
                case BUMP_MSG:
                    mCallbackText.setText("Received from service: " + msg.arg1);
                    break;
                default:
                    super.handleMessage(msg);
            }
        }

    };
}
```