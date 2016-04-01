---
title: HTML5、CSS3、ES6编程规范
date:  2016-03-21 20:09:24
tags:  知识点
---

		如何才能写出高质量的代码，一定要熟悉各个编程语言的规范。

<!--more-->



## HTML

### Semantics 语义化

HTML5为我们提供了大量的语义元素用来更准确的描述内容。你可以借助这些丰富的语义化的元素来组织你的网站：


	<!-- bad -->
	<div id="main">
	  <div class="article">
	    <div class="header">
	      <h1>Blog post</h1>
	      <p>Published: <span>21st Feb, 2015</span></p>
	    </div>
	    <p>…</p>
	  </div>
	</div>

	<!-- good -->
	<main>
	  <article>
	    <header>
	      <h1>Blog post</h1>
	      <p>Published: <time datetime="2015-02-21">21st Feb, 2015</time></p>
	    </header>
	    <p>…</p>
	  </article>
	</main>

Make sure you understand the semantic of the elements you're using. It's worse to use a semantic element in a wrong way than staying neutral.

	<!-- bad -->
	<h1>
	  <figure>
	    <img alt=Company src=logo.png>
	  </figure>
	</h1>

	<!-- good -->
	<h1>
	  <img alt=Company src=logo.png>
	</h1>

### Brevity 简洁

遵循HTML5新的代码规则，忘记XHTML中那些繁杂的习惯。


	<!-- bad -->
	<!doctype html>
	<html lang=en>
	  <head>
	    <meta http-equiv=Content-Type content="text/html; charset=utf-8" />
	    <title>Contact</title>
	    <link rel=stylesheet href=style.css type=text/css />
	  </head>
	  <body>
	    <h1>Contact me</h1>
	    <label>
	      Email address:
	      <input type=email placeholder=you@email.com required=required />
	    </label>
	    <script src=main.js type=text/javascript></script>
	  </body>
	</html>

	<!-- good -->
	<!doctype html>
	<html lang=en>
	  <meta charset=utf-8>
	  <title>Contact</title>
	  <link rel=stylesheet href=style.css>

	  <h1>Contact me</h1>
	  <label>
	    Email address:
	    <input type=email placeholder=you@email.com required>
	  </label>
	  <script src=main.js></script>
	</html>

### Accessibility 可访问性

Accessibility shouldn't be an afterthought. You don't have to be a WCAG expert to improve your
website, you can start immediately by fixing the little things that make a huge difference, such as:

* learning to use the `alt` attribute properly
* making sure your links and buttons are marked as such (no `<div class=button>` atrocities)
* not relying exclusively on colors to communicate information
* explicitly labelling form controls


		<!-- bad -->
		<h1><img alt="Logo" src="logo.png"></h1>

		<!-- good -->
		<h1><img alt="My Company, Inc." src="logo.png"></h1>



### Language 语言

虽然定义语言和字符编码方式是可选的，但还是推荐你始终在文档级别声明它们。

	<!-- bad -->
	<!doctype html>
	<title>Hello, world.</title>

	<!-- good -->
	<!doctype html>
	<html lang=en>
	  <meta charset=utf-8>
	  <title>Hello, world.</title>
	</html>


### Performance 性能

先文档后脚本，尽量不要把脚本代码放在顶部。

Unless there's a valid reason for loading your scripts before your content, don't block the
rendering of your page. If your style sheet is heavy, isolate the styles that are absolutely
required initially and defer the loading of the secondary declarations in a separate style sheet.

Two HTTP requests is significantly slower than one, but the perception of speed is the most
important factor.


	<!-- bad -->
	<!doctype html>
	<meta charset=utf-8>
	<script src=analytics.js></script>
	<title>Hello, world.</title>
	<p>...</p>

	<!-- good -->
	<!doctype html>
	<meta charset=utf-8>
	<title>Hello, world.</title>
	<p>...</p>
	<script src=analytics.js></script>


## CSS

### Semicolons 始终用分号作为结束符

While the semicolon is technically a separator in CSS, always treat it as a terminator.


	/* bad */
	div {
	  color: red
	}

	/* good */
	div {
	  color: red;
	}


### Box model 盒模型

The box model should ideally be the same for the entire document. A global
`* { box-sizing: border-box; }` is fine, but don't change the default box model
on specific elements if you can avoid it.


	/* bad */
	div {
	  width: 100%;
	  padding: 10px;
	  box-sizing: border-box;
	}

	/* good */
	div {
	  padding: 10px;
	}


### Flow 流

Don't change the default behavior of an element if you can avoid it. Keep elements in the
natural document flow as much as you can. For example, removing the white-space below an
image shouldn't make you change its default display:


	/* bad */
	img {
	  display: block;
	}

	/* good */
	img {
	  vertical-align: middle;
	}


Similarly, don't take an element off the flow if you can avoid it.


	/* bad */
	div {
	  width: 100px;
	  position: absolute;
	  right: 0;
	}

	/* good */
	div {
	  width: 100px;
	  margin-left: auto;
	}


### Positioning 定位方式

There are many ways to position elements in CSS but try to restrict yourself to the
properties/values below. By order of preference:


	display: block;
	display: flex;
	position: relative;
	position: sticky;
	position: absolute;
	position: fixed;


### Selectors 选择器

Minimize selectors tightly coupled to the DOM. Consider adding a class to the elements
you want to match when your selector exceeds 3 structural pseudo-classes, descendant or
sibling combinators.

	/* bad */
	div:first-of-type :last-child > p ~ *

	/* good */
	div:first-of-type .info


Avoid overloading your selectors when you don't need to.


	/* bad */
	img[src$=svg], ul > li:first-child {
	  opacity: 0;
	}

	/* good */
	[src$=svg], ul > :first-child {
	  opacity: 0;
	}


### Specificity

Don't make values and selectors hard to override. Minimize the use of `id`'s
and avoid `!important`.


	/* bad */
	.bar {
	  color: green !important;
	}
	.foo {
	  color: red;
	}

	/* good */
	.foo.bar {
	  color: green;
	}
	.foo {
	  color: red;
	}


### Overriding 覆盖

Overriding styles makes selectors and debugging harder. Avoid it when possible.


	/* bad */
	li {
	  visibility: hidden;
	}
	li:first-child {
	  visibility: visible;
	}

	/* good */
	li + li {
	  visibility: hidden;
	}


### Inheritance 继承

Don't duplicate style declarations that can be inherited.


	/* bad */
	div h1, div p {
	  text-shadow: 0 1px 0 #fff;
	}

	/* good */
	div {
	  text-shadow: 0 1px 0 #fff;
	}


### Brevity 代码简写

Keep your code terse. Use shorthand properties and avoid using multiple properties when
it's not needed.

	/* bad */
	div {
	  transition: all 1s;
	  top: 50%;
	  margin-top: -10px;
	  padding-top: 5px;
	  padding-right: 10px;
	  padding-bottom: 20px;
	  padding-left: 10px;
	}

	/* good */
	div {
	  transition: 1s;
	  top: calc(50% - 10px);
	  padding: 5px 10px 20px;
	}

### Language 语言

使用单词而不是数学表达式。

	/* bad */
	:nth-child(2n + 1) {
	  transform: rotate(360deg);
	}

	/* good */
	:nth-child(odd) {
	  transform: rotate(1turn);
	}

### Vendor prefixes 厂商前缀

Kill obsolete vendor prefixes aggressively. If you need to use them, insert them before the
standard property.

	/* bad */
	div {
	  transform: scale(2);
	  -webkit-transform: scale(2);
	  -moz-transform: scale(2);
	  -ms-transform: scale(2);
	  transition: 1s;
	  -webkit-transition: 1s;
	  -moz-transition: 1s;
	  -ms-transition: 1s;
	}

	/* good */
	div {
	  -webkit-transform: scale(2);
	  transform: scale(2);
	  transition: 1s;
	}

### Animations 动画

Favor transitions over animations. Avoid animating other properties than
`opacity` and `transform`.

	/* bad */
	div:hover {
	  animation: move 1s forwards;
	}
	@keyframes move {
	  100% {
	    margin-left: 100px;
	  }
	}

	/* good */
	div:hover {
	  transition: 1s;
	  transform: translateX(100px);
	}

### Units 单位

Use unitless values when you can. Favor `rem` if you use relative units. Prefer seconds over
milliseconds.

	/* bad */
	div {
	  margin: 0px;
	  font-size: .9em;
	  line-height: 22px;
	  transition: 500ms;
	}

	/* good */
	div {
	  margin: 0;
	  font-size: .9rem;
	  line-height: 1.5;
	  transition: .5s;
	}

### Colors 使用16进制颜色

If you need transparency, use `rgba`. Otherwise, always use the hexadecimal format.

	/* bad */
	div {
	  color: hsl(103, 54%, 43%);
	}

	/* good */
	div {
	  color: #5a3;
	}

### Drawing 绘图

Avoid HTTP requests when the resources are easily replicable with CSS.

	/* bad */
	div::before {
	  content: url(white-circle.svg);
	}

	/* good */
	div::before {
	  content: "";
	  display: block;
	  width: 20px;
	  height: 20px;
	  border-radius: 50%;
	  background: #fff;
	}

### Hacks

Don't use them.

	/* bad */
	div {
	  // position: relative;
	  transform: translateZ(0);
	}

	/* good */
	div {
	  /* position: relative; */
	  will-change: transform;
	}

## JavaScript

### Performance 性能

Favor readability, correctness and expressiveness over performance. JavaScript will basically never be your performance bottleneck. Optimize things like image compression, network access and DOM reflows instead. If you remember just one guideline from this document, choose this one.

	// bad (albeit way faster)
	const arr = [1, 2, 3, 4];
	const len = arr.length;
	var i = -1;
	var result = [];
	while (++i < len) {
	  var n = arr[i];
	  if (n % 2 > 0) continue;
	  result.push(n * n);
	}

	// good
	const arr = [1, 2, 3, 4];
	const isEven = n => n % 2 == 0;
	const square = n => n * n;

	const result = arr.filter(isEven).map(square);

### Statelessness

努力让你的函数更加纯净。所有的函数都应该是没有边际效应的，不要使用外部数据，返回新的对象。

Try to keep your functions pure. All functions should ideally produce no side-effects, use no outside data and return new objects instead of mutating existing ones.

	// bad
	const merge = (target, ...sources) => Object.assign(target, ...sources);
	merge({ foo: "foo" }, { bar: "bar" }); // => { foo: "foo", bar: "bar" }

	// good
	const merge = (...sources) => Object.assign({}, ...sources);
	merge({ foo: "foo" }, { bar: "bar" }); // => { foo: "foo", bar: "bar" }

### Natives

尽可能使用原生的方法。

	// bad
	const toArray = obj => [].slice.call(obj);

	// good
	const toArray = (() =>
	  Array.from ? Array.from : obj => [].slice.call(obj)
	)();

### Coercion

Embrace implicit coercion when it makes sense. Avoid it otherwise. Don't cargo-cult.

	// bad
	if (x === undefined || x === null) { ... }

	// good
	if (x == undefined) { ... }

### Loops 循环

Don't use loops as they force you to use mutable objects. Rely on `array.prototype` methods.

	// bad
	const sum = arr => {
	  var sum = 0;
	  var i = -1;
	  for (;arr[++i];) {
	    sum += arr[i];
	  }
	  return sum;
	};

	sum([1, 2, 3]); // => 6

	// good
	const sum = arr =>
	  arr.reduce((x, y) => x + y);

	sum([1, 2, 3]); // => 6

If you can't, or if using `array.prototype` methods is arguably abusive, use recursion.


	// bad
	const createDivs = howMany => {
	  while (howMany--) {
	    document.body.insertAdjacentHTML("beforeend", "<div></div>");
	  }
	};
	createDivs(5);

	// bad
	const createDivs = howMany =>
	  [...Array(howMany)].forEach(() =>
	    document.body.insertAdjacentHTML("beforeend", "<div></div>")
	  );
	createDivs(5);

	// good
	const createDivs = howMany => {
	  if (!howMany) return;
	  document.body.insertAdjacentHTML("beforeend", "<div></div>");
	  return createDivs(howMany - 1);
	};
	createDivs(5);


### Arguments 参数

Forget about the `arguments` object. The rest parameter is always a better option because:

1. it's named, so it gives you a better idea of the arguments the function is expecting
2. it's a real array, which makes it easier to use.

	// bad
	const sortNumbers = () =>
	  Array.prototype.slice.call(arguments).sort();

	// good
	const sortNumbers = (...numbers) => numbers.sort();

### Apply 忘记Apply

Forget about `apply()`. Use the spread operator instead.

	const greet = (first, last) => `Hi ${first} ${last}`;
	const person = ["John", "Doe"];

	// bad
	greet.apply(null, person);

	// good
	greet(...person);

### Bind 绑定

Don't `bind()` when there's a more idiomatic approach.

	// bad
	["foo", "bar"].forEach(func.bind(this));

	// good
	["foo", "bar"].forEach(func, this);


	// bad
	const person = {
	  first: "John",
	  last: "Doe",
	  greet() {
	    const full = function() {
	      return `${this.first} ${this.last}`;
	    }.bind(this);
	    return `Hello ${full()}`;
	  }
	}

	// good
	const person = {
	  first: "John",
	  last: "Doe",
	  greet() {
	    const full = () => `${this.first} ${this.last}`;
	    return `Hello ${full()}`;
	  }
	}

### Higher-order functions 高阶函数

当你没必要使用嵌套函数的时候一定不要使用。

	// bad
	[1, 2, 3].map(num => String(num));

	// good
	[1, 2, 3].map(String);

### Composition

Avoid multiple nested function calls. Use composition instead.


	const plus1 = a => a + 1;
	const mult2 = a => a * 2;

	// bad
	mult2(plus1(5)); // => 12

	// good
	const pipeline = (...funcs) => val => funcs.reduce((a, b) => b(a), val);
	const addThenMult = pipeline(plus1, mult2);
	addThenMult(5); // => 12

### Caching 缓存

Cache feature tests, large data structures and any expensive operation.

	// bad
	const contains = (arr, value) =>
	  Array.prototype.includes
	    ? arr.includes(value)
	    : arr.some(el => el === value);
	contains(["foo", "bar"], "baz"); // => false

	// good
	const contains = (() =>
	  Array.prototype.includes
	    ? (arr, value) => arr.includes(value)
	    : (arr, value) => arr.some(el => el === value)
	)();
	contains(["foo", "bar"], "baz"); // => false

### Variables 变量

Favor `const` over `let` and `let` over `var`.

	// bad
	var obj = {};
	obj["foo" + "bar"] = "baz";

	// good
	const obj = {
	  ["foo" + "bar"]: "baz"
	};

### Conditions 条件

Favor IIFE's and return statements over if, else if, else and switch statements.

	// bad
	var grade;
	if (result < 50)
	  grade = "bad";
	else if (result < 90)
	  grade = "good";
	else
	  grade = "excellent";

	// good
	const grade = (() => {
	  if (result < 50)
	    return "bad";
	  if (result < 90)
	    return "good";
	  return "excellent";
	})();


### Object iteration 对象迭代

尽量避免使用 `for...in`

	const shared = { foo: "foo" };
	const obj = Object.create(shared, {
	  bar: {
	    value: "bar",
	    enumerable: true
	  }
	});

	// bad
	for (var prop in obj) {
	  if (obj.hasOwnProperty(prop))
	    console.log(prop);
	}

	// good
	Object.keys(obj).forEach(prop => console.log(prop));

### Objects as Maps

While objects have legitimate use cases, maps are usually a better, more powerful choice. When in doubt, use a `Map`.

	// bad
	const me = {
	  name: "Ben",
	  age: 30
	};
	var meSize = Object.keys(me).length;
	meSize; // => 2
	me.country = "Belgium";
	meSize++;
	meSize; // => 3

	// good
	const me = Map();
	me.set("name", "Ben");
	me.set("age", 30);
	me.size; // => 2
	me.set("country", "Belgium");
	me.size; // => 3

### Curry

Currying might have its place in other languages, but avoid it in JavaScript. It makes your code harder to read by introducing a foreign paradigm while the appropriate use cases are extremely unusual.

	// bad
	const sum = a => b => a + b;
	sum(5)(3); // => 8

	// good
	const sum = (a, b) => a + b;
	sum(5, 3); // => 8

### Readability 可读性

Don't obfuscate the intent of your code by using seemingly smart tricks.

	// bad
	foo || doSomething();

	// good
	if (!foo) doSomething();
	```
	```javascript
	// bad
	void function() { /* IIFE */ }();

	// good
	(function() { /* IIFE */ }());
	```
	```javascript
	// bad
	const n = ~~3.14;

	// good
	const n = Math.floor(3.14);

### Code reuse 代码重用

Don't be afraid of creating lots of small, highly composable and reusable functions.

	// bad
	arr[arr.length - 1];

	// good
	const first = arr => arr[0];
	const last = arr => first(arr.slice(-1));
	last(arr);
	```
	```javascript
	// bad
	const product = (a, b) => a * b;
	const triple = n => n * 3;

	// good
	const product = (a, b) => a * b;
	const triple = product.bind(null, 3);

### Dependencies 最小化依赖

Minimize dependencies. Third-party is code you don't know. Don't load an entire library for just a couple of methods easily replicable:

	// bad
	var _ = require("underscore");
	_.compact(["foo", 0]));
	_.unique(["foo", "foo"]);
	_.union(["foo"], ["bar"], ["foo"]);

	// good
	const compact = arr => arr.filter(el => el);
	const unique = arr => [...Set(arr)];
	const union = (...arr) => unique([].concat(...arr));

	compact(["foo", 0]);
	unique(["foo", "foo"]);
	union(["foo"], ["bar"], ["foo"]);

##Other resources

1. 前端代码规范及最佳实践： http://coderlmn.github.io/code-standards/
2. 前端代码规范： http://materliu.github.io/code-guide/
