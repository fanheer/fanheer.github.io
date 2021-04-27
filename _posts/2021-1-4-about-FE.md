---
layout: post
title:  "三 前端冷知识"
date:   2021-1-4 15:51:54
categories: 每日学习
---

1. console.log恶搞

	Chrome的console.log是支持对文字添加样式的，甚至log图片都可以。于是可以重写掉默认的log方法，把将要log的文字应用到CSS的模糊效果，这样当有人试图调用console.log()的时候，出来的是模糊不清的文字。
	
		var _log = console.log;	
		console.log = function() {
		  _log.call(console, '%c' + [].slice.call(arguments).join(' '), 'color:transparent;text-shadow:0 0 2px rgba(0,0,0,.5);');
		};


2. 浏览器可编辑
在控制台执行以下代码，可以将整个页面变得可以编辑。

		document.body.contentEditable='true';
		// 或
		document.designMode='on'