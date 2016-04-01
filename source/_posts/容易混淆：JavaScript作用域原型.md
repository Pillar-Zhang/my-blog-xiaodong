---
title: JavaScript作用域原型
date: 2016-03-10 16:04:01
tags: 知识点
---

![人海][4]

	javascript中的的函数运行在他们被定义的作用域里，而不是他们被执行的作用域里
<!--more-->
## 问题的提出  ##

首先看一个例子:

	var name = 'laruence';
		function echo() {
	     alert(name);
	     var name = 'eve';
	     alert(name);
	     alert(age);
	  }
	     echo();

运行结果是什么呢?
上面的问题, 我相信会有很多人会认为是:

    laruence   eve   [脚本出错]

因为会以为在echo中, 第一次alert的时候, 会取到全局变量name的值, 而第二次值被局部变量name覆盖, 所以第二次alert是’eve’. 而age属性没有定义, 所以脚本会出错.
但其实, 运行结果应该是:


					undefined
					eve
					[脚本出错]

 为什么呢?

## JavaScript的作用域链 ##

首先让让我们来看看Javasript(简称JS, 不完全代表JScript)的作用域的原理: JS权威指南中有一句很精辟的描述:　”JavaScript中的函数运行在它们被定义的作用域里,而不是它们被执行的作用域里.”　
为了接下来的知识, 你能顺利理解, 我再提醒一下, 在JS中:”一切皆是对象, 函数也是”.
在JS中，作用域的概念和其他语言差不多， 在每次调用一个函数的时候 ，就会进入一个函数内的作用域，当从函数返回以后，就返回调用前的作用域.
JS的语法风格和C/C++类似, 但作用域的实现却和C/C++不同，并非用“堆栈”方式，而是使用列表，具体过程如下(ECMA262中所述):
任何执行上下文时刻的作用域, 都是由作用域链(scope chain, 后面介绍)来实现.
在一个函数被定义的时候, 会将它定义时刻的scope chain链接到这个函数对象的[[scope]]属性.
在一个函数对象被调用的时候，会创建一个活动对象(也就是一个对象), 然后对于每一个函数的形参，都命名为该活动对象的命名属性, 然后将这个活动对象做为此时的作用域链(scope chain)最前端, 并将这个函数对象的[[scope]]加入到scope chain中.
看个例子:

		var func = function(lps, rps){
		          var name = 'laruence';
		          ........
		     }
		     func();
在执行func的定义语句的时候, 会创建一个这个函数对象的[[scope]]属性(内部属性,只有JS引擎可以访问, 但FireFox的几个引擎(SpiderMonkey和Rhino)提供了私有属性__parent__来访问它), 并将这个[[scope]]属性, 链接到定义它的作用域链上(后面会详细介绍), 此时因为func定义在全局环境, 所以此时的[[scope]]只是指向全局活动对象window active object.
在调用func的时候, 会创建一个活动对象(假设为aObj, 由JS引擎预编译时刻创建, 后面会介绍)，并创建arguments属性, 然后会给这个对象添加俩个命名属性aObj.lps, aObj.rps; 对于每一个在这个函数中申明的局部变量和函数定义, 都作为该活动对象的同名命名属性.
然后将调用参数赋值给形参数，对于缺少的调用参数，赋值为undefined。
然后将这个活动对象做为scope chain的最前端, 并将func的[[scope]]属性所指向的,定义func时候的顶级活动对象, 加入到scope chain.
有了上面的作用域链, 在发生标识符解析的时候, 就会逆向查询当前scope chain列表的每一个活动对象的属性，如果找到同名的就返回。找不到，那就是这个标识符没有被定义。
注意到, 因为函数对象的[[scope]]属性是在定义一个函数的时候决定的, 而非调用的时候, 所以如下面的例子:

		 var name = 'laruence';
		     function echo() {
		          alert(name);
		     }

		     function env() {
		          var name = 'eve';
		          echo();
		     }

		     env();

运行结果是:

	laruence
结合上面的知识, 我们来看看下面这个例子:

		function factory() {
		     var name = 'laruence';
		     var intro = function(){
		          alert('I am ' + name);
		     }
		     return intro;
		}

		function app(para){
		     var name = para;
		     var func = factory();
		     func();
		}

		app('eve');

当调用app的时候, scope chain是由: {window活动对象(全局)}->{app的活动对象} 组成.  在刚进入app函数体时, app的活动对象有一个arguments属性, 俩个值为undefined的属性: name和func.    和一个值为’eve’的属性para;
此时的scope chain如下:

		[[scope chain]] = [
		{
		     para : 'eve',
		     name : undefined,
		     func : undefined,
		     arguments : []
		}, {
		     window call object
		}
		]


当调用进入factory的函数体的时候, 此时的factory的scope chain为:

		[[scope chain]] = [
		{
		     name : undefined,
		     intor : undefined
		}, {
		     window call object
		}
		]

注意到, 此时的作用域链中, 并不包含app的活动对象.
在定义intro函数的时候, intro函数的[[scope]]为:

			[[scope chain]] = [
			{
			     name : 'laruence',
			     intor : undefined
			}, {
			     window call object
			}
			]

从factory函数返回以后,在app体内调用intor的时候, 发生了标识符解析, 而此时的sope chain是:

		[[scope chain]] = [
		{
		     intro call object
		}, {
		     name : 'laruence',
		     intor : undefined
		}, {
		     window call object
		}
		]
因为scope chain中,并不包含factory活动对象. 所以, name标识符解析的结果应该是factory活动对象中的name属性, 也就是’laruence’.

所以运行结果是:

		I am laruence

现在, 大家对”JavaScript中的函数运行在它们被定义的作用域里,而不是它们被执行的作用域里.”这句话, 应该有了个全面的认识了吧?

JavaScript中没有块级作用域，例如：

		if(true){
			var color="blue";
		}
		alert(color);          //"blue"
这里的实在一个if语句中定义了变量color。如果在别的语言中color会在if语句执行完毕后被销毁。但是在JavaScript中，if语句中的变量声明会被添加到当前的执行环境中去（这里是全局的执行环境）类似的还有for


## 声明变量
 使用Var声明的变量会被自动被添加到最近的执行环境中。如果初始化变量时没有使用Var声明，该变量会被自动添加到全局环境中。例如：



	function add(num1,mum2){
		var sum=num1+num2;
		return sum;
	}
	var result=add(10,20); //30
	alert(sum);			//由于sum不是有效的变量，因此会导致错误。
如果省略这个Var关键字


	function add(sum1,sum2){
		sum=num1+num2;
		return sum;
	}
	var result=add(10,20); //30
	alert(sum);    //30


[4]: http://7xsgf8.com1.z0.glb.clouddn.com/image/bghuguang.jpg



