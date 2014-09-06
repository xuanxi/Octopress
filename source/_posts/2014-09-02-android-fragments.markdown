---
layout: post
title: "Android Fragments"
date: 2014-09-02 12:07:46 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/components/fragments.html>

要点：  
1. Design Philosophy（设计哲学）  
2. Creating a Fragment（创建 Fragment）   
3. Managing Fragments（管理 Fragment）  
4. Performing Fragment Transactions（执行 Fragment 事务）  
5. Communicating with the Activity（与 Activity 通信）   
6. Handling the Fragment Lifecycle（维护 Fragment 生命周期）  
7. Example（例子）

关键类:  
Fragment  
FragmentManager  
FragmentTransaction
<!--more-->

Fragment 是 Activity 界面的一部分，你可以在一个 Activity 中组合几个 Fragment 来构建 multi-pane 界面，并可以在其它 Activity 重用一个 Fragment。Fragment 有自己的生命周期，可以接收输入事件，可以在 Activity 运行期间被加入或移除（类似“子 Activity”）。

Fragment 必须内嵌到 Activity 里，它的生命周期受 Activity 生命周期的影响。例如 Activity paused，其中所有 Fragment 也 paused；Activity destroyed，其中所有 Fragment 也 destroyed。不过，当 Activity 在运行期间，你可以独立地操作 Fragment，比如加入或移除，而受 Activity 生命周期的影响。

你可以通过布局或代码加入一个 Fragment，也可以使用不带 UI 的 Fragment。

##1. Design Philosophy
Android 在 3.0 版本引入 Fragment，主要为了支持在大屏幕（如平板）中创建动态和自适应的界面。通过将一个 Activity 布局划分为几个碎片（fragments），你就可以在应用运行期间动态改变界面，以及在一个 Activity 维护的返回栈（back stack）中保存这些变动。

下图展示了 Fragment 分别在平板和手机上的使用：
![fragments](/images/fragments.png)

你应该将 Fragment 设计成一个标准化的可重用的组件，避免不同 Fragment 之间相互调用。当应用要同时支持手机和平板时，你可通过布局配置来重用 Fragment。

##2. Creating a Fragment
要创建 Fragment，你必须创建一个 Fragment 类的子类（或者继承现成的 Fragment 子类）。Fragment 的代码看起来与 Activity 非常像，比如都包含以下生命周期方法：onCreate(), onStart(), onPause(), and onStop()。事实上，如果你将一个使用 Activity 的应用修改为使用 Fragment，你可能只需要将 Activity 生命周期方法中的代码复制到对应的 Fragment 生命周期方法中就行。

![fragment_lifecycle](/images/fragment_lifecycle.png)

一般地情况下，你至少需要实现以下生命周期方法：

（1）onCreate()  
系统在创建 Fragment 的时候调用该方法。在这里初始化一些无论 paused 还是 stopped 状态都仍然要保留的数据。

（2）onCreateView()  
系统在第一次绘制 Fragment 界面的时候调用该方法。为了绘制 Fragment 的 UI，此方法必须返回这个 Fragment 布局的根 View。如果不提供界面的话，也可以返回 null。

（3）onPause()  
用户将要离开这个 Fragment 时，系统会首先调用该方法（然而它不总是意味着 Fragment 将被销毁）。通常你应该在这里保存用户的修改，因为用户有可能不会再回来。

大多数应用应当为每一个 Fragment 实现至少上述3个方法，你也可以使用其它的回调方法处理对应阶段的事情。全部的生命周期回调方法将会在后面的 Handlingthe Fragment Lifecycle 章节中讨论。

另外，Android 也提供了几个特定用途的 Fragment 供你继承使用：

DialogFragment  
显示一个浮动对话框。相比普通的 AlertDialog，官方更推荐使用 DialogFragment，because you can incorporate a fragment dialog into the back stack of fragments managed by the activity, allowing the user to return to a dismissed fragment.

ListFragment  
显示一个列表，与 ListActivity 类似，提供了相关的处理 ListView 的方法，如 onListItemClick()。

PreferenceFragment  
显示列表形式的偏好设置，与 PreferenceActivity 类似，在创建“设置”界面的时候使用。

###Adding a user interface
Fragment 通常被认为是 Activity 界面的一部分，它将自身布局加入到 Activity 中。要为 Fragment 提供布局，你必须实现 onCreateView() 回调方法，并返回布局的根。

注意：ListFragment 已经有默认的 onCreateView() 实现，并提供了 ListView，如下源码：
```
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container,
    Bundle savedInstanceState) {
      return inflater.inflate(com.android.internal.R.layout.list_content,
                container, false);
}
```

onCreateView() 方法提供了 LayoutInflater 对象来加载布局。一个 Fragment 示例如下：
```
public static class ExampleFragment extends Fragment {
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.example_fragment, container, false);
    }
}
```
另外，onCreateView() 中的 container 参数是 Activity 布局中用来插入 Fragment 布局的 ViewGroup。savedInstanceState 参数是上一次保存的状态（如果作了保存的话）。

inflate (int resource, ViewGroup root, boolean attachToRoot) 方法的参数说明如下：

resource：布局的 id。

root：布局的参考父 View，如果 attachToRoot 为 true，就将它作为布局的新 root 一并返回；如果 attachToRoot 为 false，就将它的 LayoutParams 参数应用到布局本来的 root。这里必须将 onCreateView() 提供的 container 传进来，以获得 container 的 LayoutParams 参数，并应用在 Fragment 布局的 root 上，以便 Fragment 布局可以正确地插入到 Activity 布局中。

attachToRoot：介绍如 root 参数所述。在 onCreateView() 中的情况是 false，因为系统已经实现将布局插入到 container 中，没必要把 container 重复加入到 Fragment 布局。


现在，你已经知道怎样定义一个 Fragment，下一节将介绍怎样将这个 Fragment 加入 Activity 中。

###Adding a fragment to an activity
有两种方式将 Fragment 加入到 Activity 中：

（1）通过 `<fragment>` 标签

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment android:name="com.example.news.ArticleListFragment"
            android:id="@+id/list"
            android:layout_weight="1"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
    <fragment android:name="com.example.news.ArticleReaderFragment"
            android:id="@+id/viewer"
            android:layout_weight="2"
            android:layout_width="0dp"
            android:layout_height="match_parent" />
</LinearLayout>
```
`android:name` 属性指定了 Fragment 类。系统在创建这个 Activity 时，会依次调用 Fragment 的 onCreateView() 方法以获得 Fragment 的实际布局，然后将布局直接替换对应的 `<fragment>` 元素。

注意：需要为每个 Fragment 指定一个唯一标识，以便系统使用这个标识来做还原操作（在 Activity restarted 的时候），同时你也可能用这个标识来执行事务，比如删除 Fragment。有两种方式提供唯一标识：  
android:id  
android:tag

（2）通过代码

在 Activity 运行过程中，你随时可以加入一个 Fragment，仅需要指定一个父 ViewGroup。

要执行 Fragment 事务（比如加入、移除、替换），你必须使用 FragmentTransaction 中的接口：
```
FragmentManager fragmentManager = getFragmentManager()
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
```
然后你可以用 add() 方法加入一个 Fragment：
```
ExampleFragment fragment = new ExampleFragment();
fragmentTransaction.add(R.id.fragment_container, fragment);
fragmentTransaction.commit();
```

add() 的第一个参数是父 ViewGroup，第二个参数是要加入的 Fragment。

一旦对 FragmentTransaction 作了改动，你必须调用 commit() 来提交改动。

###Adding a fragment without a UI
上面的例子介绍了如何加入带界面的 Fragment，不过，你也可以使用不带界面的 Fragment 来处理后台任务。

要加入不带界面的 Fragment，使用 add(Fragment, String) 方法，第二个参数使用 “tag” 而不是 “id”。因为不带界面，所以不用重写 onCreateView()。

##3. Managing Fragments
管理 Fragment 使用 FragmentManager，在 Activity 中通过 getFragmentManager() 获得 FragmentManager 对象。

在 FragmentManager 中可以做的事情是：

（1）获得 Fragment 对象。findFragmentById() 方法获得带界面的；findFragmentByTag() 方法获得不带界面的。

（2）从返回栈里弹出 Fragment。使用 popBackStack() 方法，模拟返回命令。

（3）为返回栈变动注册监听器。使用 addOnBackStackChangedListener() 方法。

For more information about these methods and others, refer to the FragmentManager class documentation.

更多信息请看 [FragmentManager](http://developer.android.com/reference/android/app/FragmentManager.html)。

##4. Performing Fragment Transactions
A great feature about using fragments in your activity is the ability to add, remove, replace, and perform other actions with them, in response to user interaction. Each set of changes that you commit to the activity is called a transaction and you can perform one using APIs in FragmentTransaction. You can also save each transaction to a back stack managed by the activity, allowing the user to navigate backward through the fragment changes (similar to navigating backward through activities).

Fragment 的一个重要功能是可以灵活地响应用户交互，比如加入、移除、替换或其它动作。一组动作称之为一个事务，你可以使用 FragmentTransaction 的接口来运行一个事务。你也可以将事务保存到由 Activity 管理的返回栈中，让用户。

##5. Communicating with the Activity  
##6. Handling the Fragment Lifecycle
管理 Fragment 的生命周期，在大多地方与管理 Activity 的生命周期相似。一个 Fragment 的生命状态有以下三种：

（1）Resumed  
Fragment 可见。

（2）Paused  
另一个 Activity 处于前台并拥有焦点，但 Fragment 所在的 Activity 仍然可见（比如前台 Activity 有部分透明）。

（3）Stopped  
Fragment 不可见，要么是宿主 Activity 处于 stopped 状态，要么是这个 Fragment 被移除（如用户滑动到下一个 Fragment）并处于后台堆栈中。stopped 状态的 Fragment 仍然生存，所有的状态和成员信息都被系统保留着。如果宿主 Activity 被销毁，Fragment 也同时被销毁。

下图为 Activity 生命周期与 Fragment 生命周期的对应关系：
![activity_fragment_lifecycle](/images/activity_fragment_lifecycle.png)

跟 Activity 一样，你可以使用 Boundle 来保存 Fragment 的状态，万一 Activity 所在进程被杀掉了，你就可以在重建 Activity 的时候还原 Fragment 的状态。你可以在 onSaveInstanceState() 里保存状态，并在 onCreate() 、onCreateView() 或 onActivityCreated() 中恢复状态。

The most significant difference in lifecycle between an activity and a fragment is how one is stored in its respective back stack. An activity is placed into a back stack of activities that's managed by the system when it's stopped, by default (so that the user can navigate back to it with the Back button, as discussed in Tasks and Back Stack). However, a fragment is placed into a back stack managed by the host activity only when you explicitly request that the instance be saved by calling addToBackStack() during a transaction that removes the fragment.

Otherwise, managing the fragment lifecycle is very similar to managing the activity lifecycle. So, the same practices for managing the activity lifecycle also apply to fragments. What you also need to understand, though, is how the life of the activity affects the life of the fragment.

Caution: If you need a Context object within your Fragment, you can call getActivity(). However, be careful to call getActivity() only when the fragment is attached to an activity. When the fragment is not yet attached, or was detached during the end of its lifecycle, getActivity() will return null.


###Coordinating with the activity lifecycle

onCreateView()  
创建 Fragment UI 时被调用。

onActivityCreated()  
在 Activity 的 onCreate() 方法返回之后被调用。


##7. Example