---
title: call()、apply()、bind()的用法
date:  2016-03-27 12:09:24
tags:  知识点
---

      本文将要探讨JavaScript中函数的一些特殊用法，包括`call`、`apply`、`bind`三个原型方法。

<!--more-->



## 函数基础

JavaScript中的函数是一种类似于Java中方法的语言功能，不过它可以独立于类进行定义。

> **函数式编程：** 由于JavaScript支持匿名函数，因此可以将函数作为对象来使用，
所以JavaScript不仅支持过程式编程（面向对象也是过程式编程的一种），还支持函数式编程。

### 上下文

函数的每次**调用**都会拥有一个特殊值——本次调用的上下文（context）——这就是`this`关键字的值。
如果函数挂载在一个对象上，作为对象的一个属性，就称它为对象的方法。当通过这个对象来调用函数时，该对象就是此次调用的上下文，
也就是该函数的`this`的值。

需要注意，`this`是一个关键字，不是变量，也不是属性名。JavaScript的语法不允许给`this`赋值。

### 函数是一种对象

JavaScript中的函数和Java中的方法或C语言中的函数最大不同在于，JavaScript中的函数也是一种对象。
但这并不意味着，所有的对象都是函数。函数是一种包含了可执行代码，并能够被其他代码调用的特殊的对象。

和变量不同，关键字`this`没有作用域的限制，嵌套的函数不会从调用它的函数中继承`this`。
- 如果嵌套函数作为方法调用，其`this`的值指向调用它的对象。
- 如果嵌套函数作为函数调用，其`this`的值不是全局对象（非严格模式下）就是`undefined`（严格模式下）。

很多人误以为调用嵌套函数时`this`会指向掉i用外层函数的上下文。如果你想访问这个外部函数的`this`值，
需要将`this`的值保存都在一个变量中，这个变量和内部函数都同在一个作用域内。例如：

```javascript
var o = {
  m: function() {
    var self = this;
    console.log(this==o); // true
    f();

    function f() {
      console.log(this === o); // false，this的值是全局对象或undefined
      console.log(self === o); // true
    }
  }
}
```

### 闭包

JavaScript的函数可以嵌套在其他函数中定义，这样它们就可以访问它们被定义时所处的作用域中的任何变量。
这意味着JavaScript函数构成了一个闭包（closure），它给JavaScript带来了非常强劲的编程能力。

## 作为值的函数

在JavaScript中，函数不仅是一种语法，也是值，也就是说，可以将函数赋值给变量，存储在对象的属性或数组的元素中，
作为参数传入另外一个函数等。

## bind、call、apply

每一个函数都包含一个`prototype`属性，这个属性是指向一个对象的引用，这个对象称作“原型对象”。
每一个函数都包含不同的原型对象。当将函数用作构造函数的时候，新创建的对象会从原型对象上继承属性。

### `Function.prototype.call()`和`Function.prototype.apply()`

`call()`和`apply()`可以看作为某个对象的方法，通过调用方法的形式来间接调用函数。
它们的第一个参数是要调用函数的母对象，它是调用上下文，在函数体内通过`this`来获得对它的引用。
`apply()`方法和`call()`方法的作用相同，只不过函数传递的方式不一样，它的实参都放入在一个数组中。

举个例子，**以对象o的方法的形式调用函数f()，并传入两个参数**，可以使用这样的代码：

```javascript

var o = {};

function f(a, b) {
  return a + b;
}

f.call(o, 1, 2);        // 将函数f作为o的方法，实际上就是重新设置函数f的上下文
f.apply(o, [1, 2]);
```

再举一个来自MDN的[例子](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)，
**使用call方法调用匿名函数**：

在下例中的for循环体内，我们创建了一个匿名函数，然后通过调用该函数的call方法，将每个数组元素作为指定的this值执行了那个匿名函数。
这个匿名函数的主要目的是给每个数组元素对象添加一个print方法，这个print方法可以打印出各元素在数组中的正确索引号。
当然，这里不是必须得让数组元素作为this值传入那个匿名函数（普通参数就可以），目的是为了演示call的用法。

```javascript
var animals = [
  {species: 'Lion', name: 'King'},
  {species: 'Whale', name: 'Fail'}
];

for (var i = 0; i < animals.length; i++) {
  (function (i) {
    this.print = function () {
      console.log('#' + i  + ' ' + this.species + ': ' + this.name);
    }
    this.print();
  }).call(animals[i], i);
}
```

### `Function.prototype.bind()`

`bind()`是在ES5中新增的方法，从名字可以看出，这个方法的主要作用就是将函数绑定到某个对象。
当在函数`f()`上调用`bind()`方法并后窜入一个对象`o`作为参数，这个方法将返回一个新函数：
（以函数调用的方式）调用新的函数将会把原始的函数`f()`作为`o`的方法来调用。例如：

```javascript
function f(y) {
  return this.x + y;
}

var o = {
  x: 1
};

var g = f.bind(o);  // 通过调用 g(x) 来调用 o.f(x)
g(2); // 3
```

其实我们可以轻松的实现`bind()`方法：

```javascript
// 返回一个函数，通过调用它来调用o中的方法f()，传递它所有的实参
function bind(f, o) {
  if (f.bind) return f.bind(o); // 如果bind()方法存在，使用bind()方法
  else return function () {
    return f.apply(o, arguments);
  }
}

```

## 函数式编程

JavaScript并非函数式编程语言，但在JavaScript中可以像操控对象一样操控函数，也就是说可以在JavaScript中应用函数式编程技术。

### 使用函数处理数组

假设有一个数组，数组元素都是数字，我们想要计算这些元素的平均值和标准差。

```javascript
var data = [1, 1, 3, 5, 5];
var sum = function(x, y) {
  return x + y;
};
var square = function(x) {
  return x * x;
};

var mean = data.reduce(sum)/data.length;
var deviations = data.map(x => x - mean);
var stddev = Math.sqrt(deviations.map(square).reduce(sum)/(data.length - 1));
```

### 高阶函数

高阶函数就是操作函数的函数，它接收一个或多个函数作为参数，并返回一个新函数。举个例子：

```javascript
function not(f) {
  return function () {
    var result = f.apply(this, arguments);
    return !result;
  };
}

// 判断x是否为偶数的函数
var even = function(x) {
  return x % 2 === 0;
};

var odd = not(even);            // 一个新函数，所做的事情和even()相反
[1, 1, 3, 5, 5].every(odd);     // true，每个函数都是奇数

```

函数`not()`是个高阶函数，因为它返回一个新的函数，这个新函数将它的实参传入`f()`，并返回`f`的返回值的逻辑非。

## References

1. [Function.prototype.call()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

** 声明：**本文整理取自 [景庄](http://wwsun.github.com)
