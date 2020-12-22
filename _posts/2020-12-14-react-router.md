---
layout: post
title:  "二 引入react-router"
date:   2020-12-22 15:51:54
categories: problems
---
## 1.安装react-router-dom
创建base.tsx文件，设置一级路由和重定向

	 {
	    path: "/home",
	    component: Home,
	  },
	  {
	    path: "/login",
	    component: Login,
	  }
	  from: "/", to: "/home"
分别创建home和login文件，其中home文件下设置二级

	 {
	    path: "/home/newlead",
	    component: NewLead,
	  },
	  {
	    path: "/home/newlead2",
	    component: NewLead2,
	  }
	  from: "/home", to: "/home/newlead"

在打开“/home/newlead”时出现报错
> newlead:17 GET http://0.0.0.0:2047/home/static/js/main-bundle-33e41f3b.js net::ERR_ABORTED 404 (Not Found)
> 
> newlead:1 Refused to apply style from 'http://0.0.0.0:2047/home/main.css' because its MIME type ('text/html') is not a supported stylesheet MIME type, and strict MIME checking is enabled.

解决方案：

方案1: 将用的 BrowserRouter 改为 HashRouter 即可

如果路由设置为 /home，原来访问localhost:2047/home，在改成 HashRouter 之后，需要访问 localhost:2047/#/home。
  
方案2: 在webpack的output下增加publicPath.

	output: {
	    filename: 'static/js/[name]-bundle-[hash:8].js',
	    publicPath: '/'
	  },