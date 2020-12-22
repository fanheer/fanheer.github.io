---
layout: post
title:  "一 搭建react+antd+ts项目"
date:   2020-12-14 15:14:54
categories: problems
---
>通过手动搭建，学习webpack的配置。

## 1.创建项目并安装依赖

	mkdir my-project
	cd my-project
	npm init
	
安装React框架以及Antd

	npm install react react-dom antd --save

安装 React 类型库 (帮助 Typescript 识别 React 库中定义的类型)

	npm install @types/react @types/react-dom --save-dev
	
安装 Typescript 和 ts-loader

	// 所有带有`.ts`或`.tsx`扩展名的文件都将由`ts-loader`处理
	npm install typescript ts-loader --save-dev
	
安装 Webpack

	// webpack4把webpack以及webpack-cli分离，以便更好的维护
	npm install webpack webpack-cli webpack-dev-server webpack-merge --save-dev
	
安装 Webpack 插件

	npm install source-map-loader html-webpack-plugin clean-webpack-plugin uglifyjs-webpack-plugin cross-env --save-dev
	// Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码
	// html-webpack-plugin为html文件中引入的外部资源如script、link动态添加每次compile后的hash，防止引用缓存的外部文件问题
	可以生成创建html入口文件，比如单页面可以生成一个html文件入口，配置N个html-webpack-plugin可以生成N个页面入口
	// clean 先把 build或dist (就是放生产环境用的文件) 目录里的文件先清除干净，再生成新的。
	// uglifyjs压缩js
	// cross 跨平台设置NODE_ENV的问题
	
## 2.配置 webpack
定义配置中所需要用到的文件路径./config/paths.js

	const path = require('path')
	const fs = require('fs')
	
	const appDirectory = fs.realpathSync(process.cwd())
	const resolveApp = relativePath => path.resolve(appDirectory, relativePath)
	
	module.exports = {
	  appBuild: resolveApp('build'),
	  appPublic: resolveApp('public'),
	  appIndex: resolveApp('src/index.tsx'),
	  appHtml: resolveApp('public/index.html'),
	}

定义公用的 webpack 配置

	在此目录建立./config/webpack/webpack.config.common.js
	const HtmlWebpackPlugin = require('html-webpack-plugin')
	const paths = require('../paths')
	module.exports = {
	  resolve: {
	    extensions: ['.tsx', '.ts', '.js', '.json']
	  },
	  devtool: 'source-map',
	  module: {
	    rules: [
	      {
	        enforce: "pre",
	        test: /\.js$/,
	        loader: "source-map-loader"
	      },
	      {
	        test: /\.(ts|tsx)$/,
	        loader: "ts-loader",
	      },
	    ],
	  },
	  plugins: [
	  <!--创建一个在内存中生成的html插件-->
	    new HtmlWebpackPlugin({
	      title: 'React + Typescript Project',
	    
	      template: paths.appHtml,
	      inject: false,
	    }),
	  ],
	}

定义 development 环境 webpack 配置./config/webpack/webpack.config.dev.js
	
	const webpack = require('webpack')
	const merge = require('webpack-merge')
	
	const paths = require('../paths')
	const commonConfig = require('./webpack.config.common')
	
	module.exports = merge.smart(commonConfig, {
	  mode: 'development',
	  entry: paths.appIndex,
	  output: {
	    filename: 'static/js/[name]-bundle-[hash:8].js',
	  },
	  plugins: [
	    new webpack.HotModuleReplacementPlugin(),
	    new webpack.NamedModulesPlugin()
	  ],
	  devServer: {
	    // 开启前端路径回路映射，子路径映射到根路径，由前端路由框架来解析
	    historyApiFallback: true,
	    // 关闭 Host 检查，同网段其他设备，可通过内网 IP 访问本机服务（需要配合 host: '0.0.0.0'）使用
	    disableHostCheck: true,
	    host: '0.0.0.0',
	    port: '8000',
	    inline: true,
	    hot: true,
	    // 请求代理服务
	    proxy: {
	      '/api': {
	        // 这里改为项目后端 API 接口 Host
	        target: 'http://backend.api.host',
	        // 支持跨域调用
	        changeOrigin: true,
	      }
	    }
	  }
	})

定义 production 环境 webpack 配置./config/webpack/webpack.config.prod.js

	const merge = require('webpack-merge')
	const CleanWebpackPlugin = require('clean-webpack-plugin')
	const UglifyJsPlugin = require("uglifyjs-webpack-plugin");
	
	const paths = require('../paths')
	const commonConfig = require('./webpack.config.common')
	
	module.exports = merge.smart(commonConfig, {
	  mode: 'production',
	  entry: paths.appIndex,
	  output: {
	    path: paths.appBuild,
	    filename: 'static/js/[name]-[hash:8].js',
	  },
	  optimization: {
	    minimizer: [
	      // 压缩 js
	      new UglifyJsPlugin({
	        cache: true,
	        parallel: true,
	        sourceMap: true
	      }),
	    ],
	    splitChunks: {
	      // 切割代码块，提取为独立的 chunk 文件
	      chunks: 'all',
	    },
	  },
	  plugins: [
	    // 每次编译之前，清空上一次编译的文件
	    new CleanWebpackPlugin([paths.appBuild], {
	      root: process.cwd()
	    }),
	  ],
	})
	
## 3.配置 tsconfig
./tsconfig.json

其中allowSyntheticDefaultImports: 允许合成默认导出

	{
	  "compilerOptions": {
	    "allowSyntheticDefaultImports": true, 
	    "jsx": "react",
	    "lib": ["es6", "dom"],
	    "module": "esnext",
	    "moduleResolution": "node",
	    "noImplicitAny": true,
	    "rootDir": "src",
	    "sourceMap": true,
	    "strict": true,
	    "target": "es5"
	  },
	  "exclude": [
	    "node_modules",
	    "build"
	  ]
	}
	
## 4.创建入口文件
./public/index.html

	<!DOCTYPE html>
	<html lang="en">
	  <head>
	    <meta charset="utf-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	    <title><%= htmlWebpackPlugin.options.title %></title>
	    <% for (let css in htmlWebpackPlugin.files.css) { %>
	      <link href="/<%=htmlWebpackPlugin.files.css[css] %>" rel="stylesheet">
	    <% } %>
	  </head>
	  <body>
	    <noscript>
	      You need to enable JavaScript to run this app.
	    </noscript>
	    <div id="root"></div>
	    <% for (let chunk in htmlWebpackPlugin.files.chunks) { %>
	      <script type="text/javascript" src="/<%=htmlWebpackPlugin.files.chunks[chunk].entry %>"></script>
	    <% } %>
	  </body>
	</html>

./src/index.tsx

	import React from 'react'
	import ReactDOM from 'react-dom'
	
	ReactDOM.render(
	  <div>Hello React</div>,
	  document.getElementById('root'),
	)

## 5.配置 package 启动脚本
./package.json

	{
	  ...,
	  "scripts": {
	    "start": "cross-env NODE_ENV=development webpack-dev-server --open --colors --config config/webpack/webpack.config.dev.js",
	    "build": "cross-env NODE_ENV=production webpack --progress --colors --config config/webpack/webpack.config.prod.js"
	  },
	  ...
	}
	
## 6.使用npm-build
由于clean-webpack-plugin已经升级的关系，所有配置方面我们需要修改一下,打开webpack.config.prod.js


	plugins: [
	    // 每次编译之前，清空上一次编译的文件
	    new CleanWebpackPlugin([paths.appBuild], {
	       root: process.cwd()
	    })
	  ]
	  改为
	 plugins: [
	    new HtmlWebpackPlugin()
	 ]
	 
## 7.启动和打包

	npm start 
	npm run build
	

## ```遇到的问题```

#### 问题1：webpack版本导致项目无法运行。

解决：当前配置可运行版本如下：

	"webpack": "^4.41.3",
	"webpack-cli": "^3.3.10",
	"webpack-dev-server": "^3.9.0",
	"webpack-merge": "^4.2.0"
	
#### 问题2：页面空白 noscript

解决：config/webpack/webpack.config.common文件下inject字段值应改为true。

	  plugins: [
	    new HtmlWebpackPlugin({
	      title: 'Lunchbox Demo',
	      template: paths.appHtml,
	      inject: true,
	    }),
	  ],

```HtmlWebpackPlugin```简化了HTML文件的创建，以便为你的webpack包提供服务。这对于在文件名中包含每次会随着编译而发生变化哈希的 webpack bundle 尤其有用。 你可以让插件为你生成一个HTML文件，使用lodash模板提供你自己的模板，或使用你自己的loader。

属性```inject: true | 'head' | 'body' | false ```
注入所有的资源到特定的 template 或者 templateContent 中，如果设置为 true 或者 body，所有的 javascript 资源将被放置到 body 元素的底部，'head' 将放置到 head 元素中。