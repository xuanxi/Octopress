---
layout: post
title: "Android Studio vs Eclipse: What are the main differences?"
date: 2014-08-07 14:09:55 +0800
comments: true
categories: Android
---
原文：  
<http://www.avocarrot.com/blog/android-studio-vs-eclipse-main-differences/>

![androidstudio](/images/androidstudio.png)
<!--more-->

Most of Google’s 2013 I/O event was about Android. One specific announcement managed to attract a lot of hype and that was no other than Google’s own Android IDE, Android Studio. It has been just over a year now since the first public release and Android Studio has come a long way, despite still being in beta. The IDE itself is based off the very popular IntelliJ IDEA from JetBrains and is being offered by Google for free.

Google 2013 I/O 大会的大部分内容是与 Android 相关，其中，专为 Android 打造的开发环境——Android Studio 无疑最引人注目。Android Studio 从公开第一个版本至今已经走过一个年头，尽管仍然处于 beta 版本（公开测试版），但相比一年前，Google 已经对其做了相当多的改进。Android Studio 基于非常著名的 JetBrains 公司的产品——IntelliJ IDEA，并免费提供给开发者。

On the other hand, Eclipse is more mature than ever and Google’s ADT plugin which transforms the popular IDE into a fully featured Android developing environment has become very stable. Eclipse feels like home for many Java developers and is a natural starting point for Java developers who want to get into Android development.

另一方面，基于 Google ADT 插件的 Eclipse 现在已经成为 Android 开发的一个非常成熟稳定的环境，作为要进入 Android 开发领域的 Java 开发人员，Eclipse 是一个自然而然的选择。

While both solutions look promising, which one has the edge over the other? In this blog we will compare the two in 5 distinct areas in an effort to reveal the main differences in the Android Studio vs Eclipse battle.

两个开发环境看起来都有不错的前景，那么哪个更具优势呢？本文试图从5个方面分析它们的不同。

###Build Tools
Android Studio utilizes the fast growing Gradle build system. It builds on top of the concepts of Apache Ant and Apache Maven but it also introduces a Groovy DSL (Domain-Specific Language) that allows for scripted builds which opens up many automation possibilities like uploading your beta .apk to TestFlight for testing. Eclipse on the other hand uses Apache Ant as its main build system which a very robust XML based build system that many Java developers may already be familiar with.

Android Studio 使用了当前发展迅猛的 Gradle 构建系统，它基于 Groovy 脚本语言，可以实现比 Ant 和 Maven 更灵活的构建过程，如自动上传测试包。而 Eclipse 使用成熟健壮的 Ant 作为构建系统。

###Advanced Code Completion/Refactoring
Both IDEs feature the standard Java code auto completion but in the case of Android Studio, Google has baked in deeper support for specific Android code and refactoring. Android Studio can refactor your code in places where it’s just not possible using Eclipse and ADT. In addition, in my opinion IntelliJ’s Java auto completion seems more “intelligent” and predicts better what I want to do so there is definitely an improvement in this area over Eclipse.

两个 IDE 都提供了代码补全和重构功能，不过，Android Studio 在对 Android 代码的补全和重构方面做了更好的支持。另外，我认为 IntelliJ 的 Java 代码补全更加智能，预判准确率更高，这个相比 Eclipse 无疑是一个进步。

###User Interface Design
One of the main selling point Google used to market Android Studio when it came out was its completely redesigned user interface design tool. After working with it for some time, it’s clear that the new tool is much better than the old. It literally crashes it. The new interface design tool in Android Studio is faster, responds to changes more rapidly and has more customization options that with Eclipse, you had to manually set in the XML.

用户界面设计工具是 Android Studio 的一个主要卖点。比起 ADT，Android Studio 上的设计工具更快、更灵活，比如在 Android Studio 的设计面板上可以直接设置元素属性，而在 Eclipse 上只能手动修改 XML 文件。

###Project Organization
Both IDEs work differently in an effort to help you manage and organize your projects. If you’ve used Eclipse then you must be familiar with the concept of workspaces. When Eclipse starts, you select the workspace that contains your projects and you can load all project of that workspace in your tree navigation. If you want to switch to a project in a different workspace, then you have to restart the whole IDE. Android Studio treats this situation differently by introducing the concept of modules. Your app could be one module, a library that you just downloaded can be another and the Ad SDK you are currently integrating could be a third. Each of these modules can have their own Gradle build files and declare their own dependencies. To me, Android Studio seems more natural but it takes some time to get used to if you have been using Eclipse for a long time.

两个 IDE 在工程组织上各不相同。Eclipse 使用 workspace - project 的结构，Android Studio 使用 project - module 的结构，我更偏向 Android Studio 的组织方式。（从表面上看，貌似也没多大差别啊）

###IDE Performance/Stability
Eclipse is a purely Java based software, and a big one. In order to run it reliably you need to have more than decent amount of RAM and good CPU power to back it up. Many user who do not strictly meet these criteria are reporting very bad experiences with it. It is not unusual for Eclipse to crash while exporting an apk or having to restart it after using it for a few hours straight. Having said that, Android Studio is still in beta so it comes with its own bugs that crash the IDE every now and then but in the meanwhile, the whole experience feels faster and more robust.

Eclipse 是一个使用纯 Java 语言编写的程序，由于它是基于可扩展的设计思想，即通过各种插件来提供特定开发环境，所以本身体积较大，运行时需要较多的内存和 CPU 资源，你可能会遇到这样的情况：打包 apk 时程序崩溃了，或者运行几个小时后因为响应太慢而不得不重启。对于 Android Studio ，上面已经提到它仍然处于 beta 阶段，会偶尔崩溃，不过，现时它的整体体验已经相对更快和更稳定。

###Conclusion
Having used both Android Studio and Eclipse for a while now, I would personally say that Android Studio has the edge over the two. It might be a bit unstable yet and some updates require a complete re-installation of the software but when it eventually comes out of beta, it will blow Eclipse with ADT out of the water. I especially like the stability of the editor and not having to reboot every now and then, the new and improved UI designer and the sexy themes that make Android Studio a real eye candy. What side will you take in the Android Studio vs Eclipse battle?

我同时使用 Android Studio 和 Eclipse 已有一段时间，我个人认为 Android Studio 更具优势，虽然存在不稳定性，以及升级可能需要重装，但就在 beta 版发布之后，它完全可以击败 Eclipse。我尤其喜欢它的编辑器的稳定、无需重启、增强的界面设计和性感的外观。

##我的看法
和本文作者一样，我也同时使用这两个 IDE 一段时间了（公司用 Eclipse，个人用 Android Studio），并同样感受到 Android Studio 的明显优势：  
1. 快，无论是启动程序，还是打开 XML 文件，都可以明显看出两个 IDE 的速度差别；  
2. 酷，那个 Darcula 的外观实在比 Eclipse 呆板的外观好看得多（Eclipse 中可使用 color theme 来美化外观，但个人觉得也比不上 Android Studio）。  
对于重构和新的界面设计工具，我的使用经验还不多，这里就不作评论了。