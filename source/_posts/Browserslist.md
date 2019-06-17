---
title: 前端之Browserslist
date: 2019-03-21 15:50:08
tags: 配置
---

## Browserslist是什么？

[browserslist](https://github.com/browserslist/browserslist#readme)是一个开源项目，是在不同的前端工具之间共用目标浏览器和 node 版本的配置工具。       
它主要被以下工具使用：

* [Autoprefixer](https://github.com/postcss/autoprefixer)
* [Babel](https://github.com/babel/babel/tree/master/packages/babel-preset-env)
* [post-preset-env](https://github.com/csstools/postcss-preset-env)
* [eslint-plugin-compat](https://github.com/amilajack/eslint-plugin-compat)
* [stylelint-unsupported-browser-features](https://github.com/ismay/stylelint-no-unsupported-browser-features)
* [postcss-normalize](https://github.com/jonathantneal/postcss-normalize)
* [obsolete-webpack-plugin](https://github.com/ElemeFE/obsolete-webpack-plugin)

## 如何使用Browserslist？

**一种方式**是在 package.json 里面增加如下配置：
```
{
  "browserslist": [
    "last 1 version",
    "> 1%",
    "maintained node versions",
    "not dead"
  ]
}

```
**另一种方式**是在工程的根目录下存在`.browerslistrc`配置文件（内容如下）：
```
# 注释是这样写的，以#号开头
last 1 version
> 1%
maintained node versions
not dead

```
[browserslist全部实例](https://github.com/browserslist/browserslist-example)演示了上面列举的每个工具是如何使用 browserslist 的。
**使用了browserslist之后，所有的工具将自动的查找当前工程规划的目标浏览器范围。**

## 查询条件列表(常用)

详细看[官方文档](https://github.com/browserslist/browserslist#queries)

| 条件 | 说明 |
| ------ | ------ | 
| > 1% | 全球超过1%人使用的浏览器 |
| > 5% in US | 指定国家使用率覆盖 | 
| last 2 versions | 所有浏览器兼容到最后两个版本根据CanIUse.com追踪的版本 | 
| Firefox ESR | 火狐最新版本 | 
| Firefox > 20 | 指定浏览器的版本范围 | 
| not ie <=8 | 方向排除部分版本 | 
| Firefox 12.1 | 指定浏览器的兼容到指定版本 | 
| unreleased versions | 所有浏览器的beta测试版本 | 
| unreleased Chrome versions | 指定浏览器的测试版本 | 
| since 2013 | 2013年之后发布的所有版本 | 
| dead | 通过last 2 versions筛选的浏览器版本中，全球使用率低于0.5%并且官方声明不在维护或者事实上已经两年没有再更新的版本。 | 
| defaults | 默认配置> 0.5%, last 2 versions, Firefox ESR, not dead | 


## Debug(验证筛选后查询的结果)

直接在工程目录下运行`npx browserslist` 来查看你配置的筛选条件筛选出的浏览器版本范围

```
npx browserslist  //打印出所有浏览器版本支出情况
```



