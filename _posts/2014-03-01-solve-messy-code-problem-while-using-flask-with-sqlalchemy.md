---
layout: post
title: 解决使用Flask-SQLAlchemy时，中文显示乱码的问题
category: 爬坑之路
---

# 问题描述

这两天在折腾Flask下的插件SQLAlchemy，在将原先写的一个小网站改用SQLAlchemy时，发现插入数据库的中文出现了乱码。很多语言、国外的库和框架以及插件在设计的时候并没有考虑到中文编码问题，有乱码是很常见的。因此按照平时处理乱码的思路开始解决问题。

# 操作环境

系统：LinuxMint Maya(一个基于Ubuntu的衍生版)

相关软件：Python + Flask + Flask-SQLAlchemy + MySQL

# 解决过程

初步判定是SQLAlchemy在链接数据库时使用的编码不正确导致，在网上使用相关关键词进行搜索，发现相似案例[Python+SQLAlchemy+MySQLdb+MySQL的中文乱码及其解决办法](http://biancheng.dnbcw.info/mysql/357779.html)，虽然不是使用Flask框架，但解决方式有可能是类似的。按照文中的方法，在数据库配置的URI末尾添加指定字符集的参数charset=utf8，删除数据库后重试，问题依旧。

沿着这个思路继续查资料，看到这篇[sqlalchemy中文问题解决方案](http://firefish.blog.51cto.com/298258/112794)，看完作者的分析(解决方法和上面的一样)，便学着去看Flask的文档，文档中提到可以通过设置SQLALCHEMY_NATIVE_UNICODE来禁止使用SQLAlchemy默认的Unicode编码。有可能是SQLAlchemy默认的Unicode编码不是UTF-8，抱着这样的想法，在程序中指定了`SQLALCHEMY_NATIVE_UNICODE=False`，执行程序，报错。

看来只能查看[Flask的源码](https://github.com/mitsuhiko/flask-sqlalchemy/blob/master/flask_sqlalchemy/__init__.py)了，似乎源码可以通过使用指定`use_native_unicode`为目标编码来指定编码方式，尝试将`db = SQLAlchemy(app)`改为`db = SQLAlchemy(app, use_native_unicode="utf8")`。这回虽然没报错，但还是乱码。

继续纠结。

某个时刻，突然想到有可能是建表的时候，没有指定字符集，使用的是数据库默认的字符集的导致的。继续找了一段时间的如何指定建表时使用字符集的方法，未果。

数据库该不会使用的不是UTF-8吧？抱着这个想法，进入数据库，输入`status`，在输出的信息上显示默认是latin-1。搞了半天，原来问题在这。

进入mysql的配置文件目录: `cd /etc/mysql/`，编辑my.cnf配置文件，在文件中的[mysqld]下面增加一行内容：`character_set_server = utf8`，在[client]和[mysql]下面分别增加一行内容：`default-charact-set = utf8`，重启MySQL的服务，设置就生效了。

通过测试，这时已经能够正常插入读取中文了。整个过错耗时3个多小时，好曲折的Debug之路！

# 总结

此次总结如下：数据库与应用程序链接出现乱码，有可能是数据库编码、数据库与程序之间的链接的编码、程序本身的编码三者不匹配导致的。处理乱码问题需要从这三方面入手。数据库建库时统一使用utf8编码，同时注意数据库默认编码的影响；数据库与程序之间的链接可以通过在url中设置charset=utf8来指定；程序本身采用utf8编码保存，对于出现中文的python程序，还要在文件开头指定编码`#coding=utf8`。

以上几个步骤下来来，就能跟乱码Say拜拜了。

---
That's all. But not ALL.
