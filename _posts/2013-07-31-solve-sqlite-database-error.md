---
title: 问题解决：SQLite：DatabaseError : attempt to write a readonly database
category: way-to-explore
layout: post
---

# 环境

LinuxMint + Apache + Mod_Wsgi + Python + Django + SQLite3

# 问题描述

在Django项目中启用admin，使用Django自带服务器能正常访问admin应用，使用Apache时，提示：DatabaseError at /admin/ attempt to write a readonly database

# 解决方法

将数据库文件（假设文件名为django.db）和数据库（假设文件夹名为database/）所在文件夹所属用户更改为www-data，命令如下（bash当前目录为database/文件夹的父文件夹）：

    chown www-data database/
    chown www-data database/django.db

# 原因分析

apache服务是由系统用户www-data启动的，它的权限和www-data一致，而数据库文件和数据库所在文件夹是由我们的用户创建的，默认对其他用户和用户组开放读取权限，不开放写入权限，因此apache无法修改数据库中的数据，抛出此错误。网上关于此问题还有另外一种解决方法：为数据库文件和数据库所在文件夹更改权限设置，将其设置为777。这种方式不推荐，**为任何文件和文件夹赋予全权限(777)将带来潜在的安全隐患，在任何情况下，都应该避免使用全权限。**

关于www-data用户：在Debian/Ubuntu上，www-data是默认运行web服务的用户/组，一般在通过apt安装web服务程序时生成。搭建web服务的文件夹/文件一般要设置成www-data的。也可以不用www-data，自己建一个新的用户和组，然后对apache/ngnix/lighttpd等web服务程序进行配置。不过这样比较麻烦。**如果web服务程序是自己编译的，不会生成www-data用户/组，需要自己配置。**

# 参考资料

1. [SQlite problems: attempt to write a readonly database][1]
1. [www-data用户是干什么的？和WEB服务有什么关系？][2]

 [1]: http://www.linuxquestions.org/questions/linux-server-73/sqlite-problems-attempt-to-write-a-readonly-database-611727/
 [2]: http://forum.ubuntu.org.cn/viewtopic.php?t=263778
