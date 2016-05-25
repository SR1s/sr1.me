---
title: 当我们谈Android电量优化的时候我们谈什么
category: think-when-god-laugh
layout: post
tag: 爬坑之路
---

解决用户的痛点，让用户用得爽，这是应用存在的核心要义；优秀的应用在这个基础上，还要让用户用得顺畅，这也是应用优化的目标；在这以上两点之外，用户还需要用得持久，这样的应用才是对用户友好的应用。前两点已经谈得足够多了，电量这块的优化，却鲜有提及。

在电量优化这一命题折腾了一周+之后，针对这块我觉得应该好好聊聊，当我们谈电量优化的时候，我们在谈什么。

# 少年，你的WakeLock掉了



# WakeLock之外我们看什么
根据官方的指引，我们能做的是执行以下命令，然后运行指定场景，进行测试，导出bugreport，通过Battery Historian进行分析。

```shell
# 重置电量信息
adb shell dumpsys batterystats --reset

# 让系统记录所有的的WakeLock信息
adb shell dumpsys batterystats --enable full-wake-history

# 测试完成后，导出bugreport
adb bugreport > name_of_bugreport.txt
```
