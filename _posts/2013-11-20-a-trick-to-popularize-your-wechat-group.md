---
title: 点击就能加入微信群(推广微信群的小技巧)
category: way-to-explore
layout: post
---

# 缘起

昨天偶然发现深大通这个微信公众号(微信号：szutong)在光棍节那天发起了一个微信活动。回复关键字“光棍节快乐”，它会给你发如下一条图文消息：

![一条特殊的消息][1]

点击第二第三行文字，将会跳转到加入微信群的页面。

![加入微信群页面][2]

第一次看到这个效果，感觉好奇特！没想到微信还能这么玩！想了下，发现方法其实很简单，说出来你们就不会崇拜我了，那就先不说了。

# 实现

慢着！那位同学请放下你手上那块又红又方的东西，咱们有话好说～

原理是这样的：一般我们通过两种方式加入微信群：

1.  已经在微信群里的人邀请你加入
2.  主动扫描群二维码加群

微信群在推广的时候使用的就是第二种方式——扫描二维码。奥秘就在二维码里。二维码是一种信息编码方式，这几年在移动设备上非常流行。既然是用来编码信息的，那微信扫一扫就是通过对二维码进行解码提取出信息，然后根据信息进行相应操作。

来看一下群二维码里有藏有什么东东：(为了测试我建了一个微信群，欢迎加入～)

![测试用群暨技术联盟交流群][3]

上面是我建立的微信群的二维码。我们使用一款非微信的二维码扫描软件(比如：二维码扫描)扫一下，就会得到这个地址就得到下面这个地址：

    http://weixin.qq.com/g/7jI0VeX8FuptdqEOrW6h

看到这里，聪明的同学应该懂了点什么吧？没错，微信就是通过藏在二维码中的这个链接实现用二维码推广微信群的功能(如果你扫一下自己的微信二维码名片也能得到一个链接)。

我们把链接发给朋友试试…

打开这个链接就会是这样：

![打开链接看到这个东西][4]

点击加入该群聊就进去啦。所以，我们利用这个链接就可以不用扫描二维码就能让用户加入我们的微信群咯(同理，公众帐号之间互相推荐也可以通过这种方式方便地加粉～)

# 说点别的

通过直接发送消息发送一个链接，非常丑陋，我们可以利用HTML中的&lt;a>链接标签来让它变得好看一点，比如：

    <a href=‘http://weixin.qq.com/g/7jI0VeX8FuptdqEOrW6h’>点击我加入技术联盟微信交流群</a>

把引号之间的链接换成自己的链接就Ok了。在测试的时候，发现使用双引号会导致文字显示不出来，因此链接也看不到了，改用单引号就没问题，目测是微信后台的一个bug，怎么利用我什么都不知道～

还有就是，利用微信编辑模式发送图文消息必须要有一张图片，要实现深大通的效果可以用开发者模式绕过它的限制。

最后还有一点也是最重要的一点：分享出这个群二维码的人必须一直在群里，如果这个人退出了微信群，那这个二维码就失效了。(没看到最后的人我不告诉Ta～)

关于二维码的具体的生成细节可以看看陈皓老师的[二维码的生成细节和原理][5]，如果仅仅想要生成自己的个性二维码，百度一下“[二维码生成][6]”，就能找到一大票应用啦。

 [1]: http://sr1-me.qiniudn.com/131120/01.jpg
 [2]: http://sr1-me.qiniudn.com/131120/02.jpg
 [3]: http://sr1-me.qiniudn.com/131120/03.jpg
 [4]: http://sr1-me.qiniudn.com/131120/04.jpg
 [5]: http://coolshell.cn/articles/10590.html
 [6]: http://www.baidu.com/s?wd=%B6%FE%CE%AC%C2%EB%C9%FA%B3%C9
