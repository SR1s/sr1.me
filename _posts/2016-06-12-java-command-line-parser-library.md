# Java 命令解析库

## Apache Commons CLI 
Apache Commons CLI 是 Apache 下面的一个解析命令行输入的工具包，该工具包还提供了自动生成输出帮助文档的功能。

Apache Commons CLI 支持多种输入参数格式，主要支持的格式有以下几种：

POSIX（Portable Operating System Interface of Unix）中的参数形式，例如 tar -zxvf foo.tar.gz

GNU 中的长参数形式，例如 du --human-readable --max-depth=1

Java 命令中的参数形式，例如 java -Djava.net.useSystemProxies=true Foo

短杠参数带参数值的参数形式，例如 gcc -O2 foo.c

长杠参数不带参数值的形式，例如 ant – projecthelp

## CmdOption

CmdOption 是一个用于Java5应用程序的注解驱动的简单命令行参数解析工具包，你所需要做的就是简单配置对象，每个字段和方法通过注解来定义。

## args4j

args4j是一个小巧的Java开发库，可以方便地解析CUI（命令行接口）程序的输入参数或选项。args4j基于MIT协议开源，对其它项目没有任何依赖。
