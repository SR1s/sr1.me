---
title: Ubuntu升级之后启动器(Launcher)和状态栏(Panel)不见了解决方法
category: way-to-bug
layout: post
---

# 环境

Ubuntu 13.04

# 问题描述

装在VitrualBox里的Ubuntu一直运行得好好的，前几天看到有系统更新，也就习惯性地允许了升级。等到昨天开机之后才发现，启动器(Launcher)和状态栏(Panel)都消失不见了。

# 解决方法

<!--more-->

按照网上能找到的方法尝试了一遍，问题没解决，还好升级前把Ubuntu系统的虚拟硬盘文件复制到另一台电脑上，从那边复制了原来的文件，重新挂载硬盘，恢复了之前正常的样子。

以下收集了一些遇到同样问题的人的解决方法，希望能帮到遇到同样问题的人。

1 可能是Ubuntu的Unity组件没有配置正确，可以尝试重新配置一下Unity组件。使用默认快捷键Ctrl + Alt + T 打开终端(或者使用Ctrl + Alt + F1～F6进入命令行界面)，输入以下指令:

    sudo dpkg-reconfigure unity
    sudo dpkg --configure -a


如果现在还不能看到GUI界面，那就重启电脑。

    sudo reboot

2 也有可能是Ubuntu的Unity组件的接口出了问题，比如和显卡驱动不兼容。进入命令行，尝试输入以下指令：

    dconf reset -f /org/compiz/ 
    unity --reset-icons &disown

3 还有可能是Unity的插件没有开启，尝试进入命令行进行以下操作：

    # 安装CCSM
    sudo apt-get install compizconfig-settings-manager 
    # 告诉终端你想要在屏幕载入GUI程序
    export DISPLAY=:0
    # 启动CCSM
    ccsm

返回图形界面，这个时候应该能看到CCSM程序窗口显示在界面上，找到Unity插件的配置项，启用Unity插件。此时应该能解决问题了，如果不行，尝试重启。

# 参考资料

[Please help! launcher and menu gone after 13.04 upgrade][1]

[Ubuntu updating 13.04. Missing sidebar as well as header bar and mini, maxi and exit button][2]

[Unity doesn't load, no Launcher, no Dash appears][3]

 [1]: http://ubuntuforums.org/showthread.php?t=2148769
 [2]: http://askubuntu.com/questions/292045/ubuntu-updating-13-04-missing-sidebar-as-well-as-header-bar-and-mini-maxi-and
 [3]: http://askubuntu.com/questions/17381/unity-doesnt-load-no-launcher-no-dash-appears