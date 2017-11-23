---
title: Vue查询手册——过滤器
date: 2017-11-20 14:22:57
tags: Vue
categories: Vue API
---
过滤器，本质上都是函数。其作用在于用户输入数据后，它能够进行处理，并返回一个数据结果。使用管道符（ | ）进行连接。
Vue支持在任何出现表达式的地方添加过滤器。
过滤器可以接受参数，参数跟在过滤器名称后面，参数之间以空格分隔。

# 内置过滤器


## 字母操作
Vue 内置了`capitalize`、`uppercase`、`lowercase`三个过滤器用于处理英文字符。注：这三个过滤器仅针对英文字符串使用。

### capitalize
capitalize 过滤器用于将表达式中的首字母转换为大写形式。
```
{{ 'fvds' | capitalize }}
// 'fvds' => 'Fvds'
```

### uppercase
uppercase 过滤器用于将表达式中的所有字母转换为大写形式。
```
{{ 'fvds' | uppercase }}
// 'fvds' => 'FVDS'
```

### lowercase
lowercase 过滤器用于将表达式中的所有字母转换为小写形式。
```
{{ 'FVDS' | lowercase }}
// 'FVDS' => 'fvds'
```

## 限制
Vue 内置了`limitBy`、`filterBy`、`orderBy`三个过滤器用于处理并返回过滤后的数组。
> 注意：如与v-for搭配使用，这三个过滤器所处理的表达式的值必须是数组。

### limitBy
limitBy 过滤器的作用是限制数组为开始的前 N 个元素，其中 N 由传入的第一个参数指定。第二个参数可选，用于指定开始的偏移量，默认为0，即不偏移。
```
<!--只显示开始的 10 个元素-->
<div v-for="item in items | limitBy 10"></div>
<!--显示 5到15 个元素-->
<div v-for="item in items | limitBy 10 5"></div>
```

### filterBy
filterBy过滤器的使用比较灵活，其第一个参数可以是字符串或者函数。
过滤条件是：`'string || function'+in+'optionKeyName'`。
如果第一个参数是字符串，那么将在每个数组元素中搜索它，并返回包含该字符串的元素组成的数组。
```
<div v-for="item in items | filterBy 'hello"></div>
```
如果item是个对象，过滤器将递归地在它所有的属性中搜索。为了缩小搜索范围，可以指定一个搜索字段。
```
<div v-for="member in didFamily | filterBy 'ddfa' in 'name'" ></div>
```
也可以在多个字段中进行搜索，字段与字段之间以空格分隔。
```
<li v-for="user in users | filterBy 'Chris' in 'name' 'nickname'" ></li>
```
还可以将搜索字段存放在一个数组中，这样当修改搜索字段时只需要修改数组即可，无须在修改View层。
```
<!-- fields=['fieldA','fieldB'] -->
<div v-for="user in users | filterBy searchText in fields" ></div>
```

### orderBy
orderBy 过滤器的作用是返回排序后的数组。过滤条件是：`'string || array || function'+'order >= 0为升序 || order < 0为降序'`。第一个参数可以是字符串、数组或者函数。第二个参数order可选，决定结果为升序或降序排列，默认为1，即升序排列。
若输入参数为字符串，则可同时传入多个字符串作为排序键名，字符串之间以空格分隔。
```
<li v-for="user in users | orderBy 'lastName' in 'firstName' 'age'" >
{{user.lastName || user.firstName || user.age }}
</li>
```

## json
Vue中json 过滤器本质上是JSON.stringify()的精简缩略版，可将表达式的值转换为JSON字符串，即输出表达式经过JSON.stringify()处理后的结果。json 可接受一个类型为Number的参数，用于决定转换后的JSON字符串的缩进距离，如果不输入该参数，则默认为2。
```
<pre>{{didFamily | json 4}}</pre>
```

## currency
currency 过滤器的作用是将数字值转换为货币形式输出。其第一个参数接受类型为 String 的货币符号，如果不输入，则默认为美元符号$。第二个参数接受类型为Number的小数位，如果不输入，则默认为2.
> 注意:如果第一个参数采取默认形式，而需要第二个参数修改小数位，则第一个参数不可省略。
```
{{ amount | currency  }}
// 12345 => $12,345.00
{{ amount | currency  '￥'}}
// 12345 => ￥12,345.00
{{ amount | currency  '$' 3}}
// 12345 => $12,345.000
```

## debounce
debounce 过滤器的作用是延迟处理器一定的时间执行。其接受的表达式的值必须为函数，因此其一般与v-on等指令结合使用。debounce 接受一个可选的参数作为延迟时间，单位为毫秒。如果没有该参数，则默认的延迟时间为300毫秒。
```
<input @keyup="onKeyup | debounce 500" />
```

# 自定义过滤器
在Vue中存在一个全局函数 Vue.filter 用于构造过滤器。`Vue.filter(ID,function(){})`
该函数接受两个参数，其中第一个为过滤器ID，作为用户自定义过滤器的唯一标识；第二个参数则为具体的过滤器函数。过滤器函数以值为参数，返回转换后的值。

## 单个参数
```
Vue.filter('reverse', function (value) {
  return value.split('').reverse().join('')
})
<!-- 'abc' => 'cba' -->
<span v-text="message | reverse"></span>
```


## 多个参数
过滤器函数除了以值为参数外，还支持任意数量的参数，参数之间以空格分隔。
```
Vue.filter('wrap', function (value, begin, end) {
  return begin + value + end
})
<!-- 'hello' => 'before hello after' -->
<span v-text="message | wrap 'before' 'after'"></span>

```

## 双向过滤器
目前我们使用过滤器都是在把来自模型的值显示在视图之前转换它。不过也可以定义一个过滤器，在把来自视图（`<input> `元素）的值写回模型之前转化它，即双向过滤器。
```
Vue.filter('currencyDisplay', {
  // model -> view
  // 在更新 `<input>` 元素之前格式化值
  read: function(val) {
    return '$'+val.toFixed(2)
  },
  // view -> model
  // 在写回数据之前格式化值
  write: function(val, oldVal) {
    var number = +val.replace(/[^\d.]/g, '')
    return isNaN(number) ? 0 : parseFloat(number.toFixed(2))
  }
})
```

## 动态参数
如果过滤器参数没有用引号包起来，则它会在当前 vm 作用域内动态计算。另外，过滤器函数的 `this` 始终指向调用它的 vm。
```
<input v-model="userInput">
<span>{{msg | concat userInput}}</span>

Vue.filter('concat', function (value, input) {
  // `input` === `this.userInput`
  return value + input
})
```


> 注意: 1、需要给定过滤器一个唯一标识。2、过滤器函数的作用是输入表达式的值，经过处理后输出。

