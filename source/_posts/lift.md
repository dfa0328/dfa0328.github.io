---
title: JS变量提升
date: 2019-05-08 17:57:31
tags: [JavaScript]
---
先上一段代码，你认为它的执行结果是什么？
```
a=2
var a;
console.log(a)  // ?
```
它的结果是2，那为什么是这样呢？这就是我们今天要说的内容。


在JavaScript中有一个很有趣的现象——**提升**，这里的提升可以分为两类`变量提升`和`函数提升`。只有搞明白它，我们才能正确的理解JavaScript的执行顺序。至于为什么会出现提升，这和JavaScript的编译原理有关，详情可以看 [这里](https://dfa0328.github.io/2018/03/28/compile)，今天这里只讲述他的提升规则。

## JS提升规则
> 1） 只有声明本身会提升，而赋值或其他逻辑会留在原地。也就是说提升并不会改变代码的执行顺序。
> 2） 每个作用域都会进行提升操作。

## 变量提升

上面的那个例子，运行起来其实是下面👇这样的，所以结果是2。
```
var a;
a=2;
console.log(a) //2 
```

再来个例子：
```
console.log(a);  // ?
var a=2;
```

上面的代码运行起来是👇这样的,所以它的结果是undefined。
```
var a;
console.log(a);
a=2;
```
看到这你就应该可以理解规则1所说的：**只有声明提升，而赋值或其他逻辑会留在原地**。而函数同样也遵循这一规则。

## 函数提升

先来一个例子：
```
foo();
function foo() {
  console.log( a );  // ?
}

```
上面的例子可以转化成下面的形式

```
function foo() {
  console.log( a );  
}
foo();
```
foo被提升了，所以foo()调用没有报错。结果是undefined。

再来一个例子：
```
foo(); // ?
bar(); // ?
var foo = function bar() {
  console.log('foo')
};
```
上面的代码可以转化下面的代码

```
var foo;
foo(); // TypeError
bar(); // ReferenceError
foo = function() {
  var bar = foo;
}
```

这个两个例子可以看出**函数声明会被提升，但是函数表达式却不会被提升**，其实函数表达式的本质是变量提升。

再来个例子：
```
foo();
function foo(){
	console.log(a);   //undefined
	a=4
	var a;
}

```
这个例子可以看出**每个作用域都会进行提升操作**。

## 函数提升+变量提升

**来一个进阶性例子**

```
foo(); // ?
function foo() {
  console.log( 1 );
}
var foo = function() {
  console.log( 2 );
};
function foo() {
  console.log( 3 );
}
foo(); // ?
```
上面的代码可以转化下面的代码

```
var foo;
function foo() {
  console.log( 1 );
}
function foo() {
  console.log( 3 );
}
foo(); // 3
var foo = function() {
  console.log( 2 );
};
foo(); //2
```
这里可以看出：
* 函数声明和变量声明都会被提升，但是**函数提升的优先级高于变量提升**。
* 尽管重复的 var 声明会被忽略掉，但出现在后面的函数声明还是可以覆盖前面的。所以要注意避免重复声明。


## 总结

> 1) 变量函数都提升，函数提升到最前，变量声明不覆盖（函数），变量赋值会覆盖（函数）。
> 2) 每个作用域都会进行提升操作。








