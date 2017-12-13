---
title: Vue生命周期
date: 2017-11-23 15:29:46
tags: Vue
categories: Vue 
---

Vue 生命周期钩子有`beforeCreate`、`created`、`beforeMount`、`mounted`、`beforeUpdate`、`updated`、`activated`、`deactivated`、`beforeDestroy`、`destroyed`、`errorCaptured`。
所有的生命周期钩子自动绑定`this`上下文到实例中，因此你可以访问数据，对属性和方法进行运算。这意味着 **你不能使用箭头函数来定义一个生命周期方法**。

<img src="vueComponent/vue.png" width="600" style="border:none" >
## beforeCreate
**在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。**注意是之前，此时data、watcher、methods统统滴没有。这个时候的vue实例还什么都没有，但是$route对象是存在的，可以根据路由信息进行重定向之类的操作。

## created
**在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，`$el`属性目前不可见。**此时 this.$data 可以访问，`watcher`、`events`、`methods`也出现了，若根据后台接口动态改变data和methods的场景下，可以使用。

## beforeMount
**在挂载开始之前被调用：相关的`render`函数首次被调用。该钩子在服务器端渲染期间不被调用。**但是render正在执行中，此时DOM还是无法操作的。相比于created生命周期，此时只是多了一个`$el`的属性，然而其值为`undefined`。使用场景我上文已经提到了，页面渲染时所需要的数据，应尽量在这之前完成赋值。

## mounted
**`el`被新创建的`vm.$el`替换，并挂载到实例上去之后调用该钩子。如果`root`实例挂载了一个文档内元素，当`mounted`被调用时`vm.$el`也在文档内。**




















