---
layout: post
title: "Android Style"
date: 2014-07-16 22:30:21 +0800
comments: true
categories: Android
---
> Build visually compelling apps that look great on any device.  
构建在任何设备上都引人注目的应用。

本篇分为以下几个部分：  
1. Devices and Displays（重要）  
2. Themes    
3. Touch Feedback  
4. Metrics and Grids（重要）  
5. Typography  
<!--more-->

##1. Devices and Displays
设备与显示

Android powers hundreds of millions of phones, tablets, and other devices in a wide variety of screen sizes and form factors. By taking advantage of Android's flexible layout system, you can create apps that gracefully scale from large tablets to smaller phones.

Android 作为一个开放的平台，支持种类繁多的设备和屏幕尺寸，因此，解决设备适配问题一直以来是应用开发中的重点工作。Android 为此提供了自适应布局的解决方案。

![devices_displays_main](/images/devices_displays_main.png)

####Be flexible 自适应  
Stretch and compress your layouts to accommodate various heights and widths.
 
Android 会根据不同的宽高值缩放布局。宽高值可以定义为 WRAP_CONTENT、MATCH_PARENT、FILL_PARENT（API-8以下）、dp值、px值等。
####Optimize layouts 布局优化
On larger devices, take advantage of extra screen real estate. Create compound views that combine multiple views to reveal more content and ease navigation.

针对大屏幕设备，通过设计混合 view 来展示更多的内容（如平板中常利用 fragment 来实现左边显示列表右边显示内容），以此充分利用屏幕空间。
####Assets for all 资源集
Provide resources for different screen densities (DPI) to ensure that your app looks great on any device.

为了让应用在所有屏幕上以最佳质量显示，需要根据不同密度屏幕提供不同尺规格的资源。目前主流手机的屏幕密度在 XHDPI（视网膜）左右的范围。

![devices_displays_density@2x](/images/devices_displays_density@2x.png)

####Strategies 适配策略
So where do you begin when designing for multiple screens? One approach is to work in the base standard (normal size and MDPI) and scale it up or down for the other buckets. Another approach is to start with the device with the largest screen size, and then scale down and figure out the UI compromises you'll need to make on smaller screens.

有两种方式开始屏幕适配工作：  
1. 从 normal size 和 MDPI 开始；  
2. 从应用要支持的最大屏幕以及对应的 DPI 开始。  
我认为挑选一个主流或偏高的屏幕开始较好，比如 xhdpi，能够保证多数用户的体验。

For details about designing layouts for larger screens, see the [Multi-pane Layouts](http://developer.android.com/design/patterns/multi-pane-layouts.html) guide.（Multi-pane Layouts 主要针对平板）

Developer Guide  
For information about how to build flexible layouts for multiple screen sizes and densities, read [Designing for Multiple Screens](http://blog.xuanxi.me/blog/2014/07/17/designing-for-multiple-screens/) and [Building a Dynamic UI with Fragments](http://developer.android.com/training/basics/fragments/index.html).

##2. Themes
主题

Themes are Android's mechanism for applying a consistent style to an app or activity. The style specifies the visual properties of the elements that make up your user interface, such as color, height, padding and font size. To promote greater cohesion between all apps on the platform, Android provides two system themes that you can choose from when building apps:

Theme 是让应用界面风格保持一致的机制，Theme 其实就是一个 style，而 style 是诸如 color、height、padding、front size 等属性值的集合。Android 官方提供了两种 theme，目的是让 Android 平台上的应用体验一致，你在创建应用的时候可选择其中之一，当然也可以定义自己的 theme。

Gmail in Holo Light：  
![themes_holo_light](/images/themes_holo_light.png)

Settings in Holo Dark：  
![themes_holo_dark](/images/themes_holo_dark.png)

Applying these themes will go a long way in helping you to build apps that fit right into the general visual language of Android.

使用以上两种 theme 可以让你的应用更好的符合 Android 设计规范。

Pick the system theme that best matches the needs and design aesthetics for your app. If your desire is to have a more distinct look for your app, using one of the system themes as a starting point for your customizations is a good idea. The system themes provide a solid foundation on top of which you can selectively implement your own visual stylings.

如果你希望设计一个与众不同的应用，一个好办法是在 Holo Light 或 Holo Dark 的基础上进行定制。

更多关于使用 theme 的信息请看 [Styles and Themes](http://blog.xuanxi.me/blog/2014/07/24/styles-and-themes/)

##3. Touch Feedback
触摸反馈

Use illumination and dimming to respond to touches, reinforce the resulting behaviors of gestures, and indicate what actions are enabled and disabled.

使用明暗效果来响应用户的触摸（如下文的 States），对手势做出结果导向的界面行为（如下文的 Communication），明确指出哪些操作可用哪些操作不可用。

Be responsive to touches in a gentle way. Whenever a user touches an actionable area in your app, let them know the app is "listening" by providing a visual response. Make it subtle —just slightly lighter or darker than the untouched color. This provides two benefits:  
Sprinkles of encouragement are more pleasant than jolts.  
Incorporating your branding is much easier because the default touch feedback works with whatever hue you choose.

用一种自然的方式来响应触摸动作。在用户触摸一个可操作区域的时候，通过界面的变化来让他们知道应用正在处理这个操作。

###States
![touch_feedback_states](/images/touch_feedback_states.png)

Most of Android's UI elements have touch feedback built in, including states that indicate whether touching the element will have any effect.

大部分的 Android 界面元素都内置了触摸反馈效果，比如上图中的几种状态。

###Communication
When your objects react to more complex gestures, help users understand what the outcome will be.  
In Recents, when a user starts swiping a thumbnail left or right, it begins to dim. This helps the user understand that swiping will cause the item to be removed.

当你的界面能够响应更复杂的手势时，要让用户知道接下来会发生什么。  
比如，在 Recents 界面，当用户向左或向右拖动一个应用缩略图时，缩略图会渐渐变暗，以此来告诉用户拖动操作会删除该应用。

![touch_feedback_manipulation](/images/touch_feedback_manipulation.png)

###Boundaries
When users try to scroll past the beginning or end of a scrollable area, communicate the boundary with a visual cue. Many of Android's scrollable UI widgets, like lists and grid lists, have support for boundary feedback built in. If you’re building custom widgets, keep boundary feedback in mind and provide it from within your app.

当用户在一个可滚动的区域尝试越过起始或结尾位置时，应该给予一个边界提示。Android 提供的 ListView 和 GridView 都带有这种效果。

![touch_feedback_communication](/images/touch_feedback_communication.png)

##4. Metrics and Grids
Devices vary not only in physical size, but also in screen density (DPI). To simplify the way you design for multiple screens, think of each device as falling into a particular size bucket and density bucket:  
The size buckets are handset (smaller than 600dp) and tablet (larger than or equal 600dp).  
The density buckets are LDPI, MDPI, HDPI, XHDPI, XXHDPI, and XXXHDPI.

设备之间除了屏幕尺寸不同，屏幕的像素密度 (DPI) 也不一样。为了简化为不同的屏幕设计 UI 的复杂度，可以将所有的设备按照大小和像素密度如下分类。  
1. 按设备大小分有两类，分别是手持设备 (小于 600 dp) 和 平板 (大于等于 600dp)；  
2. 按像素密度分有 LDPI、MDPI、HDPI、XHDPI、XXHDPI 和 XXXHDPI。  

Optimize your application's UI by designing alternative layouts for some of the different size buckets, and provide alternative bitmap images for different density buckets.

你应该为不同的设备使用不一样的布局文件来优化界面，以及为各种像素密度提供大小不同的位图。

![metrics_diagram](/images/metrics_diagram.png)

Devices vary in the amount of `density-independent pixels` (dp) they can display.

如上图所示，设备的种类可以体现在 dp 长度上。更多关于市面上设备分布的信息请看 
[Screen Sizes and Densities Device Dashboard](http://developer.android.com/about/dashboards/index.html)
###48dp Rhythm
Touchable UI components are generally laid out along 48dp units.

可触摸界面元素通常设置 48dp 左右的大小。

![metrics_48](/images/metrics_48.png)

####Why 48dp?
On average, 48dp translate to a physical size of about 9mm (with some variability). This is comfortably in the range of recommended target sizes (7-10 mm) for touchscreen objects and users will be able to reliably and accurately target them with their fingers.

就平均而言，48dp 转化为物理长度是 9mm。这是用户手指可操作的比较适合的范围（7-10mm）。

![metrics_closeup](/images/metrics_closeup.png)

####Mind the gaps
Spacing between each UI element is 8dp.

留意元素间的间隔，通常为 8dp。

###Examples
![metrics_forms](/images/metrics_forms.png)

##5. Typography
字体

![typography_main](/images/typography_main.png)

The Android design language relies on traditional typographic tools such as scale, space, rhythm, and alignment with an underlying grid. Successful deployment of these tools is essential to help users quickly understand a screen of information. To support such use of typography, Ice Cream Sandwich introduced a new type family named Roboto, created specifically for the requirements of UI and high-resolution screens.

Android 的英文字体设计基于传统的印刷排版技术，例如字体缩放、字间距规则和对齐网格。这些技术的成功运用，使用
户可以快速理解屏幕上的信息。为此，Ice Cream Sandwich 引⼊了全新的 Roboto 字体家族，它专为高分辨率屏幕而设计。

The current TextView framework offers Roboto in thin, light, regular and bold weights, along with an italic style for each weight. The framework also offers the Roboto Condensed variant in regular and bold weights, along with an italic style for each weight.

当前的 TextView 控件默认支持 thin、light、regular、bold，以及对应权重的斜体。也提供了叫 Condensed 的变种版本。

![typography_variants@2x](/images/typography_variants@2x.png)

####Default type colors
The Android UI uses the following default color styles: textColorPrimary and textColorSecondary. For light themes use textColorPrimaryInverse and textColorSecondaryInverse. The framework text color styles also support variants for touch feedback states when used inside UI elements.

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
Contrast in type sizes can go a long way to create ordered, understandable layouts. However, too many different sizes in the same UI can be messy. The Android framework uses the following limited set of type sizes:

字体⼤⼩上的不同有利于创造出有序和容易理解的布局。不过，在同⼀个界面中使用过多的字体⼤⼩则会造成混乱。Android 设计框架建议使用以下⼏种字体⼤⼩：

![typography_sizes](/images/typography_sizes.png)

Users can select a system-wide scaling factor for text in the Settings app. In order to support these accessibility features, type should be specified in `scale-independent pixels` (sp) wherever possible. Layouts supporting scalable types should be tested against these settings.

用户可以在`设置`中调整整个系统的字体⼤⼩。为了支持这⼀特性，字体的⼤⼩应尽量使用可缩放的单位，我们称之为(sp)。另外，要针对支持 sp 缩放的布局做测试，即改变系统字体大小，看布局是否显示正常。