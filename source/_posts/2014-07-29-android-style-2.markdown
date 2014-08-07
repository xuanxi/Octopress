---
layout: post
title: "Android Style (2)"
date: 2014-07-29 09:56:06 +0800
comments: true
categories: Android
---
本篇包括以下内容：  
6. Color  
7. Iconography  
8. Your Branding  
9. Writing Style
<!--more-->

##6. Color

##7. Iconography
![iconography_overview](/images/iconography_overview.png)

图标就是⼀个直观的图形，占据屏幕一个小空间，用来代表⼀个操作、⼀种状态或者⼀个应用本身。

当你为应用设计图标时，需要紧记的是你的应用可能会被安装到多种分辨率的设备中。不过，你可以通过提供不同大小的图标来适配各种密度的设备。

因为你要提供多种规格图标来适配多种密度，下文中提到的图标尺寸都用 dp 为单位。dp 是以 MDPI 中的像素为基准的，即在 MDPI 中 1dp=1px，在 HDPI 中 1dp=1.5px，在 XHDPI 中 1dp=2px，如下图所示：

![devices_displays_density@2x](/images/devices_displays_density@2x.png)

这样，当你设计图标的时候，应该按照  2:3:4:6:8（或者 1:1.5:2:3:4） 的比例去缩放。例如，一个应用启动图标的规格为 48x48 dp，意味着 MDPI 的大小是 48x48 px，HDPI 的大小是 72x72 px，以此类推。

注意：Android 也支持一种更小的屏幕密度 LDPI，不过通常不需要考虑这种密度，因为 Android 会将 HDPI 中的图标缩小一半来适配它。  
补充：在要求不太严格的情况下，我们可以只为高密度屏幕设计一套图标，比如 XHDPI，Android 会自动缩小尺寸适配所有密度屏幕。

###Launcher
启动图标

启动图标用在 Home 界面和 All Apps 界面。因为用户可以改变 Home 的背景，所以要考虑启动图标是否能够在任何类型的背景中都清晰可见。

![iconography_launcher_size](/images/iconography_launcher_size.png)

Sizes & scale   
1. 启动图标在移动设备中必须为 48x48 dp；  
2. 启动图标在 Google Play 中必须为 512x512 pixels。

![iconography_launcher_focal](/images/iconography_launcher_focal.png)

Proportions  
图标不要留白，要占满 48x48 dp 大小。

![iconography_launcher_style](/images/iconography_launcher_style.png)

Style  
Use a distinct silhouette. Three-dimensional, front view, with a slight perspective as if viewed from above, so that users perceive some depth.  
使用清晰的轮廓。三维的正面视图，看起来稍微有点从上往下的俯视效果，使用户能看到⼀些景深。

![iconography_launcher_example2](/images/iconography_launcher_example2.png)

###Action Bar
操作栏图标是⼀个图形按钮，用来表示用户在应用中可以执行的重要操作。使用简洁明了的图像代表将要执行的操作，让用户⼀目了然。

Android 内置了一些通用操作的图标供使用，例如“刷新”和“分享”。下面提供了图标包的下载链接，其中包括多种屏幕分辨率，以及对应 Holo Light和 Holo Dark 主题的图标。包中带有尚未样式化的图标，同时提供 Adobe® Illustrator® 源文件，你可以自由修改以搭配自⼰的主题。

[Download the Action Bar Icon Pack](http://developer.android.com/downloads/design/Android_Design_Icons_20131106.zip)

![iconography_actionbar_size](/images/iconography_actionbar_size.png)

Sizes & scale  
Action bar icons for phones should be 32x32 dp.  

![iconography_actionbar_focal](/images/iconography_actionbar_focal.png)

Focal area & proportions  
Full asset 整体大小, 32x32 dp  
Optical square 实际内容, 24x24 dp

![iconography_actionbar_style](/images/iconography_actionbar_style.png)

Style  
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
在特殊情况下使用非灰色的图标。例如在 Gmail 应用中，使用了黄色的星型图标表示重要的信息。如果图标是可点击的，使用和背景形成对比的颜色。

###Notification Icons
通知图标

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
下面是一些图标设计建议，这些建议假设你使用 Photoshop 或者其它栅格或矢量图形工具。

####Use vector shapes where possible  
尽量使用矢量图

####Start with large artboards
从大画板开始设计

####When scaling, redraw bitmap layers as needed
？？

####Use common naming conventions for icon assets  
 使用统一的命名方法，使得所有图标在文件夹中会聚在一起并按字母顺序排列，如下图：

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
尽管 Android 在打包时会自动压缩 png 图片，但图片中仍然包含着不必要的头部信息和 metadata，你应该把这些信息也删除掉。可以使用以下两个工具：  
[OptiPNG](http://optipng.sourceforge.net/)  
[Pngcrush](http://pmt.sourceforge.net/pngcrush/)。

##8. Your Branding
你的品牌

遵循 Android 的设计模式并不意味着你的应用和其它应用没什么区别，相反，你的应用可以表现出你的品牌的特点。

###Color
品牌颜色

用你的品牌颜色取代 Android 默认的蓝色，新的颜色将会体现在复选框、进度条、单选按钮、滑动条、tab 按钮、滑动指示等常用控件上。

在合适的地方用高对比度的颜色来作强调，比如 action bar 的背景颜色或者一个主要的按钮。记住不要过度使用这种颜色，因为不是所有操作都同等重要，所以只能用在一到两个最重要的地方。

在自定义颜色的时候，记得对触摸反馈也做出轻微调整——比原始状态稍亮或稍暗，具体可参考 “3.Touch Feedback”。

![branding_wallet](/images/branding_wallet.png)

The four colors of the Google Wallet logo provide a playful accent to the four dots that appear as the user enters a PIN.  


![branding_googlemusic](/images/branding_googlemusic.png)

The Google Play Music app has an orange theme color, which is used for emphasis in the action bar and for accent in the selected tab, scroll indicator, and hyperlinks.  

###Logo
品牌 logo

首先要明确的是，启动图标（launcher icon）不等于 logo，启动图标只是基于 logo 设计的图标。不过，启动图标是一个展示品牌 logo 的关键，因为用户要寻找它并通过它来启动应用。直接把启动图标放在 action bar 上就可以达到展示品牌的目的。

另一方法是将 logo 放在 action bar 上，而不是使用启动图标和应用名字。

![branding_launcher_icon](/images/branding_launcher_icon.png)

![branding_logo_icon_action_bar](/images/branding_logo_icon_action_bar.png)

Google+ 直接把启动图标放在 action bar 上。

![yourbranding_app](/images/yourbranding_app.png)
 
将 logo 放在 action bar 上的例子。

###Icons
品牌图标

如果你在其它平台的 app 上有符合你的品牌要求的图标，也可以用到 Android 上来，但要确保品牌与这些图标的风格一致。

![yourbranding_in-app-icons](/images/yourbranding_in-app-icons.png)

One exception: For any icon in your existing set where the symbol is different from Android's, use Android's symbol but give it your brand's styling. That way, users will understand what the purpose of the icon is based on what they've learned in other Android apps (Design principle: Give me tricks that work everywhere). But the icon will still look like it belongs with all of your other icons as a part of your brand.

Example:  
The brand's normal icon for sharing on other platforms is a right arrow.

![yourbranding_sharing](/images/yourbranding_sharing.png)

如果你没有自己的图标，并且只创建 Android 应用，这样的话，可以使用 Android 提供的标准图标，而通过颜色和 logo 来展示你的品牌。（对设计能力不足的小团队可以这样做）

##9. Writing Style