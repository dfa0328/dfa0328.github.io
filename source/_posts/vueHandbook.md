---
title: Vue查询手册——指令
date: 2017-11-17 11:52:17
tags: Vue
categories: Vue API
---

# 指令
指令（Directive）是特殊的带有前缀的 v-的特性。指令的值限定为绑定表达式，指令的职责就是当其表达式的值改变时把某些特殊的行为应用到DOM上。

## 内部指令（基础指令）
Vue的基础指令有： `v-text`、`v-html`、`v-show`、`v-if`、`v-else`、 `v-else-if`、
`v-repeat`、`v-for`、`v-on`、`v-bind`、`v-model`、`v-pre`、`v-cloak`、`v-once`、`v-el`、`v-ref`等;

### v-text
**类型：** string
**用法：**更新元素的 textContent。如果要更新部分的 textContent ，需要使用\{ \{Mustache \} \}插值。
示例：    
```
<span v-text="msg"></span>
<!-- 效果和下面的一样 -->
<span>{{msg}}</span>
```

### v-html
**类型：** string
**用法：** 更新元素的 innerHTML 。内容按普通 HTML 插入——数据绑定被忽略。如果试图使用 v-html 组合模板,可以重新考虑是否通过使用组件来替代。
示例：   
```
<div v-html="html"></div>
<!-- 效果和下面的一样 -->
<div>{{{html}}}</div>
```

> **注意：不建议在网站上直接动态渲染任意HTML片段，很容易导致XSS攻击。只在可信的内容上使用v-html，永不用在用户提交的内容上。**

### v-show
**类型：** any
**用法：** 根据表达式的值来切换元素的 display CSS 属性显示或者隐藏HTML元素。当v-show 赋值为false时，元素将被隐藏。
示例：       
```
<ul id="demo" v-show="block">
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
</ul>

<script>
    var app=new Vue({
        el:'#demo',
        data:{
              block:true
        }
    });
</script>
```

### v-if
**类型：** any
**用法：** 根据表达式的值的真假条件渲染元素。在切换时元素及它的数据绑定 / 组件被销毁并重建。如果元素是 `<template> `，将提出它的内容作为条件块。当v-if 赋值为false，那么对应的元素就会从DOM中移除。
示例：         
```
<div id="app">
    <h1>Hello,Vue  js!!</h1>
    <h1 v-if='yes'>Yes!</h1>
    <h1 v-if='no'>No!</h1>
    <h1 v-if='age >=25'>Age:{{age}}</h1>
    <h1 v-if='name.indexOf("jack") >=0'>Name:{{name}}</h1>
    <template v-if="login">
		  <h1>Title</h1>
		  <p>Paragraph 1</p>
		  <p>Paragraph 2</p>
	</template>
</div>
<script type="text/javascript">
    var vm=new Vue({
        el:'#app',
            data:{
                yes:true,
                no:false,
                age:28,
                name:'abcdefg',
                login:true 
        }
    })
</script>
```
> **注意：v-if指令是根据条件表达式的值来执行元素的插入或者删除行为。**


### v-else
**用法：** 为 v-if 或者 v-else-if 添加 “else 块”。
示例：    
```
<div id="demo">
    <template v-if="login">欢迎来到这里学习</template>
    <template v-else="login">请登录</template>
</div>

<script>
    var app=new Vue({
        el:'#demo',
        data:{
            login:true
        }
    });
</script>
```

> **注意：v-else 元素必须紧跟在 v-if 或者 v-else-if 元素的后面——否则它将不会被识别。**


### v-else-if
**类型：** any
**限制：** 前一兄弟元素必须有 v-if 或 v-else-if。
**用法：** 表示 v-if 的 “else if 块”。可以链式调用。
示例：      
```
<ul id="list">
	<li v-if="type === 'A'">A</li>
	<li v-else-if="type === 'B'">B</li>
	<li v-else-if="type === 'C'">C</li>
	<li v-else>Not A/B/C</li>
</ul>
<script type="text/javascript">
	var app=new Vue({
		el:'#list',
		data:{
			type:'B'
		}
	});
</script>
```
### v-repeat
**用法：** 基于 ViewModel 上的对象数组渲染列表。对于数组中的每个对象，该指令将创建一个以该对象作为其 $data 对象的子 Vue 实例。这些子实例继承父实例的数据作用域，因此在重复的模板元素中你既可以访问子实例的属性，也可以访问父实例的属性。此外, 你还可以通过 $index 属性来获取当前实例对应的数组索引。也可以使用别名`v-repeat="user in users"`。 该指令1.0之后废弃，使用v-for    
示例：    
```
<ul id="demo">
  <li v-repeat="items" class="item-{{$index}}">
    {{$index}} - {{parentMsg}} {{childMsg}}
  </li>
</ul>
var demo = new Vue({
  el: '#demo',
  data: {
    parentMsg: 'Hello',
    items: [
      { childMsg: 'Foo' },
      { childMsg: 'Bar' }
    ]
  }
})
```
### v-for
**类型：** Array | Object | number | string
**用法：** 基于源数据重复渲染元素或模板块。我们也可以使用$index来呈现相对应的数组索引。v-for需要特殊的别名，形式为“item in items”（items是数据数组，item 是当前数组元素的别名）。
示例：

```
<div id="demo">
	<ol>
		<li v-for='value in arr'>
			{{value}} 
		</li>
	</ol>
</div>
<script type="text/javascript">
	new Vue({
		el: '#demo',
		data: {
		    arr:['apple','banana','orange','pear'],
		    json:{a:'张三',b:'李四',c:'王五'}
		}
	})
</script>
```


### v-on
**缩写：** @
**类型：** Function | Inline Statement
**参数：** event (required)
**修饰符：**
* `.stop` - 调用 event.stopPropagation()。
* `.prevent` - 调用 event.preventDefault()。
* `.capture` - 添加事件侦听器时使用 capture 模式。
* `.self` - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
* `.{keyCode | keyAlias}` - 只当事件是从特定键触发时才触发回调。
* `.native` - 监听组件根元素的原生事件。
* `.once` - 只触发一次回调。
* `.left` - (2.2.0) 只当点击鼠标左键时触发。
* `.right` - (2.2.0) 只当点击鼠标右键时触发。
* `.middle` - (2.2.0) 只当点击鼠标中键时触发。
* `.passive` - (2.3.0) 以 { passive: true } 模式添加侦听器

**用法：**
绑定事件监听器。事件类型由参数指定。表达式可以是一个方法的名字或一个内联语句，如果没有修饰符也可以省略。
用在普通元素上时，只能监听 原生 DOM 事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件。
在监听原生 DOM 事件时，方法以事件为唯一的参数。如果使用内联语句，语句可以访问一个 $event 属性,`v-on:click="handle('ok', $event)"`。
示例：     
```
<!-- 方法处理器 -->
<button v-on:click="doThis"></button>
<!-- 内联语句 -->
<button v-on:click="doThat('hello', $event)"></button>
<!-- 缩写 -->
<button @click="doThis"></button>
<!-- 停止冒泡 -->
<button @click.stop="doThis"></button>
<!-- 阻止默认行为 -->
<button @click.prevent="doThis"></button>
<!-- 阻止默认行为，没有表达式 -->
<form @submit.prevent></form>
<!--  串联修饰符 -->
<button @click.stop.prevent="doThis"></button>
<!-- 键修饰符，键别名 -->
<input @keyup.enter="onEnter">
<!-- 键修饰符，键代码 -->
<input @keyup.13="onEnter">
<!-- 点击回调只会触发一次 -->
<button v-on:click.once="doThis"></button>
<!-- 在子组件上监听自定义事件 -->
<my-component @my-event="handleThis"></my-component>
<!-- 内联语句 -->
<my-component @my-event="handleThis(123, $event)"></my-component>
<!-- 组件中的原生事件 -->
<my-component @click.native="onClick"></my-component>
```

### v-bind
**缩写：** `:`
**类型：** any (with argument) | Object (without argument)
**参数：** attrOrProp (optional)
**修饰符：**
* `.prop` - 被用于绑定 DOM 属性。
* `.once` - 单次绑定，只能用于prop绑定 。
* `.sync` - 双向绑定，只能用于prop绑定 。 语法糖，会扩展成一个更新父组件绑定值的 v-on 侦听器。
* `.camel` - 将绑定的特性名字转换回驼峰命名。只能用于普通HTML特性的绑定，通常用于绑定用驼峰命名的SVG特性，比如 viewBox。

**用法：**
动态地绑定一个或多个特性，或一个组件 prop 到表达式。
在绑定 class 或 style 特性时，支持其它类型的值，如数组或对象。可以通过下面的教程链接查看详情。
在绑定 prop 时，prop 必须在子组件中声明。可以用修饰符指定不同的绑定类型。
没有参数时，可以绑定到一个包含键值对的对象。注意此时 class 和 style 绑定不支持数组和对象。
示例：     
```
<!-- 绑定一个属性 -->
<img v-bind:src="imageSrc">
<!-- 缩写 -->
<img :src="imageSrc">
<!-- with inline string concatenation -->
<img :src="'/path/to/images/' + fileName">
<!-- class 绑定 -->
<div :class="{ red: isRed }"></div>
<div :class="[classA, classB]"></div>
<div :class="[classA, { classB: isB, classC: isC }]">
<!-- style 绑定 -->
<div :style="{ fontSize: size + 'px' }"></div>
<div :style="[styleObjectA, styleObjectB]"></div>
<!-- 绑定一个有属性的对象 -->
<div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>
<!-- 通过 prop 修饰符绑定 DOM 属性 -->
<div v-bind:text-content.prop="text"></div>
<!-- prop 绑定. “prop” 必须在 my-component 中声明。 -->
<my-component :prop="someThing"></my-component>
<!-- 双向 prop 绑定 -->
<my-component :prop.sync="someThing"></my-component>
<!-- 单次 prop 绑定 -->
<my-component :prop.once="someThing"></my-component>
<!-- camel 绑定 v-bind 属性名称驼峰化 -->
<svg :view-box.camel="viewBox"></svg>
<!-- XLink -->
<svg><a :xlink:special="foo"></a></svg>

```

### v-model
**类型：** 随表单控件类型不同而不同。
**修饰符：**
* `.lazy` - 取代 input 监听 change 事件
* `.number` - 输入字符串转为数字
* `.trim` - 输入首尾空格过滤

**用法：** v-model指令用来在input、select、text、checkbox、radio等表单控件或者组件上创建双向绑定。你可以用 v-model 指令在表单控件元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但 v-model 本质上不过是语法糖，它负责监听用户的输入事件以更新数据，并特别处理一些极端的例子。
示例：     
```
<!-- 文本 -->
<input v-model="message" placeholder="edit me">
<!-- 多行文本 -->
<textarea v-model="message" placeholder="add multiple lines"></textarea>
<!-- 复选框 多个勾选框，绑定到同一个数组-->
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<!-- 单选框-->
<input type="radio" id="one" value="One" v-model="picked">
<input type="radio" id="two" value="Two" v-model="picked">
<!-- 选择列表-->
<select v-model="selected">
	<option>A</option>
	<option>B</option>
	<option>C</option>
</select>
<!-- 选择列表（绑定到一个数组）-->
<select v-model="selected" multiple>
	<option>A</option>
	<option>B</option>
	<option>C</option>
</select>
<!-- .lazy-->
<input v-model.lazy="msg" >
<!-- .number-->
<input v-model.number="age" type="number">
<!-- .trim-->
<input v-model.trim="msg">
```

> **注意：v-model 并不关心表单控件初始化所生成的值。因为它会选择 Vue 实例数据来作为具体的值。
对于要求 IME （如中文、 日语、 韩语等） 的语言，你会发现那v-model不会在 ime 构成中得到更新。如果你也想实现更新，请使用 input事件。**

### v-pre
**用法：** 跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签。跳过大量没有指令的节点会加快编译。
示例：     
```
//以上代码，第一个span里的内容不会被编译，显示为{{message}},
//第二个span里的内容会被编译，显示为hello vue
<div id="demo">
	<span v-pre>{{message}}</span>
	<span>{{message}}</span>
</div>
<script type="text/javascript">
	var app=new Vue({
		el:'#demo',
		data:{
			message:'hello vue'
		}
	});
</script>
```

### v-cloak

**用法：** 这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕，否则在渲染页面时，有可能用户会先看到Mustache标签，然后看到编译后的数据。
示例：      
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<style type="text/css">
			[v-cloak] {
			  display: none;
			}
		</style>
		<script src="vue.js" ></script>
	</head>
	<body>
		<div id="demo">
			<span>{{message}}</span>
			<span v-cloak>{{message}}</span>
		</div>
		<script type="text/javascript">
			var app=new Vue({
				el:'#demo',
				data:{
					message:'hello vue'
				}
			});
		</script>
	</body>
</html>
```

### v-once
**用法：** 只渲染元素和组件一次。随后的重新渲染,元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。
示例：      
```
<div id="demo">
	<!-- 单个元素 -->
	<span v-once>This will never change: {{msg}}</span>
	<!-- 有子元素 -->
	<div v-once>
	  <h1>comment</h1>
	  <p>{{msg}}</p>
	</div>
</div>
<script type="text/javascript">
	var app=new Vue({
		el:'#demo',
		data:{
			msg:'hello vue'
		}
	});
</script>
```

### v-el
**用法：**  为DOM元素注册一个索引，方便通过所属实例的$els访问这个元素。可以赢v-el:some-el设置this.$els.someEl。
实例：     
```
<span v-el:msg>hello</span>
<span v-el:other-msg>world</span>
//通过this.$els获取相应的DOM元素。
this.$els.msg.textContent  //hello
this.$els.otherMsg.textContent   //world
```

### v-ref
**用法：** 在父组件上注册一个子组件的索引，便于直接访问。不需要表达式，必须提供参数id。可以通过父组件的$refs对象访问子组件。
当v-ref和v-for一起使用时，注册的值将是一个数组，包含所有的子组件，对应于绑定数组；如果v-for使用在一个对象上，注册的值将是一个对象，包含所有的子组件，对应于绑定对象。

> **注意：因为HTML不区分大小写，camelCase风格的名字比如 v-ref:someRef将全部转换为小写。可以用v-ref:some-ref 设置 this.$refs.someRef。**


## 自定义指令
Vue.js 允许你注册自定义指令，实质上是让你教 Vue 一些新技巧：怎样将数据的变化映射到 DOM 的行为。你可以使用 `Vue.directive(id, definition)` 的方法传入**指令 id** 和**定义对象**来注册一个全局自定义指令。定义对象需要提供一些钩子函数。
     
### 钩子函数（全部可选）
* `bind`： 只调用一次，在指令第一次绑定到元素上时调用。
* `update`： 在 bind 之后立即以初始值为参数第一次调用，之后每当绑定的值变化时调用，参数为新值与旧值。
* `unbind`：只调用一次，在指令从元素上解绑时调用。

示例：     
```
Vue.directive('my-directive', { 
	bind: function () { 
		// 做绑定的准备工作 
		// 比如添加事件监听器，或是其他只需要执行一次的复杂操作 
	}, 
	update: function (newValue, oldValue) { 
		// 根据获得的新值执行对应的更新 
		// 对于初始值也会被调用一次 
	}, 
	unbind: function () { 
		// 做清理工作 
		// 比如移除在 bind() 中添加的事件监听器 
	} 
});

//调用
<div v-my-directive="someValue"></div>
```

### 实例属性

* `el` - 指令绑定的元素
* `vm` - 拥有该指令的上下文 ViewModel
* `expression` - 指令的表达式，不包括参数和过滤器
* `arg` - 指令的参数
* `name` - 指令的名字，不包含前缀
* `modifiers` - 一个对象，包含指令的修饰符。 
* `descriptor` - 一个对象，包含指令的解析过程。
* `raw` - 未被解析的原始表达式

> **注意：这些属性是只读的，不要修改它们。你也可以给指令对象附加自定义的属性，但是注意不要覆盖已有的内部属性。**

示例：    
```
<div id="demo" v-demo="LightSlateGray : msg"></div>
Vue.directive('demo', {
  bind: function () {
    this.el.style.color = '#fff'
    this.el.style.backgroundColor = this.arg
  },
  update: function (value) {
    this.el.innerHTML =
      'name - '       + this.name + '<br>' +
      'raw - '        + this.raw + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - '   + this.arg + '<br>' +
      'value - '      + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
//结果
name - demo
raw - LightSlateGray：msg
expression - msg
argument - LightSlateGray
value - hello!

```

### 对象字面量
如果指令需要多个值，可以传入一个 JavaScript 对象字面量。记住，指令可以使用任意合法的 JavaScript 表达式
```
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
Vue.directive('demo', function (value) {
  console.log(value.color) // "white"
  console.log(value.text) // "hello!"
})
```

### 字面修饰符
当指令使用了字面修饰符，它的值将按普通字符串处理并传递给 update 方法。update 方法将只调用一次，因为普通字符串不能响应数据变化。
```
<div v-demo.literal="foo bar baz">
Vue.directive('demo', function (value) {
  console.log(value) // "foo bar baz"
})

```


### 元素指令
有时我们想以自定义元素的形式使用指令，而不是以特性的形式。这与 Angular 的 “E” 指令非常相似。元素指令可以看做是一个轻量组件。
```
Vue.elementDirective('my-directive', {
  // API 同普通指令
  bind: function () {
    // 操作 this.el...
  }
})

//使用
<my-directive></my-directive>
```

元素指令不能接受参数或表达式，但是它可以读取元素的特性从而决定它的行为。
> 迥异于普通指令，元素指令是终结性的，这意味着，一旦 Vue 遇到一个元素指令，它将跳过该元素及其子元素——只有该元素指令本身可以操作该元素及其子元素。


### 高级选项

#### params
自定义指令可以接收一个 params 数组，指定一个特性列表，Vue 编译器将自动提取绑定元素的这些特性
```
<div v-example a="hi"></div>

Vue.directive('example', {
  params: ['a'],
  bind: function () {
    console.log(this.params.a) // -> "hi"
  }
})
```

此 API 也支持动态属性。this.params[key] 会自动保持更新。另外，可以指定一个回调，在值变化时调用：
```
<div v-example v-bind:a="someValue"></div>

Vue.directive('example', {
  params: ['a'],
  paramWatchers: {
    a: function (val, oldVal) {
      console.log('a changed!')
    }
  }
})
```

#### deep
如果自定义指令用在一个对象上，当对象内部属性变化时要触发 update，则在指令定义对象中指定 deep: true。
```
<div v-my-directive="obj"></div>

Vue.directive('my-directive', {
  deep: true,
  update: function (obj) {
    // 在 `obj` 的嵌套属性变化时调用
  }
})
```

#### twoWay 
如果指令想向 Vue 实例写回数据，则在指令定义对象中指定 twoWay: true 。该选项允许在指令中使用 this.set(value)
```
Vue.directive('example', {
  twoWay: true,
  bind: function () {
    this.handler = function () {
      // 将数据写回 vm
      // 如果指令这样绑定 v-example="a.b.c"
      // 它将用给定值设置 `vm.a.b.c`	  
      this.set(this.el.value)
    }.bind(this)
    this.el.addEventListener('input', this.handler)
  },
  unbind: function () {
    this.el.removeEventListener('input', this.handler)
  }
})
```

#### acceptStatement
传入 acceptStatement:true 可以让自定义指令接受内联语句，就像 v-on 那样。
```
<div v-my-directive="a++"></div>

Vue.directive('my-directive', {
  acceptStatement: true,
  update: function (fn) {
    // 传入值是一个函数
    // 在调用它时将在所属实例作用域内计算 "a++" 语句
  }
})
```

#### priority
可以给指令指定一个优先级（默认是 1000）。同一个元素上优先级高的指令会比其它指令处理得早一些。优先级一样的指令按照它在元素特性列表中出现的顺序依次处理，但是不能保证这个顺序在不同的浏览器中是一致的。
另外，流程控制指令 v-if 和 v-for 在编译过程中始终拥有最高的优先级。















