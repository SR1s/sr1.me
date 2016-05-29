---
title: Android从入门到不归路
category: knowledge-graph
layout: post
tag: 长夜漫漫
---

# 零

从12年开始接触Android，那个时候2.3还大行其道，4.0才刚刚出炉，算是从蛮荒时代一路过来，以一个学习者的姿态，一路磕磕碰碰的过来，蓦然回首，发现世界已经变了。

去年开始就想整理一下Android的学习资料，毕竟已经过了最开始的时期，现阶段学习Android可以说比任何时候都要方便，在线教育开始成熟，互联网在移动互联网的带动下已经非常普及，而且，各种内容分发平台也如雨后春笋冒了出来，开发者相关的从码农周刊、各种日报开始，到现在的开发者头条、稀土掘金，现在获取信息已经很方便了，但如果再让当年的我来看，应该会挑花了眼吧。

先开个坑，发下去年的大纲，后续逐渐把内容补全。目标(但不承诺实现)与时俱进，基础又有一定深度。

---

# Android开发入门

## Chapter 1: 简介
不出意外这一章是Android的关于，老生常谈的介绍一下AndroidMa，同时搭建一下开发环境，再弄一个Hello World就了事了。这部分我希望能跟我大学最喜欢的毛sir的计算机导论入门一样，穿插一些趣事，讲一些行话，介绍一下工作中的一些流程经验

## Chapter 2: Java基础
Java基础是不需要说了吧。这里会说到大致需要多少水平的基础。关于Android开发语言，最近也有很多的信息，如Jetbrain开发的Kotlin语言比Java要好用，Google准备和苹果一起推动使用Swift开发，Facebook主导开发的ReactNative会取代源生开发。

## Chapter 3: 项目基础结构
普及性项目的结构，消除陌生感紧张感

## Chapter 4: UI设计，基础控件
这里就比较枯燥了，这里会说下一些常用的技巧和注意点吧

基础的控件学完了，怎么方便快捷的做出好看的UI和交互呢？最重要的是，符合Android Material Design！在一开始，很多东西都要我们徒手来完成，好在15年，官方推出了Android Design Support库，帮我们解放了双手：

下面这个教程手把手带你领略了一把这个类库：
https://inthecheesefactory.com/blog/android-design-support-library-codelab

## Chapter 5: Activity和跳转
这里也很基础，四大基础组件之一

## Chapter 6: Fragment
这个现在用得很多，UI轻量化的方式，但从工作中来看，也轻量不到哪里去，而且没看到一个好的UI框架能很好的管理Fragment这块。

## Chapter 7: SharedPreference本地持久化
也很常见啦，还有数据库，还有本地文件

## Chapter 8: ContentProvider
数据库的进一步封装

## Chapter 9: 异步
这里讲异步会不会太晚？异步化一直是一个很重要概念，很大一部分的优化的目的，就是降低对主线程的使用，是一个很核心的概念

## Chapter 10: Service
长时间运行的组件，这里讲下什么场景下使用它，比如Aidl，比如锁屏

## Chapter 11: Broadcast
系统里的大喇叭

## Chapter 12: Alarm、JobSchedular
定时任务，这里可以再说下耗电相关的东西

## Chapter 13: Notification 通知
这里很简单，但要有节操的使用它

## Chapter 14: 硬件相关Camera之类的
操作硬件的一些技巧


## Chapter 15: WebView
新的大坑，介绍下QQ浏览器的X5内核，还有官方的webkit

## Chapter 16: 消息推送
这个也是百花齐放

## Chapter 17: 几种常见的数据格式json、xml
前后台交互的根本

html解析库：https://jsoup.org/
目标是直接上方便使用的库，快速上手，搜索得到的结果大部分的资料还是很繁重的写法，心累。

## Chapter 18: 优化相关
应用如何优化性能，说下工具？

## Chapter 19: 测试
单元测试，UI测试，自动化测试，软件质量之类的

## Chapter 20: 进阶建议
如何精进的建议


> Written with [StackEdit](https://stackedit.io/).
