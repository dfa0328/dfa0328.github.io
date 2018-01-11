---
title: webpack优化
date: 2018-01-11 18:44:45
tags: webpack
categories: Webpack
---

# 打包速度优化
babel是影响打包速度的原因之一

```
module: {
        loaders: [
	         { 
	            test: /\.js$/,
	            loader: "react-hot-loader!babel-loader",
	            //指定打包文件 
	            include: [path.join(process.cwd(), './src')],
	            // 非打包文件
	            exclude: /(node_modules|bower_components|static|test|build|configs)/
	         }
        ],
     
    },

```


减少使用stage
```
//优化前
{
  "presets": ["react", "es2015", "stage-2"]
}
//优化后
{
  "presets": ["react", "es2015"]
}
```




