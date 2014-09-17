---
layout: post
title: "Android Loaders"
date: 2014-09-02 12:09:14 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/components/loaders.html>

Loader 在 Android 3.0 版本被引入，它使得在 Activity 或 Fragment 中异步加载数据变得简单。Loader 有以下特性：  
1. 在所有 Activity 和 Fragment 中都可用；  
2. 异步加载数据；  
3. 它会监控数据的变动，并在变动时传送新的数据；
4. 当由于配置改变而被重新创建后，它们自动重连到上一个 Loader 的游标，所以不必重新查询数据。

关键类：  
LoaderManager  
Loader
<!--more-->

##1. Loader API Summary
使用 Loader 会涉及到几个类和接口，下面做一下介绍：  

（1）LoaderManager

抽象类，用在 Activity 或 Fragment 里管理 Loader 实例。通常与 CursorLoader 结合使用，也可以定义自己的 Loader。每个 Activity 或 Fragment 只能有一个 LoaderManager 实例，但一个 LoaderManager 可以持有多个 Loader。

（2）LoaderManager.LoaderCallbacks

一个用于客户端（即用户界面）与 LoaderManager 交互的回调接口。例如，你使用回调方法 onCreateLoader() 来创建一个新的 Loader。

（3）Loader

抽象类，用来执行异步数据加载操作。它是具体 Loader 的基类。一般情况下使用 CursorLoader，但是你也可以实现你自己的子类。一旦 Loader 被激活，它们将监视它们的数据源并且在数据改变时发送新的结果。

（4）AsyncTaskLoader

抽象类，类似 AsyncTask。

（5）CursorLoader
AsyncTaskLoader 的子类，它查询 ContentResolver 然后返回一个 Cursor。它实现了 Loader 协议，可以在后台查询 Cursor 而不阻塞 UI。

上面所列的类和接口是实现 Loader 的基础组件，你的每个 Loader 实现并不一定需要所有的组件，但至少要有一个 LoaderManager 和一个具体的 Loader（如 CursorLoader）。

##2. Using Loaders in an Application
This section describes how to use loaders in an Android application. An application that uses loaders typically includes the following:

使用 Loader 通常要包含以下内容：

（1）一个 Activity 或 Fragment；  
（2）一个 LoaderManager 实例；  
（3）一个 CursorLoader 实例，对应 ContentProvider 数据源；  
（4）实现 LoaderManager.LoaderCallbacks；  
（5）实现显示数据的方式，如 SimpleCursorAdapter；  
（6）数据源，如 ContentProvider。

###Starting a Loader
在 Activity 的 onCreate() 方法或 Fragment 的 onActivityCreated() 方法中初始化一个 Loader：
```
// Prepare the loader.  Either re-connect with an existing one,
// or start a new one.
getLoaderManager().initLoader(0, null, this);
```
initLoader() 方法有以下参数：

（1）代表这个 Loader 的唯一 ID。  
（2）Optional arguments to supply to the loader at construction (null in this example).  
（3）LoaderManager.LoaderCallbacks 的实现。

对 initLoader() 的调用可以确保一个 Loadder 实例被初始化并处于活动状态。它可能有两种结果：  

（1）如果该 ID 的 Loader 实例已经存在，则重用最后一个创建的 Loader。（不是对应 ID 的 Loader 哦）  
（2）如果不存在，就响应 LoaderManager.LoaderCallbacks 接口中的 onCreateLoader() 方法。

initLoader() 方法会返回被创建好的 Loader 的实例，但我们不需要获取它，因为 LoaderManager 会管理好它的生命周期，你基本上不用直接和 Loader 打交道，你的主要工作是处理好 LoaderManager.LoaderCallbacks 的接口。

###Restarting a Loader

###Using the LoaderManager Callbacks


##3. Example
```
public static class CursorLoaderListFragment extends ListFragment
        implements OnQueryTextListener, LoaderManager.LoaderCallbacks<Cursor> {

    // This is the Adapter being used to display the list's data.
    SimpleCursorAdapter mAdapter;

    // If non-null, this is the current filter the user has provided.
    String mCurFilter;

    @Override public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);

        // Give some text to display if there is no data.  In a real
        // application this would come from a resource.
        setEmptyText("No phone numbers");

        // We have a menu item to show in action bar.
        setHasOptionsMenu(true);

        // Create an empty adapter we will use to display the loaded data.
        mAdapter = new SimpleCursorAdapter(getActivity(),
                android.R.layout.simple_list_item_2, null,
                new String[] { Contacts.DISPLAY_NAME, Contacts.CONTACT_STATUS },
                new int[] { android.R.id.text1, android.R.id.text2 }, 0);
        setListAdapter(mAdapter);

        // Prepare the loader.  Either re-connect with an existing one,
        // or start a new one.
        getLoaderManager().initLoader(0, null, this);
    }

    @Override public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        // Place an action bar item for searching.
        MenuItem item = menu.add("Search");
        item.setIcon(android.R.drawable.ic_menu_search);
        item.setShowAsAction(MenuItem.SHOW_AS_ACTION_IF_ROOM);
        SearchView sv = new SearchView(getActivity());
        sv.setOnQueryTextListener(this);
        item.setActionView(sv);
    }

    public boolean onQueryTextChange(String newText) {
        // Called when the action bar search text has changed.  Update
        // the search filter, and restart the loader to do a new query
        // with this filter.
        mCurFilter = !TextUtils.isEmpty(newText) ? newText : null;
        getLoaderManager().restartLoader(0, null, this);
        return true;
    }

    @Override public boolean onQueryTextSubmit(String query) {
        // Don't care about this.
        return true;
    }

    @Override public void onListItemClick(ListView l, View v, int position, long id) {
        // Insert desired behavior here.
        Log.i("FragmentComplexList", "Item clicked: " + id);
    }

    // These are the Contacts rows that we will retrieve.
    static final String[] CONTACTS_SUMMARY_PROJECTION = new String[] {
        Contacts._ID,
        Contacts.DISPLAY_NAME,
        Contacts.CONTACT_STATUS,
        Contacts.CONTACT_PRESENCE,
        Contacts.PHOTO_ID,
        Contacts.LOOKUP_KEY,
    };
    public Loader<Cursor> onCreateLoader(int id, Bundle args) {
        // This is called when a new Loader needs to be created.  This
        // sample only has one Loader, so we don't care about the ID.
        // First, pick the base URI to use depending on whether we are
        // currently filtering.
        Uri baseUri;
        if (mCurFilter != null) {
            baseUri = Uri.withAppendedPath(Contacts.CONTENT_FILTER_URI,
                    Uri.encode(mCurFilter));
        } else {
            baseUri = Contacts.CONTENT_URI;
        }

        // Now create and return a CursorLoader that will take care of
        // creating a Cursor for the data being displayed.
        String select = "((" + Contacts.DISPLAY_NAME + " NOTNULL) AND ("
                + Contacts.HAS_PHONE_NUMBER + "=1) AND ("
                + Contacts.DISPLAY_NAME + " != '' ))";
        return new CursorLoader(getActivity(), baseUri,
                CONTACTS_SUMMARY_PROJECTION, select, null,
                Contacts.DISPLAY_NAME + " COLLATE LOCALIZED ASC");
    }

    public void onLoadFinished(Loader<Cursor> loader, Cursor data) {
        // Swap the new cursor in.  (The framework will take care of closing the
        // old cursor once we return.)
        mAdapter.swapCursor(data);
    }

    public void onLoaderReset(Loader<Cursor> loader) {
        // This is called when the last Cursor provided to onLoadFinished()
        // above is about to be closed.  We need to make sure we are no
        // longer using it.
        mAdapter.swapCursor(null);
    }
}
```