---
layout: post
title: "Styles and Themes"
date: 2014-07-24 17:50:10 +0800
comments: true
categories: Android
---
原文：  
<http://developer.android.com/guide/topics/ui/themes.html>

A style is a collection of properties that specify the look and format for a View or window. A style can specify properties such as height, padding, font color, font size, background color, and much more. A style is defined in an XML resource that is separate from the XML that specifies the layout.

一个 style 是某些属性的集合，这些属性定义了 view 或 window 的外观和格式，比如 height、padding、font color、font size、background color 等。
<!--more-->

Styles in Android share a similar philosophy to cascading stylesheets in web design—they allow you to separate the design from the content.

与 web 设计中的 CSS 相似，Android style 的目的是设计与内容分离。

例如，使用 style 的话，你可以将以下代码：
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
A theme is a style applied to an entire Activity or application, rather than an individual View (as in the example above). When a style is applied as a theme, every View in the Activity or application will apply each style property that it supports. For example, you can apply the same CodeFont style as a theme for an Activity and then all text inside that Activity will have green monospace font.

theme 就是一个用于 Activity 和 application 的 style，而普通的 style 只用于单独的 view。当 style 被当做 theme 使用之后，在 Activity 或 application 中的所有 view 都会应用该 style 的属性。例如，你可以将一个字体 style 用作一个 Activity 的 theme，然后该 Activity 里的所有文本都会使用这个字体。

##Defining Styles
To create a set of styles, save an XML file in the res/values/ directory of your project. The name of the XML file is arbitrary, but it must use the .xml extension and be saved in the res/values/ folder.  
The root node of the XML file must be `<resources>`.

要创建一组 style，首先在 res/values/ 目录下创建一个 xml 文件，文件名可自定义，通常为 styles.xml。  
文件的根节点为 `<resoueces>`。

以下为一个 style 示例：
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
Each child of the `<resources>` element is converted into an application resource object at compile-time, which can be referenced by the value in the `<style>` element's name attribute. This example style can be referenced from an XML layout as @style/CodeFont (as demonstrated in the introduction above).

每个 `<resources>` 元素的子节点在编译时都被转换为一个应用程序资源对象，其可以通过 `<style>` 元素的 `name` 属性的值来引用，如 `style="@style/CodeFont"`。

The parent attribute in the `<style>` element is optional and specifies the resource ID of another style from which this style should inherit properties. You can then override the inherited style properties if you want to.

`<style>` 中的 parent 属性是可选的，用来指定另外一个 style 的资源ID，前者继承后者的所有属性。你也可以覆写继承来的 style 属性。

Remember, a style that you want to use as an Activity or application theme is defined in XML exactly the same as a style for a View. A style such as the one defined above can be applied as a style for a single View or as a theme for an entire Activity or application. How to apply a style for a single View or as an application theme is discussed later.

用在 Activity 或应用程序上的 theme 的定义方式和 style 的定义方式是相同的。一个如同上面那样例子中定义的 style 既可以用作一个 View的 style，也可以用作一个 Activity 或应用程序的 theme。

###Inheritance
The parent attribute in the `<style>` element lets you specify a style from which your style should inherit properties. You can use this to inherit properties from an existing style and then define only the properties that you want to change or add. You can inherit from styles that you've created yourself or from styles that are built into the platform. (See Using Platform Styles and Themes, below, for information about inheriting from styles defined by the Android platform.) For example, you can inherit the Android platform's default text appearance and then modify it:

parent 属性让你可以继承一个 style，然后定义你想改变或添加的属性。你可以从自定义的 style 或者平台的 style 中继承：
```
    <style name="GreenText" parent="@android:style/TextAppearance">
        <item name="android:textColor">#00FF00</item>
    </style>
```
If you want to inherit from styles that you've defined yourself, you do not have to use the parent attribute. Instead, just prefix the name of the style you want to inherit to the name of your new style, separated by a period. For example, to create a new style that inherits the CodeFont style defined above, but make the color red, you can author the new style like this:

如果你要继承自定义的 style，不必用到 parent，仅需要把父 style 名字作为新 style 名字的前缀即可，中间用点分隔：
```
    <style name="CodeFont.Red">
        <item name="android:textColor">#FF0000</item>
    </style>
```
Notice that there is no parent attribute in the `<style>` tag, but because the name attribute begins with the CodeFont style name (which is a style that you have created), this style inherits all style properties from that style. This style then overrides the android:textColor property to make the text red. You can reference this new style as @style/CodeFont.Red.

上面的 style 使用 `@style/CodeFont.Red` 来引用。

You can continue inheriting like this as many times as you'd like, by chaining names with periods. For example, you can extend CodeFont.Red to be bigger, with:

也可以多次继承：
```
    <style name="CodeFont.Red.Big">
        <item name="android:textSize">30sp</item>
    </style>
```
Note: This technique for inheritance by chaining together names only works for styles defined by your own resources. You can't inherit Android built-in styles this way. To reference a built-in style, such as TextAppearance, you must use the parent attribute.

要注意的是，前缀加点间隔的方法仅使用与自定义的 style，并不适用于平台内建的 style。

###Style Properties
Now that you understand how a style is defined, you need to learn what kind of style properties—defined by the <item> element—are available. You're probably familiar with some already, such as layout_width and textColor. Of course, there are many more style properties you can use.

接下来，你需要了解在 `<style>` 的 `<item>` 元素中可以定义哪些属性值。比如你可能已经知道的 layout_width 和 textColor 等等。

The best place to find properties that apply to a specific View is the corresponding class reference, which lists all of the supported XML attributes. For example, all of the attributes listed in the table of TextView XML attributes can be used in a style definition for a TextView element (or one of its subclasses). One of the attributes listed in the reference is android:inputType, so where you might normally place the android:inputType attribute in an <EditText> element, like this:

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
This simple example may look like more work, but when you add more style properties and factor-in the ability to re-use the style in various places, the pay-off can be huge.

这个例子看起来是增加了工作量，但当你的 style 里包含了更多的属性，并且在多个布局中都使用到这个 style 的时候，style 的作用就会凸显。

For a reference of all available style properties, see the R.attr reference. Keep in mind that all View objects don't accept all the same style attributes, so you should normally refer to the specific View class for supported style properties. However, if you apply a style to a View that does not support all of the style properties, the View will apply only those properties that are supported and simply ignore the others.

要知道的是，不是所有的 View 都接受相同的 style 属性，所以你应该先查看所支持的属性。不过，如果一个 style 中包含了 View 不支持的属性，这些属性会被忽略。

Some style properties, however, are not supported by any View element and can only be applied as a theme. These style properties apply to the entire window and not to any type of View. For example, style properties for a theme can hide the application title, hide the status bar, or change the window's background. These kind of style properties do not belong to any View object. To discover these theme-only style properties, look at the R.attr reference for attributes that begin with window. For instance, windowNoTitle and windowBackground are style properties that are effective only when the style is applied as a theme to an Activity or application. See the next section for information about applying a style as a theme.

有些属性不被任何 View 支持，只能用作 theme，比如隐藏应用标题栏，隐藏状态栏，改变窗口背景等。[R.attr](http://developer.android.com/reference/android/R.attr.html) 中以 `windows` 起头的属性就是这样的属性。

Note: Don't forget to prefix the property names in each `<item>` element with the android: namespace. For example: `<item name="android:inputType">`.

注意，每一个属性要使用 `android:` 作为命名空间。

##Applying Styles and Themes to the UI
应用 Styles 和 Themes

有两种方式设置一个 style：  
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

##Using Platform Styles and Themes
The Android platform provides a large collection of styles and themes that you can use in your applications. You can find a reference of all available styles in the R.style class. To use the styles listed here, replace all underscores in the style name with a period. For example, you can apply the Theme_NoTitleBar theme with "@android:style/Theme.NoTitleBar".

Android 平台提供了大量的 style 和 theme 供你使用。你可以在[R.style](http://developer.android.com/reference/android/R.style.html) 类中找到所有可用的 style，只需要用句号替换 style 名称中的下划线即可。例如，你可以通过 `@android:style/Theme.NoTitleBar` 使用 Theme_NoTitleBar theme。

The R.style reference, however, is not well documented and does not thoroughly describe the styles, so viewing the actual source code for these styles and themes will give you a better understanding of what style properties each one provides. For a better reference to the Android styles and themes, see the following source code:

由于 [R.style](http://developer.android.com/reference/android/R.style.html) 文档的不完善，你可以查看源码以更深入了解这些 style 的定义情况：  
[Android Styles (styles.xml)](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/styles.xml)  
[Android Themes (themes.xml)](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/themes.xml)  