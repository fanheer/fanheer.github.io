---
layout: post
title:  "四 每日学习"
date:   2021-3-18 19:19:54
categories: problems
---

1. Array.filter(Boolean)

ECMAScirpt5 中 Array 类中的 filter 方法使用目的是移除所有的 ”false“ 类型元素 (false, null, undefined, 0, NaN or an empty string)。

Boolean 是一个函数，它会对遍历数组中的元素，并根据元素的真假类型，对应返回 true 或 false.

	var a=[1,2,"b",0,{},"",NaN,3,undefined,null,5];
	var b=a.filter(Boolean); // [1,2,"b",{},3,5]
	
	
	b = a.filter(Boolean);
	等价于
	b = a.filter(function (x) { return Boolean(x); });