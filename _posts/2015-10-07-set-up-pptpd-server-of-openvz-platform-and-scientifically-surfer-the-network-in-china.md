---
title: VPN科学上网全攻略
category: think-when-god-laugh
layout: post
tag: 爬坑之路
---


这不是一个教程，这只是教程的合集，还有记录遇到的坑和填坑方案，以及一些扩展出来的知识。

内容包括：

- 如何搭建VPN服务器(基于pptpd)
- OpenVZ虚拟化架构的VPS上，配置上和普通的方法有什么不一样
- 一个非常可笑且荒谬却流传甚广的sysctl报错解决方案
- iPhone上能正常连接，Mac上却一直验证失败的解决方法
- 实现智能分流，科学上网


# 起因

本来已经在VPS上搭好了一个VPN，一直以来也用得好好的，但总觉得速度有点慢，某天看到搬瓦工上可以切换机房。本着不折腾会死的心理，迁移起了机房。

然后悲剧的发现，其他地方：杰克逊维尔(Jacksonville)、凤凰城(Phoenix)的速度还不如洛杉矶(Los Angeles)，最后只能再度迁移回去。

然后悲剧的发现，VPN不能正常工作了…

因为迁移的过程中一直用的是`scp`命令在本地和服务器之间拷贝文件来测速，所以也一直没发现这个问题。

再再因为之前是用脚本傻瓜化安装的，完全不知道到底出了什么问题，无奈之下只能重装。然后踩了一堆的坑，总算是搞定了。

# 如何搭建基于PPTP的VPN服务器

一开始我参考的都是中文的教程，因为遇到了能连上VPN，但上不了网的问题，所以我把谷歌搜索结果前两页的教程都看了一遍，感觉都是大同小异，有的时候怀疑是不是从同一个作者那里抄过来的，因为某个谬误在很多篇教程里都提到了，这个下面会说到。

## 1. 非OpenVz虚拟化架构的VPS和服务器

先给出一个Ubuntu社区的教程，这个教程适用于非OpenVz虚拟化架构的VPS，国内的教程来来去去说的都是这上面的东西：[https://help.ubuntu.com/community/PPTPServer](https://help.ubuntu.com/community/PPTPServer)

如果英文阅读有障碍，这里给出一个还算靠谱的中文教程，但是千万千万要注意：

**修改sysctl.conf这一步，如果遇到sysctl报错的解决方案千万不要去搜中文解决方案**
**修改sysctl.conf这一步，如果遇到sysctl报错的解决方案千万不要去搜中文解决方案**
**修改sysctl.conf这一步，如果遇到sysctl报错的解决方案千万不要去搜中文解决方案**

**[以上警告事项已知晓，点击查看中文教程](http://blog.atime.me/note/pptpd.html)**

关于这个报错的解决方案后面会提到。

上面的中文教程还说了一些Ubuntu社区教程没说的东西，比如：

- pptp存在安全问题，详情参考[这里](http://pptpclient.sourceforge.net/protocol-security.phtml)
- 无法连接或连接后无法解析DNS
- Virtualbox上的系统无法使用pptp vpn的问题
- 开启pptpd的debug输出，并使用`pptpd -f`来调试问题

## 2. OpenVz虚拟化架构的VPS

我的VPS是搬瓦工提供的，这个提供商的VPS是基于OpenVZ的，它们跟主流的基于KVM、XEN的不一样，据说坑很多，比如搭建VPN服务器。o(╯□╰)o

我照着上面的教程做完之后，发现能连接VPN，但是却上不了网，不管怎样都不行。

先给出一个在OpenVZ上的教程，英文：[http://www.putdispenserhere.com/pptp-vpn-setup-guide-for-a-debian-openvz-vps/](http://www.putdispenserhere.com/pptp-vpn-setup-guide-for-a-debian-openvz-vps/)

这个教程跟上一个的教程不一样的地方在于：
上一个使用`iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE`来转发流量，而OpenVz的教程使用的是`iptables -t nat -A POSTROUTING -j SNAT --to-source <你的VPS的公网IP>`

同时，你会发现，使用`ifconfig`会看到OpenVz虚拟化架构的VPS上没有eth0这个设备，取而代之的是一个叫venet0:0的设备，关于这个设备，可以看OpenVZ官方维基上的[说明](http://wiki.openvz.org/Virtual_network_device)。

就是因为这个不同，导致了常规的方法不能成功。

如果你看了这两个教程会发现，Ubuntu社区给出的教程里直接把对iptable的配置加入了开机自启动的文件里，而这篇关于OpenVZ的则是先把iptable的配置保存到本地文件，然后在开机自启动文件里把配置重新加载进来。

这两种方法都可以，但我更推荐Ubuntu社区的做法，因为开机自启动的时候加载配置可能会把其他程序的配置给覆盖了，导致其他程序运行不正常。

# 关于sysctl -p执行报错的解决方案

## 一个中文圈广泛传播的错误的解决方案

很多人(包括我)，在配置开启ipv4转发的时候，遇到了这样一个错误：

    [root@Server ~]# /sbin/sysctl -p
    net.ipv4.ip_forward = 1
    net.ipv4.tcp_syncookies = 1
    error: "net.bridge.bridge-nf-call-ip6tables" is an unknown key
    error: "net.bridge.bridge-nf-call-iptables" is an unknown key
    error: "net.bridge.bridge-nf-call-arptables" is an unknown key

然后我网上搜了一圈：

找到一个解决方案，只需要四行命令：

    # 这是一个错误的解决方案
    # 叔叔有练过，小朋友不要轻易尝试
    # 后悔一生
    rm -f /sbin/modprobe
    ln -s /bin/true /sbin/modprobe
    rm -f /sbin/sysctl
    ln -s /bin/true /sbin/sysctl

然后再次执行`sysctl -p`，这个报错就“完美”的解决了！

但是真的是这样吗？

停下来仔细想下，上面这四行命令实际上做了以下几件事：

    # 这是一个错误的解决方案
    # 叔叔有练过，小朋友不要轻易尝试
    # 后悔一生
    # 把modprobe这个命令的可执行文件删除了
    rm -f /sbin/modprobe
    # 创建一个叫modprobe的快捷方式，指向/bin/true
    ln -s /bin/true /sbin/modprobe
    # 把sysctl这个命令的可执行文件删除了
    rm -f /sbin/sysctl
    # 创建一个叫sysctl的快捷方式，指向/bin/true
    ln -s /bin/true /sbin/sysctl

那么`/bin/true`这个东东，到底是什么呢？StackOverflow上的[回答](http://stackoverflow.com/questions/2175405/what-is-bin-true)是这样的：

> `/bin/true` is a command that returns 0 (a truth value in the shell).
> Its purpose is to use in places in a shell script where you would normally use a literal such as "true" in a programming language, but where the shell will only take a command to run.
> `/bin/false` is the opposite that non-zero (a false value in the shell).
> 
> From the man page:
> `true - do nothing, successfully`
> `true returns a status 0`

也就是这个命令什么都不会做，只会返回命令执行成功。也就是说，上面的解决方案实际上把我们两个有用的命令删除了，拿一个什么都不干只会返回成功的命令代替了他们来执行命令。这不是自欺欺人吗？！

可悲的是我最开始也是无脑操作，直到执行到第三句命令的时候想，为什么链接的是同一个文件呢？然后才醒悟过来，但已经晚了，我的modprobe命令怎么也回不来了，最后只能重装系统。%>_<%

可惜这个方法过于有效，以至于网上很多地方引用了这个解决方案，比如：[这个](http://www.isays.cn/6022.html)、[还有这个](http://blog.lokyshin.com/?p=47)、[那个](http://5beta.com/linux/centos-sysctl-p-error.html)、[还有那个(见评论区)](http://www.tennfy.com/1364.html)，流毒甚广。[摊手]

## 2. 实际的解决方案

那么这个报错应该怎么解决呢？

可以看到，很多人采用错误的解决方案后，成功的跳过了这个问题，并且不影响操作，因此可以说，只要我们执行过一次`sysctl -p`，`net.ipv4.ip_forward=1`这个设置就已经生效了。所以不用理会它就可以了。

但如果你看着这个搞错感觉很碍眼，可以到之前那个配置文件里，找到报错的键所在的行，如`net.bridge.bridge-nf-call-ip6tables`所在的行，在最前面加上`#`把这行配置注释了，再次执行`sysctl -p`就不会报错了。

从帮助信息里可以看到：

    [root@Server ~]# sysctl -h
    Usage:
      sysctl [options] [variable[=value] ...]
    Options:
      -p, --load[=<file>]  read values from file

`sysctl -p`命令是从配置文件里读取配置，所以即便读取失败，也不会影响正常的配置生效。

因此解决方案是：

- 对于非强迫症的人来说，直接忽略报错即可；
- 对于强迫症患者，打开配置文件，把报错的条目注释掉即可。

# 还有一个问题

搞完了上面这些，我的iPhone终于能够连上VPN上网了，但奇怪的是，在Mac上每次连接都不成功。打开pptpd的调试模式，看log发现是验证失败。但同样的账户密码，为什么会失败呢？

搞了好久都没头绪，然后某一瞬间，想到会不会是用户账户配置文件的问题，在用户账户配置文件里，我使用Tab作为分隔符，分开各项配置，会不会实际上是空格呢？尝试把Tab改为空格，然后重启pptpd服务，问题解决。

从pptpd的用户账户配置文件[示例](http://poptop.sourceforge.net/dox/chap-secrets.txt)里，也可以看到各项配置的分隔符是空格。这点在很多地方都没说到，但具体为什么会有的可以验证成功有的又不行我也搞不清楚。

# 扩展：分流国内国外流量，科学上网不绕道

开了VPN，访问国外是没问题了，但访问国内难道还要跑一遍VPN再到国内拿数据，然后再通过VPN传回来？数据多跑了一圈，且不说国际带宽很有限，就这么来回折腾，效率也是很低的。

好在已经有大牛开源了一个帮助我们区分国内国外流量的项目，只需要简单的配置，就能实现国内不走VPN，国外走VPN了，而且Windows、Linux、Mac都支持，实在是良心啊。项目的README上写得很清楚，这里就不费多口舌了。[项目传送门](https://github.com/jimmyxu/chnroutes)

原理大概是在建立\撤销连接前\后设置一个路由表，让系统根据路由表来决定如何请求数据。

至此科学上网的攻略算是大功告成！终于又可以愉快的当一只码狗好好写代码调八阿哥了。

# That's all, But NOT ALL.

> Written with [StackEdit](https://stackedit.io/).