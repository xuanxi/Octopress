---
layout: post
title: "Android Style (2)"
date: 2014-07-29 09:56:06 +0800
comments: true
categories: Android
---
本篇分为以下几个部分：  
6. Color  
7. Iconography  
8. Your Branding  
9. Writing Style
<!--more-->

##6. Color

##7. Iconography
![iconography_overview](/images/iconography_overview.png)

An icon is a graphic that takes up a small portion of screen real estate and provides a quick, intuitive representation of an action, a status, or an app.

图标就是⼀个直观的图形，占据屏幕一个小空间，用来代表⼀个操作、⼀种状态或者⼀个应用本身。

When you design icons for your app, it's important to keep in mind that your app may be installed on a variety of devices that offer a range of pixel densities, as mentioned in Devices and Displays. But you can make your icons look great on all devices by providing each icon in multiple sizes. When your app runs, Android checks the characteristics of the device screen and loads the appropriate density-specific assets for your app.

当你为应用设计图标时，需要紧记的是你的应用可能会被安装到多种分辨率的设备中。不过，你可以通过提供不同大小的图标来适配各种密度的设备。

Because you will deliver each icon in multiple sizes to support different densities, the design guidelines below refer to the icon dimensions in dp units, which are based on the pixel dimensions of a medium-density (MDPI) screen.

因为你要提供多种规格图标来适配多种密度，下文中提到的图标尺寸都用 dp 为单位。dp 是以 MDPI 中的像素为基准的，即在 MDPI 中 1dp=1px，相应的，在 HDPI 中 1dp=1.5px，在 XHDPI 中 1dp=2px，如下图所示：

![devices_displays_density@2x](/images/devices_displays_density@2x.png)

So, to create an icon for different densities, you should follow the 2:3:4:6:8 scaling ratio between the five primary densities (medium, high, x-high, xx-high, and xxx-high respectively). For example, consider that the size for a launcher icon is specified to be 48x48 dp. This means the baseline (MDPI) asset is 48x48 px, and the high density (HDPI) asset should be 1.5x the baseline at 72x72 px, and the x-high density (XHDPI) asset should be 2x the baseline at 96x96 px, and so on.

这样，当你设计图标的时候，应该按照  2:3:4:6:8（或者 1:1.5:2:3:4） 的比例去缩放。例如，一个应用启动图标的规格为 48x48 dp，意味着 MDPI 的大小是 48x48 px，HDPI 的大小是 72x72 px，以此类推。

Note: Android also supports low-density (LDPI) screens, but you normally don't need to create custom assets at this size because Android effectively down-scales your HDPI assets by 1/2 to match the expected size.

注意：Android 也支持一种更小的屏幕密度 LDPI，不过通常不需要考虑这种密度，因为 Android 会将 HDPI 中的图标缩小一半来适配它。  
补充：在要求不太严格的情况下，我们可以只为高密度屏幕设计一套图标，比如 XHDPI，Android 会自动缩小尺寸适配所有密度屏幕。

###Launcher
启动图标

The launcher icon is the visual representation of your app on the Home or All Apps screen. Since the user can change the Home screen's wallpaper, make sure that your launcher icon is clearly visible on any type of background.

启动图标用在 Home 界面和 All Apps 界面。因为用户可以改变 Home 的背景，所以要考虑启动图标是否能够在任何类型的背景中都清晰可见。

![iconography_launcher_size](/images/iconography_launcher_size.png)

Sizes & scale 大小与缩放  
1. 启动图标在移动设备中必须为 48x48 dp；  
2. 启动图标在 Google Play 中必须为 512x512 pixels。

![iconography_launcher_focal](/images/iconography_launcher_focal.png)

Proportions 对称  
图标不要留白，要占满 48x48 dp 大小。

![iconography_launcher_style](/images/iconography_launcher_style.png)

Style 样式  
Use a distinct silhouette. Three-dimensional, front view, with a slight perspective as if viewed from above, so that users perceive some depth.  
使用清晰的轮廓。三维的正面视图，看起来稍微有点从上往下的俯视效果，使用户能看到⼀些景深。

![iconography_launcher_example2](/images/iconography_launcher_example2.png)

###Action Bar
Action bar icons are graphic buttons that represent the most important actions people can take within your app. Each one should employ a simple metaphor representing a single concept that most people can grasp at a glance.

操作栏图标是⼀个图像按钮，用来表示用户在应用中可以执行的重要操作。使用简洁明了的图像代表将要执行的操作，让用户⼀目了然。

Pre-defined glyphs should be used for certain common actions such as "refresh" and "share." The download link below provides a package with icons that are scaled for various screen densities and are suitable for use with the Holo Light and Holo Dark themes. The package also includes unstyled icons that you can modify to match your theme, in addition to Adobe® Illustrator® source files for further customization.

Android 内置了一些通用操作的图标供使用，例如“刷新”和“分享”。下面提供了图标包的下载链接，其中包括多种屏幕分辨率，以及对应 Holo Light和 Holo Dark 主题的图标。包中带有尚未样式化的图标，你可以自由修改以搭配自⼰的主题，同时提供 Adobe® Illustrator® 源文件，供你做进⼀步定制。

[Download the Action Bar Icon Pack](http://developer.android.com/downloads/design/Android_Design_Icons_20131106.zip)

![iconography_actionbar_size](/images/iconography_actionbar_size.png)

Sizes & scale 大小与缩放  
Action bar icons for phones should be 32x32 dp.  

![iconography_actionbar_focal](/images/iconography_actionbar_focal.png)

Focal area & proportions 内容区域与对称  
Full asset 整体大小, 32x32 dp  
Optical square 实际内容, 24x24 dp

![iconography_actionbar_style](/images/iconography_actionbar_style.png)

Style 样式  
Pictographic, flat, not too detailed, with smooth curves or sharp shapes. If the graphic is thin, rotate it 45° left or right to fill the focal space. The thickness of the strokes and negative spaces should be a minimum of 2 dp.  
形象化，扁平，不要⼩细节，使用平滑曲线或尖锐的轮廓。如果是瘦长型的图形，将它转45度角再填满内容区。线的宽度不应低于2 dp。

![iconography_actionbar_colors](/images/iconography_actionbar_colors.png)

Colors for Light  
Colors: #333333  
Enabled: 60% opacity  
Disabled: 30% opacity

Colors for Dark  
Colors: #FFFFFF  
Enabled: 80% opacity  
Disabled: 30% opacity

###Small / Contextual Icons
小图标与上下文图标

Within the body of your app, use small icons to surface actions and/or provide status for specific items. For example, in the Gmail app, each message has a star icon that marks the message as important.

在应用的主体区域中，使用⼩图标表示操作或者特定对象的状态。例如在 Gmail 应用中，每条信息都有⼀个星型图标用来标记“重要”。

![iconography_small_size](/images/iconography_small_size.png)

Sizes & scale  
Small icons should be 16x16 dp.

![iconography_small_focal](/images/iconography_small_focal.png)

Focal area & proportions  
Full asset, 16x16 dp  
Optical square, 12x12 dp

![iconography_small_style](/images/iconography_small_style.png)

Style  
Neutral, flat, and simple. Filled shapes are easier to see than thin strokes. Use a single visual metaphor so that a user can easily recognize and understand its purpose.  
灰色为主、扁平和简单。最好使用填充图标而不是细线条勾勒。使用简单直观的图形，让用户容易理解图标的作用。

![iconography_small_colors](/images/iconography_small_colors.png)

![iconography_small_example](/images/iconography_small_example.png)

Colors  
Use non-neutral colors sparingly and with purpose. For example, Gmail uses yellow in the star icon to indicate a bookmarked message. If an icon is actionable, choose a color that contrasts well with the background.

在特殊情况下使用非灰色的图标。例如在 Gmail 应用中，使用了黄色的星型图标表示重要的信息。如果图标是可点击的，使用和背景形成对比的颜色。

###Notification Icons
![iconography_notification_size](/images/iconography_notification_size.png)

Sizes & scale  
Notification icons must be 24x24 dp.

![iconography_notification_focal](/images/iconography_notification_focal.png)

Focal area & proportions  
Full asset, 24x24 dp  
Optical square, 22x22 dp

![iconography_notification_style](/images/iconography_notification_style.png)

Style  
Keep the style flat and simple, using the same single, visual metaphor as your launcher icon.  
扁平，简单，类似启动图标。

![iconography_notification_example](/images/iconography_notification_example.png)

Colors  
Notification icons must be entirely white. Also, the system may scale down and/or darken the icons.  
通知图标必须为纯白色，另外，系统会缩小图标或令图标变暗。

###Design Tips
Here are some tips you might find useful as you create icons or other drawable assets for your application. These tips assume you are using Adobe® Photoshop® or a similar raster and vector image-editing program.

下面是一些图标设计建议，这些建议假设你使用 Photoshop 或者其它栅格或矢量图形工具。

####Use vector shapes where possible  
尽量使用矢量图

####Start with large artboards
从大画板开始设计

####When scaling, redraw bitmap layers as needed
？？

####Use common naming conventions for icon assets  
Try to name files so that related assets will group together inside a directory when they are sorted alphabetically. In particular, it helps to use a common prefix for each icon type. For example:  

使用统一的命名方法，使得所有图标在文件夹中会聚在一起并按字母顺序排列：

![icon_common_naming](/images/icon_common_naming.png)

####Set up a working space that organizes files by density  
为不同密度图标建立对应文件夹：
```
art/...
    mdpi/...
        _pre_production/...
            working_file.psd
        finished_asset.png
    hdpi/...
        _pre_production/...
            working_file.psd
        finished_asset.png
    xhdpi/...
        _pre_production/...
            working_file.psd
        finished_asset.png
```
####Remove unnecessary metadata from final assets  
Although the Android SDK tools will automatically compress PNGs when packaging application resources into the application binary, a good practice is to remove unnecessary headers and metadata from your PNG assets. Tools such as OptiPNG or Pngcrush can ensure that this metadata is removed and that your image asset file sizes are optimized.
 
尽管 Android 在打包时会自动压缩 png 图片，但图片中仍然包含着不必要的头部信息和 metadata，你应该把这些信息也删除掉。[OptiPNG](http://optipng.sourceforge.net/) 和 [OptiPNG](http://pmt.sourceforge.net/pngcrush/) 可以帮你这个忙。

##8. Your Branding
你的品牌

Following Android design patterns doesn't mean that your app has to look the same as everyone else's. In Android, your app can shine as an extension of your brand.

遵循 Android 的设计模式并不意味着你的应用和其它应用没什么区别，相反，你的应用可以表现出你的品牌的特点。

###Color
Use your brand color for accent by overriding the Android framework's default blue in UI elements like checkboxes, progress bars, radio buttons, sliders, tabs, and scroll indicators.

用你的品牌颜色取代 Android 默认的蓝色，新的颜色将会体现在复选框、进度条、单选按钮、滑动条、tab 按钮、滑动指示等常用控件上。

Look for opportunities to use high-contrast color for emphasis, for example, as the background color of the action bar or a primary button. But don't go overboard: not all actions are equal, so use it only for the one or two most important things.

在合适的地方用高对比度的颜色来作强调，比如 action bar 的背景颜色或者一个主要的按钮。记住不要过度使用这种颜色，因为不是所有操作都同等重要，所以只能用在一到两个最重要的地方。

When customizing colors, touch feedback should be subtle — just slightly lighter or darker than the untouched color.

在自定义颜色的时候，记得对触摸反馈也做出轻微调整——比原始状态稍亮或稍暗，具体可参考 “3.Touch Feedback”。

![branding_wallet](/images/branding_wallet.png)

The four colors of the Google Wallet logo provide a playful accent to the four dots that appear as the user enters a PIN.  


![branding_googlemusic](/images/branding_googlemusic.png)

The Google Play Music app has an orange theme color, which is used for emphasis in the action bar and for accent in the selected tab, scroll indicator, and hyperlinks.  
Google Play Music 使用橙色作为主题颜色，用在 action bar、tab、scroll indicator 和超链接上。

###Logo
首先要明确的是，launcher icon 不等于 logo，launcher icon 只是基于 logo 设计的图标。

Your app's launcher icon is a key place to incorporate your logo, because it's what users will look for and touch to begin using your app. You can carry the launcher icon through to all the screens in your app by showing it in the action bar along with the name of the app.  

应用的启动图标就是一个展示品牌 logo 的关键地方，因为用户要寻找它并通过它来启动应用。直接把启动图标放在 action bar 上就可以达到展示品牌的目的。

Another approach to consider is to have your logo take the place of the launcher icon and app name in the action bar.

另一方法是将 logo 放在 action bar 上，而不是使用启动图标和应用名字。

![branding_launcher_icon](/images/branding_launcher_icon.png)

![branding_logo_icon_action_bar](/images/branding_logo_icon_action_bar.png)

Google+ reinforces its brand by carrying its launcher icon through to the action bar.  
Google+ 直接把启动图标放在 action bar 上以凸显品牌。

![yourbranding_app](/images/yourbranding_app.png)

Example of a the logo in the action bar. This works well in cases where the brand's logo matches the name of the app.  
将 logo 放在 action bar 上的例子。

###Icons
If you have icons that you're already using for your app on other platforms and they have a distinctive look intended to fit your brand, use them on your Android app as well. If you take this approach, make sure your brand styling is applied to every single icon in your app.

如果你在其它平台的 app 上有符合你的品牌要求的图标，也可以用到 Android 上来，但要确保品牌与这些图标的风格一致。

![yourbranding_in-app-icons](/images/yourbranding_in-app-icons.png)

One exception: For any icon in your existing set where the symbol is different from Android's, use Android's symbol but give it your brand's styling. That way, users will understand what the purpose of the icon is based on what they've learned in other Android apps (Design principle: Give me tricks that work everywhere). But the icon will still look like it belongs with all of your other icons as a part of your brand.

Example:  
The brand's normal icon for sharing on other platforms is a right arrow.

![yourbranding_sharing](/images/yourbranding_sharing.png)

What if you don't already have your own icons — for example, if you're creating a brand new app only for Android? In this case, use Android's standard icons and rely more on color and logo for branding. Get the Action Bar Icon Pack, available for free in Downloads.

如果你没有自己的图标，并且只创建 Android 应用，这样的话，可以使用 Android 提供的标准图标，通过颜色和 logo 来展示你的品牌。

##9. Writing Style