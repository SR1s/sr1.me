---
title: 拥抱变化-React Native
category: think-when-god-laugh
layout: post
tag: 爬坑之路
---

# 前端什么最火
毫无疑问是React Native。给了新的想象空间。

# 入门

环境搭建参考官方文档，相关文件的下载比较久要耐心等待。

# 学习

这里说的是Android相关的东东，不排除有些东西跟iOS是相通的

# 相关备忘

## react-native run-android 
编译并安装Android应用到设备上，同时启动本地Server，用于向应用提供Js Bundle(React Native编写的程序)

## react-native start 
实际上Android应用大部分时候只需要第一次安装就行了，后续都是启动Server，向应用提供Js Bundle而已。通过这个命令就可以启动Server而不需要编译和安装Android源生应用壳这个步骤了。

## debug server ip and port
调试服务器就是用于提供Js Bundle的电脑啦，ip很容易查，ifconfig一下就好了。端口demo应用里用的是8081，记得配置一下，不然请求不到Js Bundle。