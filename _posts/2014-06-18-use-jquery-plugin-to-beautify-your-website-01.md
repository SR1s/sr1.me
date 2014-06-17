---
title: 50个JQuery插件之1：Flickplate
category: way-to-explore
layout: post
---

# 新系列or新坑？
前几天刷微博，发现某公号发布了这篇文章[50个jQuery插件可将你的网站带到另外一个高度](http://www.lupaworld.com/article-239506.html)，今天终于抽空看了一下，里面推荐的插件真心高大上，用在网站上绝对能提升逼格。因此决定把这50个插件都试一遍，顺便开坑。BTW，这段时间已经挖了好几篇文章的坑，可惜一直没填上.

![笑哭](http://sr1-me.qiniudn.com/emotions/laugh2cry.jpg)。

# Flickerplate是什么？

Flickerplate是一个图片轮播插件，支持响应式和触摸手势，大小仅11kb，简单小巧实用。项目主页地址：[点我](http://getwebplate.com/plugins/flickerplate)

# 如何使用（我是勤劳的搬运工）

## 1. 下载Flickerplate插件，解压css、js文件夹到项目中

## 2. 在网页中引入以下几个文件：

	<!-- Flickerplate依赖的文件 -->
	<script type="text/javascript" src="js/jquery-v1.10.2.js"></script>
	<script type="text/javascript" src="js/jquery-finger-v0.1.0.js"></script>
	<script type="text/javascript" src="js/modernizr-custom-v2.7.1.js"></script>
	<!-- Flickerplate核心文件 -->
	<script type="text/javascript" src="js/flickerplate.js"></script>
	<link rel="stylesheet" type="text/css" href="css/flickerplate.css">

## 3. 在网页中依照以下格式创建HTML内容

	<div id="flicker-area">
		<ul>
			<li data-background="img/field.jpg">
				<div class="flick-title">First 1</div>
				<div class="flick-sub-text">First 2</div>
			</li>
			<li data-block-text="false" data-background="img/field.jpg">
				<div class="flick-title">Second 1</div>
				<div class="flick-sub-text">Second 2</div>
			</li>
			<li data-theme="dark" data-background="img/field.jpg">
				<div class="flick-title">Third 1</div>
				<div class="flick-sub-text">Third 2</div>
			</li>
		</ul>
	</div>

1. 每一个li元素就是一个轮播页；
2. data-background属性指定轮播页的背景图片；
3. data-block-text属性设置是否显示文字的背景色，默认值是true；
4. data-theme属性设置主题，有两个默认值可选，默认是light，此时左右两个箭头、文本文字的颜色是白色，文本的背景颜色是黑色，若设置为dark，则这两部分的内容的颜色会对调;
5. li元素中的子元素使用了两个css类，flick-title和flick-sub-text这两个类样式都使文本居中，文本的显示效果受data-theme和data-block-text的值影响。从语义上就能看出，，flick-title是大标题，flick-sub-text是副标题或正文，因此字号上前者会比后者大。

## 4. 让Flickerplate工作起来

前面仅仅是定义了HTML的内容，要让Flickerplate真正工作起来，还需要执行以下方法调用。

	<script type="text/javascript">
		$(document).ready(function(){
			$("#flicker-area").flicker();
		});
	</script>

上面这段代码使用JQuery的选择器，在页面加载完毕之后，指定相应的HTML内容（这里是id为flicker-area的区域），使Flickerplate在这个区域生效。

完成了以上几个步骤，就大功告成啦。是不是很简单呢！

# 进阶配置

To be continue!
