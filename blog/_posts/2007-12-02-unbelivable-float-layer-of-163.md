---
date: 2007-12-02 23:19:10
layout: post
title: 网易无敌的浮动层解决方案揭密
category: experience
tags:
- css
- 无js
- 浮动层
- 网易博客
---

网易博客一直被我当作一个值得学习的产品，除了过度使用ajax造成几乎无法被搜索引擎收录外，其中不管是架构思想还是技术上都还是有一些令人叹为精妙的地方的。

前几天发现了他登录框的弹出浮动层一个新的变化，在IE下拖动浏览器滚动条的时候，浮动层的居中效果已经不再会随滚动条微弱的抖动了。可以对比一下，显然新浪的博客却还没有找到解决方案，右下角的浮动小广告在滚动页面时仍然会抖动。由于IE并不支持css里position:fixed;的方法，所以我就开始一边拖他的登录框，一边思考他是如何实现浏览器滚动无抖动的。当我把登录框使劲往右侧一拖时，更另我惊奇的事情发生了，整个框居然盖住了浏览器右边的滚动条，感觉像是被拖到浏览器外面去了一样！

苦于在IE下看不到他ajax出来的源代码，于是换到FF，并打开强大的firebug，小样儿，还不快给爷现出原形！鼠标定位到登录框上，看到他的css里只有个position:absolute;是值得关注的，并没有使用FF下可以固定定位的fixed。继续找到登录框的父级容器，上面的css除了继承的*{margin:0;padding:0;}和body里的字体外，什么都没有了，而且在浏览器上下滚动的时候也不像用JS做的定位，于是更感到奇怪。再看与其同级的有一个id是blog-163-com-body的div，firebug指示其占有的区域是整个屏幕，而且css里第一行赫然的写着height:100%;！再点body看css，同样写了height:100%;。难道就是传说中的100%高度“大法”？果然html标签也是！但还是没想通什么页面里面的元素可以挡住滚动条，还特意测试了下html标签下appendChild，失败，于是又仔细看一遍css，终于发现了里面惊人的秘密：

html大致结构：

	<body>
	<div id="absbox">
		放置浮层的区域
	</div>
	<div id="mainbody">
		真正放置内容的区域
	</div>
	</body>

css主要实现：

	html{height:100%; overflow:hidden; border:0;}
	body{height:100%; overflow:hidden;}
	#blog-163-com-body.bggb{width:100%; height:100%; position:relative; overflow-y:scroll; overflow-x:auto;}

答案的关键就在于对容器的理解，和对溢出高度滚动条的控制，**通过一个普通的div模拟了整个浏览器的滚动条**，以使得可以让他同级的元素盖在他上面。分析之后，完成了一个[DEMO](/demo/float-layer/)，并真正发现了这样做的好处：

1. 实现了无JS相对窗口固定的浮动层
（主要针对IE不能使用position:fixed）
2. 让页面里的元素可以盖住浏览器的滚动条
（这样最大的好处就是在使用弹出层并添加半透明不可点的背景时不再需要根据页面的长度计算高度了，完全css控制，而且滚动条被遮挡后也直接限制了鼠标滚轮的操作）
3. 拥有了基本的“页面管理器”的思想
（我个人提出的一种管理页面里各种消息框，弹出层以及类似flash的深度管理的初步思想）

最后不得不感叹网易做前端这块的人真够变态！这么无敌的招数都能想出来，真是佩服，看来taobaoUED招聘的css题目也应该加上这一道：“如何实现无JS相对窗口固定的浮动层”，或者“如何让页面里的元素盖住浏览器的滚动条”，才算的上足够变态。

-EOF-