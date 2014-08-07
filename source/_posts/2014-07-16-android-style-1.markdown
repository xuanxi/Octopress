---
layout: post
title: "Android Style (1)"
date: 2014-07-16 22:30:21 +0800
comments: true
categories: Android
---
> Build visually compelling apps that look great on any device.  
构建在任何设备上都引人注目的应用。

原文：  
<http://developer.android.com/design/style/index.html>

本篇包括以下内容：  
1. Devices and Displays    
2. Themes    
3. Touch Feedback  
4. Metrics and Grids  
5. Typography  
<!--more-->

##1. Devices and Displays
设备与显示

![devices_displays_main](/images/devices_displays_main.png)

Android 作为一个开放的平台，支持种类繁多的设备和屏幕尺寸，因此，解决设备适配问题一直以来是应用开发中的重点工作。Android 为此提供了以下三种基本方式来实现屏幕适配。
####Be flexible 自适应  
Android 会根据不同屏幕的宽高值缩放布局。宽高值可以定义为 WRAP_CONTENT、MATCH_PARENT、FILL_PARENT（API-8以下）、dp值、px值等。
####Optimize layouts 布局优化
针对大屏幕设备，通过设计混合 view 来展示更多的内容（如平板的一屏使用两个 fragment 来实现左边显示列表右边显示内容），以此充分利用屏幕空间。
####Assets for all 资源集
为了让应用界面在所有屏幕上以最佳质量显示，需要根据不同密度屏幕提供不同尺寸的图片资源。目前主流手机的屏幕密度在 XHDPI左右的范围（即通常说的视网膜）。下图是屏幕密度分布：

![devices_displays_density@2x](/images/devices_displays_density@2x.png)

####Strategies 适配策略
有两种方式开始屏幕适配工作：  
1. 从 normal size 和 MDPI 开始；  
2. 从应用要支持的最大屏幕以及对应的 DPI 开始。  
通常采用方法2，或者挑选一个主流或偏高的屏幕开始，比如 1280x768 及对应的 XHDPI，这样才能够保证多数用户的体验。MDPI 和 HDPI 设备已逐渐退出市场。

Developer Guide  
更多关于屏幕适配的内容, read [Designing for Multiple Screens](http://blog.xuanxi.me/blog/2014/07/17/designing-for-multiple-screens/) and [Building a Dynamic UI with Fragments](http://developer.android.com/training/basics/fragments/index.html).

##2. Themes
主题

theme 是让应用界面风格保持一致的机制，theme 其实就是一个 style，而 style 是诸如 color、height、padding、front size 等属性值的集合。在 4.0 系统之后，Android 提供了两种 theme（Hole Light 和 Holo Dark），使用官方的 theme 可以让你的应用更好的符合 Android 设计规范。这两种 theme 如下图所示：

Gmail in Holo Light：  
![themes_holo_light](/images/themes_holo_light.png)

Settings in Holo Dark：  
![themes_holo_dark](/images/themes_holo_dark.png)

如果你希望设计一个与众不同的应用，一个好办法是在 Holo Light 或 Holo Dark 的基础上进行定制。

更多关于使用 theme 的信息请看 [Styles and Themes](http://blog.xuanxi.me/blog/2014/07/24/styles-and-themes/)

##3. Touch Feedback
触摸反馈

使用明暗效果来响应用户的触摸（如下文的 States），对手势做出结果导向的界面行为（如下文的 Communication）。

###States
大部分的 Android 界面元素都内置了触摸反馈效果，比如 Button。

![touch_feedback_states](/images/touch_feedback_states.png)

###Communication
当你的界面能够响应更复杂的手势时，要让用户知道接下来会发生什么。  
比如，在 Recents 界面，当用户向左或向右拖动一个应用缩略图时，缩略图会渐渐变暗，以此来告诉用户拖动操作会删除该应用。

![touch_feedback_manipulation](/images/touch_feedback_manipulation.png)

###Boundaries
当用户在一个可滚动的区域尝试越过起始或结尾位置时，应该给予一个边界提示。Android 提供的 ListView 和 GridView 都带有这种效果。

![touch_feedback_communication](/images/touch_feedback_communication.png)

##4. Metrics and Grids
设备之间除了屏幕尺寸不同，屏幕的像素密度 (DPI) 也不一样。为了简化为不同的屏幕设计 UI 的复杂度，可以将所有的设备按照大小和像素密度如下分类。  
1. 按设备大小分有两类，分别是手持设备 (小于 600 dp) 和 平板 (大于等于 600dp)；  
2. 按像素密度分有 LDPI、MDPI、HDPI、XHDPI、XXHDPI 和 XXXHDPI。  

![metrics_diagram](/images/metrics_diagram.png)

dp: `density-independent pixels` 密度无关像素单位，意思是相同 dp 值在不同密度屏幕上的显示效果一样。Android 提供 dp 的目的是为了自适应屏幕。

更多关于市面上设备分布的信息请看 
[Screen Sizes and Densities Device Dashboard](http://developer.android.com/about/dashboards/index.html)

###48dp Rhythm
可触摸元素的尺寸通常设置在 48dp 左右的范围。

![metrics_48](/images/metrics_48.png)

####Why 48dp?
就平均而言，48dp 转化为物理上的长度是 9mm，这是手指可操作的比较适合的范围（7-10mm）。

![metrics_closeup](/images/metrics_closeup.png)

####Mind the gaps
元素间的间隔通常为 8dp。

###Examples
![metrics_forms](/images/metrics_forms.png)

##5. Typography
字体

![typography_main](/images/typography_main.png)

Android 的英文字体设计基于传统的印刷排版技术，例如字体缩放、字间距规则和对齐网格。这些技术的成功运用，让用
户可以快速理解屏幕上的信息。为此，Ice Cream Sandwich 系统引⼊了全新的 Roboto 字体，它专为高分辨率屏幕而设计。

TextView 控件默认支持 thin、light、regular、bold，以及对应权重的斜体。也提供了叫 Condensed 的变种版本。

![typography_variants@2x](/images/typography_variants@2x.png)

####Default type colors
Android 使用 textColorPrimary 和 textColorSecondary 作为默认颜色值。在 light 主题中使用 textColorPrimaryInverse 和 textColorSecondaryInverse。
```
<style name="Theme.Light">
	<item name="textColorPrimary">@android:color/primary_text_light</item>
	<item name="textColorSecondary">@android:color/secondary_text_light</item>
	<item name="textColorPrimaryInverse">@android:color/primary_text_dark</item>
	<item name="textColorSecondaryInverse">@android:color/secondary_text_dark</item>
</style>
```
![typography_defaults](/images/typography_defaults.png)

####Typographic Scale
字体⼤⼩上的不同有利于创造出有序和容易理解的布局。不过，在同⼀个界面中使用过多的字体⼤⼩则会造成混乱。Android 设计框架建议使用以下⼏种字体⼤⼩：

![typography_sizes](/images/typography_sizes.png)

sp: `scale-independent pixels` 缩放无关像素单位。

用户可以在`设置`中调整整个系统的字体⼤⼩。为了支持这⼀特性，字体的⼤⼩应尽量使用sp。另外，要针对支持 sp 缩放的布局做测试，即改变系统字体大小，看布局是否显示正常。
