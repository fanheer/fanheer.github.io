---
layout: post
title:  "四 每日学习"
date:   2021-3-18 19:19:54
categories: problems
---

###1. Array.filter(Boolean)

ECMAScirpt5 中 Array 类中的 filter 方法使用目的是移除所有的 ”false“ 类型元素 (false, null, undefined, 0, NaN or an empty string)。
	
Boolean 是一个函数，它会对遍历数组中的元素，并根据元素的真假类型，对应返回 true 或 false.

```javascript
var a=[1,2,"b",0,{},"",NaN,3,undefined,null,5];
var b=a.filter(Boolean); // [1,2,"b",{},3,5]
	
b = a.filter(Boolean);
等价于
b = a.filter(function (x) { return Boolean(x); });
```

###2. 正则表达式

`参考 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions`

(1) 两种方式创建

	正则表达式字面量：var re = /ab+c/;
	RegExp对象的构造函数：var re = new RegExp("ab+c");


###3. transition不支持display属性

* 想让display从none变为block，或者从block变为none时，实现这样的动画效果大多数人都会采用transition，但transition并不支持display。那么能想到的办法就是使用visibility属性实现从visible到hidden的转换。
* 但是visibility和display是有区别的，display隐藏的元素是脱离文档流的，它不在页面中占用空间；而visibility: hidden的元素会在页面上留白。所以还是display的用法好一点。
* 解决方案：
	* 监听onAnimationEnd

页面部分

```
<div class="overlay" onAnimationEnd={this.removeAnimateClass}>
	...
</div>
```
js部分

```javascript
removeAnimateClass = (): void => {
    if (this.overlay?.classList?.contains('overlay-leave')) {
      /* eslint-disable no-unused-expressions */
      this.overlay?.classList?.remove('tz-overlay--beforeleave')
    }
    // weapp不支持合并remove
    this.overlay?.classList?.remove('overlay-enter')
    this.overlay?.classList?.remove('overlay-leave')
}
```
css

```css
.overlay{
	 top: 0;
	  left: 0;
	  width: 100%;
	  height: 100%;
	  position: fixed;
	  background-color: rgba($color: #000, $alpha: 0.3);
	  display: none;
	&--active{
	    display: block;
	  }
	  &--beforeleave{
	    display: block;
	  }
}
.overlay-enter{
  animation: toShow .2s ease-in;
}
.overlay-leave{
  animation: toHide .2s .2s linear forwards;
}
@keyframes toShow {
  from{
    opacity: 0;
  }
  to{
    opacity: 1;
  }
}
@keyframes toHide {
  0%{
    opacity: 1;
  }
  100%{
    opacity: 0;
  }
}
```