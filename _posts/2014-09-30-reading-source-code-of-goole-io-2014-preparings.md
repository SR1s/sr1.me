---
title: AndroidStudio安装、使用遇到的问题
category: way-to-explore
layout: post
---

# Android Studio
谷歌开源了Google IO 2014的官方应用的[源代码](https://github.com/google/iosched)，想着研究下源码涨涨姿势，结果发现源码用的是Android Studio开发的。官方也推荐使用Android Studio来构建程序。为了跟境外先进技术接轨，是以开始了从Eclipse向Android Studio的转移。

Android Studio是一款Android集成开发环境(IDE)，由Android官方基于Intellij Idea开发。从发布到现在已经有好几年了，虽然还是Beta版，但已经比第一版稳定了很多。个人最明显的感受是：Github上用Android Studio开发的开源项目越来越多……如果再不接触它，恐怕将来会跟不上境外先进势力的发展趋势。但由于拖延症，一直迟迟没有去尝试它。(就跟研究源码的念头一样..囧)

# Android Studio的安装
从Android[官方网站](http://developer.android.com/sdk/installing/studio.html)上可以直接下载安装程序，我使用的是Windows版。下载完成之后双击安装就可以了。

按理来说，安装完成之后应该是能轻松打开程序的，从官方下的Eclipse就是这样子。但我错了。打开程序的时候，闪过了一下程序的启动界面，然后界面关闭，应用程序窗口却没有如期出现。之后不管怎么启动程序都没用，这回连启动界面都没有。

期间我做了几个尝试：

1. 关机重启，现象依旧。(怀疑是刚卸载Intellij IDEA，没重启两个软件有冲突)

1. 卸载重装，现象依旧。(怀疑是下载过程中受GFW干扰，导致下载下来的文件不完整)

多方尝试未果，最后只能求助Google。在StackOverFlow上看到一个[回答](http://stackoverflow.com/questions/16574189/android-studio-installation-on-windows-7-fails-no-jdk-found)，说启动不了可能是JDK、JRE环境没配置好。但我打开CMD，分别输入java、javac都能得到运行的输出(Eclipse都运行得好好的)。

文中有回答说也遇到我这种情况，毫无提示，但程序就是启动不了，只能切换到cmd下，在Android Studio的启动目录下运行studio.bat文件，从文件的输出上看出问题的。于是马上转战命令行，运行的结果显示：启动JVM的时候遇到一个问题，无法找到某个默认的类(根据印象翻译的)。但这个问题找的解决方法不怎么好用，所以索性重新装了个JDK 1.8。

安装完毕后记得：

1. 配置系统环境变量JAVA_HOME为JDK的安装路径；

1. 在系统环境变量的Path里添加JDK、JRE的路径。

这个时候Android Studio总算是启动起来了。耗时两晚，实在是折腾。

# 使用Android Studio
能启动就能轻松上手使用了吗？事实证明我还是太天真了。带着从Eclipse积攒过来的经验，我选择了导入工程，然后选择了源码里的android文件夹。看起来好像是导入了呢……然后点了下工具栏上的“Sync project with Gradle”图标，然后Gradle就开始下载一堆东西，构建项目依赖。等待N久，报了好几个错：

1. 找不到SDK路径，需要去系统环境变量里设置ANDROID_SDK_HOME的值；

1. failed to find xxx.xxx.xxx(Java包，大部分是Android相关的)，这个就要交给SDK Manager来处理了；

1. 找不到Git工具，下载个Git安装就行了。

第一个问题很好解决，后面两个就比较坑爹了。得益于我大中华局域网的可靠保障GFW，谷歌相关服务基本废掉，包括SDK Manager连接的dl-ssl.google.com，git的官方网站[git-scm.com](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CBsQFjAA&url=%68%74%74%70%3a%2f%2f%67%69%74%2d%73%63%6d%2e%63%6f%6d%2f&ei=kngrVJypEcnmuQSgz4LgDQ&usg=AFQjCNHBOAWIJWLQ6wmj_GErAgGzWCkTbA&bvm=bv.76477589,d.c2E)也惨遭毒手。所以说搭(fan)梯(qiang)是天朝程序员的必备技能之一。用自建的SS下了个Git，还算简单。但SDK Manager只支持http代理，不支持Socks，手头又没有http代理，只能改Host曲线救国了。好在学校支持IPv6，能直连谷歌的IPv6服务器，找了个可用的地址(如：2404:6800:4005:805::1008)，配置了下Host，总算能下了。除了速度慢了点、偶尔会出现超时，其他都还好。

经历了以上的折腾，Android Studio正常使用是没问题了。

对了，期间还出现过要求选择Gradle Home目录的情况，虽然官方说可以跳过不理，但我的版本找不到跳过的选项，这个Gradle Home目录是：Android Studio的安装目录/plugins/gradle/，需要配置的话就用他了。

# One more thing...
上面只是简单说了下安装、使用过程中遇到的一些问题，具体使用还是要在摸索。官方有使用的简单[说明](http://developer.android.com/sdk/installing/studio.html)，晚上匆匆过了一遍，感觉Android Studio还是比Eclipse要强的。推荐一看。

That's all, But NOT ALL. ;p
