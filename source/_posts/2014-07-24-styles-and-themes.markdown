---
layout: post
title: "Styles and Themes"
date: 2014-07-24 17:50:10 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/topics/ui/themes.html>

一个 style 是某些属性的集合，这些属性定义了 view 或 window 的外观和格式，比如 height、padding、font color、font size、background color 等。
<!--more-->

与 web 设计中的 CSS 相似，Android style 的目的是设计与内容分离。例如，使用 style 的话，你可以将以下代码：
```
<TextView
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:textColor="#00FF00"
    android:typeface="monospace"
    android:text="@string/hello" />
```
替换为：
```
<TextView
    style="@style/CodeFont"
    android:text="@string/hello" />    
```
theme 就是一个用于 Activity 和 Application 的 style，而普通的 style 只用于单独的 view。当 style 被当做 theme 使用之后，在 Activity 或 Application 中的所有 view 都会应用该 style 的属性。例如，你可以将一个字体 style 用作一个 Activity 的 theme，然后该 Activity 里的所有文本都会使用这个字体。

##1. 定义 Styles
要创建一组 style，首先在 res/values/ 目录下创建一个 xml 文件，文件名可自定义，通常为 styles.xml。文件的根节点为 `<resoueces>`：
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="CodeFont" parent="@android:style/TextAppearance.Medium">
        <item name="android:layout_width">fill_parent</item>
        <item name="android:layout_height">wrap_content</item>
        <item name="android:textColor">#00FF00</item>
        <item name="android:typeface">monospace</item>
    </style>
</resources>
```
每个 `<resources>` 元素的子节点在编译时都被转换为一个应用程序资源对象，其可以通过 `<style>` 元素的 `name` 属性的值来引用，如 `style="@style/CodeFont"`。

`<style>` 中的 parent 属性是可选的，用来指定另外一个 style 的资源ID，前者继承后者的所有属性。你也可以覆写继承来的 style 属性。

用在 Activity 或应用程序上的 theme 的定义方式和 style 的定义方式是相同的。一个如同上面那样例子中定义的 style 既可以用作一个 View的 style，也可以用作一个 Activity 或应用程序的 theme。

###继承
parent 属性让你可以继承一个 style，然后定义你想改变或添加的属性。你可以从自定义的 style 或者平台的 style 中继承：
```
    <style name="GreenText" parent="@android:style/TextAppearance">
        <item name="android:textColor">#00FF00</item>
    </style>
```
如果你要继承自定义的 style，不必用到 parent，仅需要把父 style 名字作为新 style 名字的前缀即可，中间用点分隔：
```
    <style name="CodeFont.Red">
        <item name="android:textColor">#FF0000</item>
    </style>
```
上面的 style 使用 `@style/CodeFont.Red` 来引用。也可以多次继承：
```
    <style name="CodeFont.Red.Big">
        <item name="android:textSize">30sp</item>
    </style>
```

要注意的是，前缀加点间隔的方法仅使用与自定义的 style，并不适用于平台内建的 style。

###Style 属性
接下来，你需要了解在 `<style>` 的 `<item>` 元素中可以定义哪些属性值。比如你可能已经知道的 layout_width 和 textColor 等等。

要知道某个 view 的所有属性，最好的地方就是它的官方参考文档（比如 TextView 的 <http://developer.android.com/reference/android/widget/TextView.html），在 XML Attributes 的地方就列出了所有支持的属性，这些属性都可以用在 style 里。

下面以 EditText 的 android:inputType 属性为例子，一般情况你可以在布局中直接使用：
```
<EditText
    android:inputType="number"
    ... />
```
你也可以定义一个 style：
```
<style name="Numbers">
  <item name="android:inputType">number</item>
  ...
</style>
```
然后你的布局变成这样：
```
<EditText
    style="@style/Numbers"
    ... />
```
这个例子看起来是增加了工作量，但当你的 style 里包含了更多的属性，并且在多个布局中都使用到这个 style 的时候，style 的作用就会凸显。

要知道的是，不是所有的 View 都接受相同的 style 属性，所以你应该先查看所支持的属性。不过，如果一个 style 中包含了 View 不支持的属性，这些属性会被忽略。

有些属性不被任何 View 支持，只能用作 theme，比如隐藏应用标题栏，隐藏状态栏，改变窗口背景等。[R.attr](http://developer.android.com/reference/android/R.attr.html) 中以 `windows` 起头的属性就是这样的属性。

注意，每一个属性要使用 `android:` 作为命名空间，比如: `<item name="android:inputType">`。

##2. 使用自定义的 Styles 和 Themes
有两种方式使用一个 style：  
对一个独立的 View，添加 style 属性到你的布局的 View 元素中：
```
<TextView
    style="@style/CodeFont"
    android:text="@string/hello" />
```
对一个 Activity 或应用，添加 `android:theme` 属性到 Android manifest 的 `<activity>` 或 `<application>` 元素中：
```
<application android:theme="@style/CustomTheme">
<activity android:theme="@style/CustomTheme">
```

##3. 使用系统自带的 Styles and Themes
Android 平台提供了大量的 style 和 theme 供你使用。你可以在[R.style](http://developer.android.com/reference/android/R.style.html) 类中找到所有可用的 style，只需要用句号替换 style 名称中的下划线即可。例如，你可以通过 `@android:style/Theme.NoTitleBar` 使用 Theme_NoTitleBar theme。

由于 [R.style](http://developer.android.com/reference/android/R.style.html) 文档的不完善，你可以查看源码以更深入了解这些 style 的定义情况：  
[Android Styles (styles.xml)](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/styles.xml)  
[Android Themes (themes.xml)](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/themes.xml)  