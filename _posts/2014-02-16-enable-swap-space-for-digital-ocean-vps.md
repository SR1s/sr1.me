---
title: 为Digital Ocean的VPS主机启用Swap交换空间
layout: post
---

# 起因
Digital Ocean的VPS最近挺火的，基础配置(512MB内存，20G固态硬盘，每月1TB流量)才5刀/月的价格让人想抗拒都难。不过512MB的内存用起来还是有点紧张的，网站访问量不高还可以，访问量一高内存紧张，系统会把一些进程杀死，比如mysql的进程。那个时候虽然网站还是能访问，但失去了数据库链接的网站跟宕机没什么两样。

难道只能升级配置了？本来我也是这么打算的，前不久在HN上看到一篇文章吐槽DigitalOcean，说DO默认没有开启Swap分区，背后是打着驱使用户从基础配置往上升级的主意。文章的最后给出了DO官方发表的《如何在CentOS 6 上为VPS主机启用Swap交换空间》，进过测试，官方给出的方法同样适用于Ubuntu 12.04。

# Swap空间
Swap空间类似于Windows系统下的虚拟内存，系统可以通过把不常运行的程序占用的内存空间转移到硬盘上，为正在运行的程序提供更多的物理内存，从而加快程序运行的速度。由于硬盘的速度比内存的速度慢了好几个数量级，因此在将转移到虚拟内存中的程序切换到前台运行的时候，性能上会有所降低。但由于DO适用的是固态硬盘，速度很快，因此性能上的降低不会像机械硬盘那样降低很多。为基础配置的VPS启用Swap空间能有效提高主机的性能。

---

# 启用Swap空间

## 检查是否已经适用了Swap空间
打开终端，输入`swapon -s`命令，如果没有任何的信息返回，那就是还没有启用Swap空间。

## 检查文件系统
知道我们还没有启动Swap空间后，我们再检查一下我们的硬盘还剩下多少空间可以使用，使用`df`命令查看。在本例中，我们将创建一个512MB大小的Swap空间文件，从指令的输出可以看到，我们有足够的空间用于创建这个文件。
    df
    Filesystem           1K-blocks      Used  Available Use% Mounted on
    /dev/hda              20642428   1347968   18245884   7% /

## 创建并使用Swap空间文件
通过使用`sudo dd if=/dev/zero of=/swapfile bs=1024 count=512k`指令，我们将在根目录下创建一个大小为512MB，文件名为swapfile的Swap空间文件。`of=/swapfile`参数指定了文件的创建位置和文件名；`count=512k`指定了文件的大小。
执行了上述步骤后，我们就可以将这个文件用于Swap交换空间的使用了。输入指令`sudo mkswap /swapfile`，将得到以下结果：
    Setting up swapspace version 1, size=536866kB
最后，使用`sudo swapon /swapfile`激活Swap空间
使用`swapon -s`指令，将得到以下结果：
    swapon -s
    Filename                                Type            Size    Used    Priority
    /swapfile                               file            524280  0       -1

但以上的设置是一次性的，重启后将失效。想要确保系统永久的启用Swap交换空间，可以通过编辑fstab文件，在文件中添加以下条目来实现：
    sudo nano /etc/fstab
添加这个条目:
    /swapfile          swap            swap    defaults        0 0

搞掂，打完收工！谢谢大家！

That's all. But not all.