---
title: webpack常用配置
date: 2017-06-11 16:42:19
tags: Webpack
categories: Webpack
---
# 单页面
单个入口和单个出口
```
var config = {
	entry:{
		page:'./src/a.js'	
	}, 
	output:{
		path:'./dist',
		filename:'js/[name].js',
		publicPath: "/" //线上文件的文件名
	},
	
}
module.exports=config;
```


# 多页面
多个文件多个出口
```
var config = {
	entry:{
		page1:'./src/a.js',
		page2:'./src/b.js',	
		page3:'./src/c.js'	
	}, 
	output:{
		path:'./dist',
		filename:'js/[name].js',
		publicPath: "/" //线上文件的文件名
	},
	
}
module.exports=config;

```

# 自动化生成页面中 html 文件
webpack 是没有这个功能的，所以需要引入插件`html-webpack-plugin`;
以多页面应用为例：（ 同一模板分别插入不同js文件导出到不同html文件 ）

```
const HtmlWebpackPlugin = require('html-webpack-plugin');
var config = {
	entry:{
		a:'./src/a.js',
		b:'./src/b.js',	
		c:'./src/c.js'	
	}, 
	output:{
		path:'./dist',
		filename:'js/[name].js',
		publicPath: "/" //线上文件的文件名
	},
	plugins:[
 		new HtmlWebpackPlugin({
 			title: 'this is a', //html文件title
			filename: 'a.html', //生成文件名
			template: './src/index.template.html',//模板文件
			inject: 'body', //js插入位置
			excludeChunks:['b','c'],//引入除了这些文件外的所有文件
 		}),
 		new HtmlWebpackPlugin({
 			title: 'this is b',  //html文件title
			filename: 'b.html', //生成文件名
			template: './src/index.template.html',//模板文件
			inject: 'body', //js插入位置
			excludeChunks:['a','c'],//引入除了这些文件外的所有文件
 		}),
 		new HtmlWebpackPlugin({
 			title: 'this is c',  //html文件title
			filename: 'c.html', //生成文件名
			template: './src/index.template.html',//模板文件
			inject: 'body', //js插入位置
			excludeChunks:['a','b'],//引入除了这些文件外的所有文件
 		})
   	],
	
}
module.exports=config;

```

修改`html`文件的title需要在template文件中做以下修改：
```
<title><%= htmlWebpackPlugin.options.title %></title>
```

# 热更新

方法一：最简单的方式（修改 webpack-dev-server 配置）

```
var config = {
	entry:{
		page:'./src/a.js'	
	}, 
	output:{
		path:'./dist',
		filename:'js/[name].js',
		publicPath: "/" //线上文件的文件名
	},
	//webpack-dev-server配置
	devServer: {
		contentBase: './dist',
		//默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录
		port: 8082,//设置默认监听端口，如果省略，默认为"8080"
		open:'http://localhost:8082', //启动服务自动打开浏览器
		historyApiFallback:true //是否热更新
	}
}
module.exports=config;
 
```

方法二：

1、将代码内联到入口js文件里 
```
entry:['webpack/hot/dev-server',__dirname+'/src/main.js']
```

2、启动代码热替换的plugins
```
plugins:[
	webpack.HotModuleReplacementPlugin()
]
```




















