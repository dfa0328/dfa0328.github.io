---
title: JS 模块机制
tags: JavaScript
categories: JavaScript 
---
在`ES6`之前，JavaScript中并没有在语言标准中提供模块定义规范，这对开发规模较大、较复杂的应用造成一定的影响。而在非语言层面，一些开源社区制定了模块定义规范，主要有`CommonJS`（服务器）、`AMD`（浏览器）和`CMD`。在`ES6`中，定义了`import`和`export`两种语法声明，从而在语言面实现了模块功能。

# ECMAScript 6之前的模块化
* CommonJS
* AMD
* CMD

**前景**：   
在JavaScript的发展历程中，它主要在浏览器前端发光发热。由于官方规范（ECMAScript）规范化的时间较早，规范涵盖范畴非常小。随着Web2.0的推进，JavaScript规范的缺陷就显露出来了。
 * **没有模块系统**
 * **标准库较少** ECMAScript仅定义了部分核心库，对于文件系统，I/O流等常见需求却没有标准的API。 
 * **没有标准接口** 在JavaScript中，几乎没有定义过如Web服务器或者数据库之类的标准统一接口
 * **缺乏包管理系统** 这导致JavaScript应用中基本没有自动加载和安装依赖的能力。

## CommomJS规范

CommonJS规范的提出，主要是为了弥补当前JavaScript没有标准的缺陷。它希望js可以在任何地方运行，不只是浏览器中。CommonJS规范涵盖了`模块`、`二进制`、`Buffer`、`字符集编码`、`I/O流`、`进程环境`、`文件系统`、`套接字`、`单元测试`、`Web服务器网关接口`、`包管理`等。这里我们只阐述CommomJS模块规范。

### CommomJS的模块规范
CommonJS对模块的定义十分简单，主要分为`模块引用`、`模块定义`和`模块标识`3个部分。

* #### 模块引用
在CommonJS规范中，存在`require()`方法，这个方法接受模块标识，以此引入一个模块的API到当前上下文中。require命令的基本功能是，读入并执行一个JavaScript文件，然后返回`module.exports`对象。如果没有发现指定模块，会报错。
模块引用的示例代码如下

	```
	var main=require('main');
	//等同于
	var main=require('main.js');
	```

* #### 模块定义
在模块中，上下文提供`require()`方法来引入外部模块。对应引入的功能，上下文提供了`exports`对象用于导出当前模块的方法或变量，并且它是唯一导出的出口（exports 变量，指向 module.exports）。在模块中，还存在一个`module`对象，它代表模块自身，而`exports`是`module`的属性。

	```
	// main.js
	var counter = 3;
	function incCounter() {
	  counter++;
	}
	//如果一个模块的对外接口，就是一个单一的值，
	//不能使用exports输出，只能使用module.exports输出
	module.exports = {
	  counter: counter, 
	  incCounter: incCounter,
	};
	//sum.js
	var main = require('main');
	exports.add = function(){
	    return main.incCounter();
	};

	```

* #### 模块标识
模块标识其实就是传递给`require()`方法的参数，它必须是符合小驼峰命名的字符串，或者以`.`、`..`开头的相对路径，或者绝对路径。require命令用于加载文件，后缀名默认为`.js`。
根据参数的不同格式，require命令去不同路径寻找模块文件。       
**加载规则**
	（1) 如果参数字符串以`/`开头，则表示加载的是一个位于绝对路径的模块文件。比如，`require('/home/marco/foo.js')`将加载`/home/marco/foo.js`。
	（2)如果参数字符串以`./`开头，则表示加载的是一个位于相对路径（跟当前执行脚本的位置相比）的模块文件。比如，`require('./circle')`将加载当前脚本同一目录的`circle.js`。
	（3)如果参数字符串不以`./`或`/`开头，则表示加载的是一个默认提供的核心模块（位于Node的系统安装目录中），或者一个位于各级`node_modules`目录的已安装模块（全局安装或局部安装）。
	举例来说，脚本`/home/user/projects/foo.js`执行了`require('bar.js')`命令，Node会依次搜索以下文件。

	```
	/usr/local/lib/node/bar.js
	/home/user/projects/node_modules/bar.js
	/home/user/node_modules/bar.js
	/home/node_modules/bar.js
	/node_modules/bar.js
	```
	这样设计的目的是，使得不同的模块可以将所依赖的模块本地化。

	（4）如果参数字符串不以`./`或`/`开头，而且是一个路径，比如 `require('example-module/path/to/file')` ，则将先找到 `example-module` 的位置，然后再以它为参数，找到后续路径。
	（5）如果指定的模块文件没有发现，Node会尝试为文件名添加 `.js` 、 `.json` 、 `.node` 后，再去搜索。`.js` 件会以文本格式的JavaScript脚本文件解析，`.json` 文件会以JSON格式的文本文件解析， `.node` 文件会以编译后的二进制文件解析。
	（6）如果想得到 `require` 命令加载的确切文件名，使用 `require.resolve()` 方法。

### CommonJS模块的特点
* 所有代码都运行在模块作用域，不会污染全局作用域。
* 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。且CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作
* 模块加载的顺序，按照其在代码中出现的顺序。


## AMD

AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

### AMD规范

* #### 模块引用
AMD也采用`require()`语句加载模块，但是不同于CommonJS，它要求两个参数：
```
require([module], callback);
```

	第一个参数`[module]`，是一个数组，里面的成员就是要加载的模块；第二个参数`callback`，则是加载成功之后的回调函数。如果将前面的代码改写成AMD形式，就是下面这样：

	```
	require(['math'], function (math) {
	　　　　math.add(2, 3);
	　　});
	```

 	require方法也可以用在define方法内部。

	```
		define(function (require) {
		   var otherModule = require('otherModule');
		});
	```

 	require方法允许添加第三个参数，即错误处理的回调函数。

	```
	require(
		    [ "backbone" ], 
		    function ( Backbone ) {
		        return Backbone.View.extend({ /* ... */ });
		    }, 
		    function (err) {
				// ...
		    }
		);
	```


* #### 模块定义
`define()`方法用于定义模块，它是全局变量。

	```
	define(id?: String, dependencies?: String[], factory: Function|Object);
	```

	* id（名字）
第一个参数，`id`，是个字符串。它指的是定义中模块的名字，这个参数是可选的。如果没有提供该参数，模块的名字应该默认为模块加载器请求的指定脚本的名字。如果提供了该参数，模块名必须是“顶级”的和绝对的（不允许相对名字）。
**模块名的格式**
模块名用来唯一标识定义中模块，它们同样在依赖数组中使用。AMD的模块名规范是CommonJS模块名规范的超集。引用如下：
		* 模块名是由一个或多个单词以正斜杠为分隔符拼接成的字符串
		* 单词须为驼峰形式，或者`.`，`..`
		* 模块名不允许文件扩展名的形式，如`.js`
		* 模块名可以为 `相对的` 或 `顶级的`。如果首字符为`.`或`..`则为`相对的`模块名
		* 顶级的模块名从根命名空间的概念模块解析
		* 相对的模块名从 `require` 书写和调用的模块解析    

	  	上文引用的CommonJS模块id属性常被用于JavaScript模块。
	  	相对模块名解析示例：
		* 如果模块 `a/b/c` 请求 `../d`, 则解析为`a/d`
		* 如果模块 `a/b/c` 请求 `./e`, 则解析为`a/b/e`

	* dependencies（依赖）
第二个参数，`dependencies`，是个定义中模块所依赖模块的数组。依赖模块必须根据模块的factory方法优先级执行，并且执行的结果应该按照依赖数组中的位置顺序以参数的形式传入（定义中模块的）factory方法中。

 	依赖的模块名如果是相对的，应该解析为相对定义中的模块。换句话来说，相对名解析为相对于模块的名字，并非相对于寻找该模块的名字的路径。

 	本规范定义了三种特殊的依赖关键字。如果`require`,`exports`, 或 `module`出现在依赖列表中，参数应该按照CommonJS模块规范自由变量去解析。 

 	依赖参数是可选的，如果忽略此参数，它应该默认为`["require", "exports", "module"]`。然而，如果factory方法的形参个数小于3，加载器会选择以函数指定的参数个数调用factory方法。

 * factory（函数）
第三个参数，`factory`，为模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次。如果是对象，此对象应该为模块的输出值。

   如果工厂方法返回一个值（对象，函数，或任意强制类型转换为true的值），应该为设置为模块的输出值。

  	按照是否依赖其他模块，可以分成两种情况讨论。第一种情况是**定义独立模块**，即所定义的模块不依赖其他模块；第二种情况是**定义非独立模块**，即所定义的模块依赖于其他模块。
 
	```
	//独立模块
	define({
	    method1: function() {},
	    method2: function() {},
	});
	//等价于
	define(function () {
		return {
		    method1: function() {},
			method2: function() {},
	    };
	});
	//非独立模块
	define(['module1', 'module2'], function(m1, m2) {
	   ...
	});
	```


## CMD规范
在 CMD 规范中，一个模块就是一个文件。

### define 
```
define(factory);
```
 `define` 接受 `factory` 参数，`factory` 可以是一个函数，也可以是一个对象或字符串。
* `factory` 为对象、字符串时，表示模块的接口就是该对象、字符串。比如可以如下定义一个 JSON 数据模块：
```
define({ "foo": "bar" });
```
* 也可以通过字符串定义模板模块
```
define('I am a template. My name is {{name}}.');
```
* `factory` 为函数时，表示是模块的构造方法。执行该构造方法，可以得到模块向外提供的接口。`factory` 方法在执行时，默认会传入三个参数：`require`、`exports` 和 `module`：
```
define(function(require, exports, module) {

  // 模块代码

});

```

* `define` 也可以接受两个以上参数。字符串 `id` 表示模块标识，数组 `deps` 是模块依赖。`id `和 `deps` 参数可以省略。省略时，可以通过构建工具自动生成。**但带 `id` 和 `deps` 参数的 `define` 用法不属于 CMD 规范，而属于 Modules/Transport 规范。**
```
define('hello', ['jquery'], function(require, exports, module) {

  // 模块代码

});
```

* define.cmd
一个空对象，可用来判定当前页面是否有 CMD 模块加载器：
```
if (typeof define === "function" && define.cmd) {
  // 有 Sea.js 等 CMD 模块加载器存在
}
```


### require
`require` 是 `factory` 函数的第一个参数。
* **require(id)**
require 是一个方法，接受 模块标识 作为唯一参数，用来获取其他模块提供的接口。
```
define(function(require, exports) {

  // 获取模块 a 的接口
  var a = require('./a');

  // 调用模块 a 的方法
  a.doSomething();

});

```

* **require.async(id, callback?)**
require.async 方法用来在模块内部异步加载模块，并在加载完成后执行指定回调。callback 参数可选。
```
define(function(require, exports, module) {

  // 异步加载一个模块，在加载完成时，执行回调
  require.async('./b', function(b) {
    b.doSomething();
  });

  // 异步加载多个模块，在加载完成时，执行回调
  require.async(['./c', './d'], function(c, d) {
    c.doSomething();
    d.doSomething();
  });

});
```
 **注意**：`require` 是同步往下执行，`require.async `则是异步回调执行。`require.async` 一般用来加载可延迟异步加载的模块。 

* **require.resolve(id)**
使用模块系统内部的路径解析机制来解析并返回模块路径。该函数不会加载模块，只返回解析后的绝对路径。
```
define(function(require, exports) {

  console.log(require.resolve('./b'));
  // ==> http://example.com/path/to/b.js

});
```
 这可以用来获取模块路径，一般用在插件环境或需动态拼接模块路径的场景下

### exports
`exports` 是一个对象，用来向外提供模块接口。
```
define(function(require, exports) {

  // 对外提供 foo 属性
  exports.foo = 'bar';

  // 对外提供 doSomething 方法
  exports.doSomething = function() {};

});
```
### module
`module` 是一个对象，上面存储了与当前模块相关联的一些属性和方法。

* module.id 
模块的唯一标识。
```
//`define` 的第一个参数就是模块标识。
define('id', [], function(require, exports, module) {

  // 模块代码

});
```

* module.uri 
根据模块系统的路径解析规则得到的模块绝对路径。
**注意**：一般情况下（没有在 define 中手写 id 参数时），module.id 的值就是 module.uri，两者完全相同。

* module.dependencies
`dependencies` 是一个数组，表示当前模块的依赖。

* module.exports 
当前模块对外提供的接口。
传给`factory` 构造方法的 `exports` 参数是 `module.exports`对象的一个引用。只通过 `exports` 参数来提供接口，有时无法满足开发者的所有需求。 比如当模块的接口是某个类的实例时，需要通过 `module.exports` 来实现.
```
define(function(require, exports, module) {

  // exports 是 module.exports 的一个引用
  console.log(module.exports === exports); // true

  // 重新给 module.exports 赋值
  module.exports = new SomeClass();

  // exports 不再等于 module.exports
  console.log(module.exports === exports); // false

});
```
 **注意**：对 module.exports 的赋值需要同步执行，不能放在回调函数里。下面这样是不行的：
 
	```
	// x.js
	define(function(require, exports, module) {

	  // 错误用法
	  setTimeout(function() {
	    module.exports = { a: "hello" };
	  }, 0);

	});
	```
 在 y.js 里有调用到上面的 x.js:

	```
	// y.js
	define(function(require, exports, module) {

	  var x = require('./x');

	  // 无法立刻得到模块 x 的属性 a
	  console.log(x.a); // undefined

	});
	```

# ECMAScript 6 模块机制

`ECMAScript 6`基于`export`和`import`，定义了模块的导出和导入规范，在语言标准层面实现了模块机制。该标准的目标是创建一种能够兼容`CommoneJS`和`AMD`两标准的规范，即可以像`CommoneJS`一样语法简洁、使用单一的接口且支持循环依赖，又可以像`AMD`支持异步加载和可配置的模块加载。

## ES6的模块机制具有以下特点：
* 简洁的语法。语法将比`CommoneJS`更简单，只使用`export`和`import`实现模块的导出和导入(1、 使用`export`关键字定义导出对象，这个关键字可以无限次使用 2、 使用`import`关键字引入导入对象，这个关键字可导入任意数量的模块)
 
 
	
* 模块结构可以做静态分析。这使得在编译时就能确定模块的依赖关系，以及输入和输出的变量
* 模块支持异步加载
* 为加载模块提供编程支持，可以按需加载
* 比`CommonJS`更优秀的循环依赖处理

## ES6定义的模块标准由两部分组成：
* **声明语法**（定义引入与导出）
* **编程式加载接口（API）**：用于配置如何加载模块和按条件加载模块

### 声明语法

#### export
`export`语法声明用于导出函数、对象、指定文件（或模块）的原始值。`export`有两种模块导出方式：命名式导出（名称导出）和定义式导出（默认导出），命名式导出每个模块可以多个，而默认导出每个模块仅一个。模块导出分为：`命名式导出` 和 `默认导出`。

**命名式导出**
模块可以通过`export`前缀关键词声明导出对象，导出对象可以是多个。这些导出对象用名称进行区分，称之为`命名式导出`。
```
export { myFunction,name }; // 导出一个已定义的函数和变量
export const foo = Math.sqrt(2); // 导出一个常量
//我们可以使用*和from关键字来实现的模块的继承：
export * from 'article';
```

**默认导出**
`默认导出`也被称做`定义式导出`。命名式导出可以导出多个值，但在在`import`引用时，也要使用相同的名称来引用相应的值。而默认导出每个导出只有一个单一值，这个输出可以是一个函数、类或其它类型的值，这样在模块`import`导入时也会很容易引用。
```
export default function() {}; // 可以导出一个函数
export default class(){}; // 也可以出一个类
```

`export`可能会有以下几种形式的导出语法：
```
export { name1, name2, …, nameN };
export { variable1 as name1, variable2 as name2, …, nameN };
export let name1, name2, …, nameN; // 也可以是 var
export let name1 = …, name2 = …, …, nameN; // 也可以是 var, const

export default expression;
export default function (…) { … } // 也可以是 class, function*
export default function name1(…) { … } // 也可以是 class, function*
export { name1 as default, … };

export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
```
* `name1… nameN`－导出的“标识符”。导出后，可以通过这个“标识符”在另一个模块中使用`import`引用
* `default`－设置模块的默认导出。设置后`import`不通过“标识符”而直接引用默认导入
* `*`－继承模块并导出继承模块所有的方法和属性
* `as`－重命名导出“标识符”
* `from`－从已经存在的模块、脚本文件…导出

**注意：**`export`命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。
```
// 报错
export 1;

// 报错
var m = 1;
export m;

// 正确写法一
export var m = 1;

// 正确写法二
var m = 1;
export {m};

// 正确写法三
var n = 1;
export {n as m};
```

#### import
`import`语法声明用于从已导出的模块、脚本中导入函数、对象、指定文件（或模块）的原始值。`import`模块导入与`export`模块导出功能相对应，也存在两种模块导入方式：`命名式导入`（名称导入）和`默认导入`（定义式导入）。 
**命名式导入**
我们可以通过指定名称，就是将这些成员插入到当作用域中。
```
//可以导入单个成员或多个成员：
import {myMember} from "my-module";
import {foo, bar} from "my-module";
//通过*符号，我们可以导入模块中的全部属性和方法
import * as myModule from "my-module";
//也可以使用as对导入成员重命名，以方便在当前模块内使用：
import {reallyReallyLongModuleMemberName as shortName} from "my-module";
//导入多个成员时，同样可以使用别名
import {reallyReallyLongModuleMemberName as shortName, anotherLongModuleName as short} from "my-module";
//导入一个模块，但不进行任何绑定
import "my-module";
```

**默认导入**
在模块导出时，可能会存在默认导出。同样的，在导入时可以使用import指令导出这些默认值。
```
//直接导入默认值：
import myDefault from "my-module";
//也可以在命名空间导入和名称导入中，同时使用默认导入：
import myDefault, * as myModule from "my-module";// myModule 做为命名空间使用 
//或
import myDefault, {foo, bar} from "my-module"; // 指定成员导入
```


`import`可能会有以下几种形式的导入语法：
```
import defaultMember from "module-name";
import * as name from "module-name";
import { member } from "module-name";
import { member as alias } from "module-name";
import { member1 , member2 } from "module-name";
import { member1 , member2 as alias2 , [...] } from "module-name";
import defaultMember, { member [ , [...] ] } from "module-name";
import defaultMember, * as name from "module-name";
import "module-name";
```

* `name`－从将要导入模块中收到的导出值的名称
* `member`, memberN－从导出模块，导入指定名称的多个成员
* `defaultMember`－从导出模块，导入默认导出成员
* `alias, aliasN`－别名，对指定导入成员进行的重命名
* `module-name`－要导入的模块。是一个文件名
* `as`－重命名导入成员名称（“标识符”）
* `from`－从已经存在的模块、脚本文件等导入

由于`import`是静态执行，所以不能使用表达式和变量。
```
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}

```


**注意：** `export`和`import`命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错。这是因为处于条件代码块之中，就没法做静态优化了，违背了ES6模块的设计初衷。
