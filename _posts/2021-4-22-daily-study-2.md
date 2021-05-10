---
layout: post
title:  "五 每日学习(2)(let和const,less和sass)"
date:   2021-4-22 14:21:03
categories: 每日学习
---

### 1. let（理解闭包）和const命令

  es6引入let前js中没有块级作用域。

  var声明的变量由于不存在块级作用域所以可以在全局环境中调用，而let声明的变量由于存在块级作用域所以不能在全局环境中调用。

  理解闭包，案例：
```javascript
    var a=[];
        for(var i=0;i<10;i++){ // i是全局变量 会影响后面的i
            a[i]=function(){
                console.log(i);
            };
        }
    a[6]();    //10    

    var a=[];
    for(let i=0;i<10;i++){// i是局部变量 会影响后面的i
        a[i]=function(){
            console.log(i);
        };
    }
    a[6]();    //6  
```

### 2. less和sass

Sass和Less都属于CSS预处理器。

CSS 预处理器定义了一种新的语言，其基本思想是，用一种专门的编程语言，为 CSS 增加了一些编程的特性，将 CSS 作为目标生成文件，然后开发者就只要使用这种语言进行CSS的编码工作。

转化成通俗易懂的话来说就是“用一种专门的编程语言，进行 Web 页面样式设计，再通过编译器转化为正常的 CSS 文件，以供项目使用”。

https://www.cnblogs.com/pink-chen/p/10727915.html