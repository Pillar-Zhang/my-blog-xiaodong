---
title: JavaScript函数式编程
date: 2016-05-12 21:31:00
tags: JavaScript
---
![](http://7xsgf8.com1.z0.glb.clouddn.com/image/6dc60c09e1c9cb3cbfa8.png)


       JavaScript函数式编程让你的代码更有价值

<!--more-->

[月影](https://www.h5jun.com/)推荐，完成于2016年04月29日 

    原文：https://www.h5jun.com/post/functional-how-far.html
<p>函数式编程（Functional Programming）其实相对于计算机的历史而言是一个非常古老的概念，甚至早于第一台计算机的诞生。函数式编程的基础模型来源于 λ 演算，而 λ 演算并非设计于在计算机上执行，它是由 Alonzo Church 和 Stephen Cole Kleene 在 20 世纪三十年代引入的一套用于研究函数定义、函数应用和递归的形式系统。</p>
<p><img src="https://p.ssl.qhimg.com/d/inn/8aaf8340/fp1.png" alt="functional programming"></p>
<p>随着计算机语言、Web和前端的发展，JavaScript这门语言被越来越广泛地使用。作为现代编程语言的代表作之一的JavaScript，与和它具有相似性的一些现代编程语言一样，都有着函数式的某些特性，以至于 Douglas Crockford 在他写的 《JavaScript : The Good Parts》中说，JavaScript 是披着 C 外衣的 Lisp。</p>

<h2 id="toc-d35">函数式编程究竟是什么</h2>
<p>函数式编程是一种编程思想或者说编程泛型，网上的各种资料和技术书籍中都会有对它的基本定义。根据基本定义，通常情况下，函数式编程强调函数计算本身，而不是如同经典的命令式编程模型那样强调指令执行。</p>
<p>以上定义只是定义，就像<strong>面向对象编程</strong>的定义是将计算机程序用单个能够起到子程序作用的单元或对象组合而成。这种定义总是正确的，但有时候无助于我们真正理解它，因此我们还是需要通过代码来理解。</p>
<p>如同<strong>面向过程</strong>和<strong>面向对象</strong>一样，<strong>函数式编程</strong>思想体现在代码上，会带有明显的特征，以至于我们可以将这种编程思想同程序的其他部分区别开来。但这里我并不是要谈标准的、纯粹的或者规范的“函数式”风格，那是一种类似于LISP或者Scheme语言的风格，显然并不适合于JavaScript或者类似于JavaScript的现代编程语言。就像我们讨论面向对象不必强调纯粹性一样，我们在实际工作中很少用到单一的纯粹的泛型，通常情况下都是几种模型混合使用。如同面向对象和面向过程不可分割一样，函数式、面向对象和面向过程的编程泛型常常混合于我们的代码中，如果不去刻意区分它们，很难将它们彻底独立开来。</p>
<p>先来看一段简单的示例代码：</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">mul</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x * y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">concat</span>(<span class="hljs-params">arr1, arr2</span>)</span>{
    <span class="hljs-keyword">return</span> arr1.concat(arr2);
}

<span class="hljs-built_in">console</span>.log(add(<span class="hljs-number">1</span>, add(<span class="hljs-number">2</span>, <span class="hljs-number">3</span>)),     <span class="hljs-comment">//6</span>
    mul(<span class="hljs-number">1</span>, mul(<span class="hljs-number">2</span>, mul(<span class="hljs-number">3</span>, <span class="hljs-number">4</span>))),        <span class="hljs-comment">//24</span>
    concat([<span class="hljs-number">1</span>, <span class="hljs-number">2</span>], concat([<span class="hljs-number">3</span>, <span class="hljs-number">4</span>], [<span class="hljs-number">5</span>, <span class="hljs-number">6</span>]))); <span class="hljs-comment">//[1,2,3,4,5,6]</span>
</code></pre>
<p>上面的代码中，我们有 3 个简单的函数，用来计算两个数的和、积以及两个数组的连接。我们看到，当我们要对两个以上的数进行操作的时候，调用起来略显繁琐，所以我们可以对它们进行一系列修改，使得它们支持多个数连续的操作：</p>
<p><strong>版本1</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">return</span> args.reduce((x, y) =&gt; x + y);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">mul</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">return</span> args.reduce((x, y) =&gt; x * y);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">concat</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">return</span> args.reduce((arr1, arr2) =&gt; arr1.concat(arr2));
}

<span class="hljs-built_in">console</span>.log(add(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>),     <span class="hljs-comment">//6</span>
    mul(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>),        <span class="hljs-comment">//24</span>
    concat([<span class="hljs-number">1</span>, <span class="hljs-number">2</span>], [<span class="hljs-number">3</span>, <span class="hljs-number">4</span>], [<span class="hljs-number">5</span>, <span class="hljs-number">6</span>])); <span class="hljs-comment">//[1,2,3,4,5,6]</span>
</code></pre>
<p>上面的代码通过对三个函数的分别修改，将它们改造成了支持多个操作数的版本，实现了我们简化函数调用的目的。不过，这不是一种足够聪明的做法，因为如果我们还有更多类似函数，我们还得对这些函数进行一一的改写。</p>
<p>注意到，我们完全可以一次性抽取出一个通用的版本 <code>reduce</code> ，它的作用是当任意两个参数的函数如果传入多余2个函数时，用这些参数进行 reduce 迭代：</p>
<p><strong>版本二</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">reduce</span>(<span class="hljs-params">fn, ...args</span>)</span>{
    <span class="hljs-keyword">return</span> args.reduce(fn);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">mul</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x * y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">concat</span>(<span class="hljs-params">arr1, arr2</span>)</span>{
    <span class="hljs-keyword">return</span> arr1.concat(arr2);
}

<span class="hljs-built_in">console</span>.log(reduce(add, <span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>),     <span class="hljs-comment">//6</span>
    reduce(mul, <span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>),        <span class="hljs-comment">//24</span>
    reduce(concat, [<span class="hljs-number">1</span>, <span class="hljs-number">2</span>], [<span class="hljs-number">3</span>, <span class="hljs-number">4</span>], [<span class="hljs-number">5</span>, <span class="hljs-number">6</span>])); <span class="hljs-comment">//[1,2,3,4,5,6]</span>
</code></pre>
<p>上面的代码好处是显而易见，我们不再需要对 add、mul、concat 分别进行修改，并且 <code>reduce</code> 可以应用在任何一个两个参数的函数上。不过呢，我们还是修改了函数调用的过程，改变了调用的函数和参数。然而，我们可以进一步进行优化：</p>
<p><strong>版本三</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">reduce</span>(<span class="hljs-params">fn, ...args</span>)</span>{
    <span class="hljs-keyword">return</span> args.reduce(fn);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">mul</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x * y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">concat</span>(<span class="hljs-params">arr1, arr2</span>)</span>{
    <span class="hljs-keyword">return</span> arr1.concat(arr2);
}

add = reduce.bind(<span class="hljs-literal">null</span>, add);
mul = reduce.bind(<span class="hljs-literal">null</span>, mul);
concat = reduce.bind(<span class="hljs-literal">null</span>, concat);

<span class="hljs-built_in">console</span>.log(add(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>),     <span class="hljs-comment">//6</span>
    mul(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>),        <span class="hljs-comment">//24</span>
    concat([<span class="hljs-number">1</span>, <span class="hljs-number">2</span>], [<span class="hljs-number">3</span>, <span class="hljs-number">4</span>], [<span class="hljs-number">5</span>, <span class="hljs-number">6</span>])); <span class="hljs-comment">//[1,2,3,4,5,6]</span>
</code></pre>
<p>在这里，我们利用 reduce 和 <code>bind</code> 方法改变了 add、mul 和 concat。当然我们这么做带来一个小小的副作用，那就是 this 被限定为 null。我们还有更好的做法：</p>
<p><strong>版本四</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">reduce</span>(<span class="hljs-params">fn</span>)</span>{
    <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
        <span class="hljs-keyword">return</span> args.reduce(fn.bind(<span class="hljs-keyword">this</span>));
    }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">mul</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x * y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">concat</span>(<span class="hljs-params">arr1, arr2</span>)</span>{
    <span class="hljs-keyword">return</span> arr1.concat(arr2);
}

add = reduce(add);
mul = reduce(mul);
concat = reduce(concat);

<span class="hljs-built_in">console</span>.log(add(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>),     <span class="hljs-comment">//6</span>
    mul(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>),        <span class="hljs-comment">//24</span>
    concat([<span class="hljs-number">1</span>, <span class="hljs-number">2</span>], [<span class="hljs-number">3</span>, <span class="hljs-number">4</span>], [<span class="hljs-number">5</span>, <span class="hljs-number">6</span>])); <span class="hljs-comment">//[1,2,3,4,5,6]</span>
</code></pre>
<p>在这个版本中，我们干脆让 reduce 返回 function，实际上我们相当于让 reduce 对函数进行了变换。我们可以这样理解，函数变换基于原始函数包装了一个新的函数，新的函数在原始函数的基础上具备某些增强的能力。在这里我们得到了一颗<strong>函数式编程</strong>的“糖果”——我们可以像操作数据那样操作一组函数，使得这些函数具备某些新的能力。而这，就是<strong>过程抽象</strong>的基本思想。这些变换函数的函数我们称之为<strong>“高阶函数”</strong>。</p>
<p><img src="https://dn-h5jun.qbox.me/matrix/t8Gyl7MMq6YH3owqTUufAu5p.png" alt="procedural abstraction"></p>
<p>上面的代码我们是否还能进一步优化？事实上还是可以，如果 reduce 的函数是异步的，那么我们前面的函数是处理不了的，因此我们可以再设计一下 reduce，让它支持异步函数：</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">reduce</span>(<span class="hljs-params">fn, async</span>)</span>{
  <span class="hljs-keyword">if</span>(<span class="hljs-keyword">async</span>){
    <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
      <span class="hljs-keyword">return</span> args.reduce((a, b)=&gt;{
        <span class="hljs-keyword">return</span> <span class="hljs-built_in">Promise</span>.resolve(a).then((v)=&gt;fn.call(<span class="hljs-keyword">this</span>, v, b));
      });
    }
  }<span class="hljs-keyword">else</span>{
    <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
      <span class="hljs-keyword">return</span> args.reduce(fn.bind(<span class="hljs-keyword">this</span>));
    }
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
  <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">mul</span>(<span class="hljs-params">x, y</span>)</span>{
  <span class="hljs-keyword">return</span> x * y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">concat</span>(<span class="hljs-params">arr1, arr2</span>)</span>{
  <span class="hljs-keyword">return</span> arr1.concat(arr2);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">asyncAdd</span>(<span class="hljs-params">x, y</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> <span class="hljs-built_in">Promise</span>((resolve, reject)=&gt;{
    setTimeout(()=&gt; resolve(x+y), <span class="hljs-number">100</span>); 
  });
}

add = reduce(add);
mul = reduce(mul);
concat = reduce(concat);

<span class="hljs-built_in">console</span>.log(add(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>),   <span class="hljs-comment">//6</span>
  mul(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>),    <span class="hljs-comment">//24</span>
  concat([<span class="hljs-number">1</span>, <span class="hljs-number">2</span>], [<span class="hljs-number">3</span>, <span class="hljs-number">4</span>], [<span class="hljs-number">5</span>, <span class="hljs-number">6</span>])); <span class="hljs-comment">//[1,2,3,4,5,6]</span>

asyncAdd = reduce(asyncAdd, <span class="hljs-literal">true</span>);
asyncAdd(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>).then((v)=&gt;<span class="hljs-built_in">console</span>.log(v));
</code></pre>
<h2 id="toc-bac">过程抽象的具体应用</h2>
<h3 id="toc-870"><a class="anchor" href="#toc-870"></a>函数调用的频度控制</h3>
<p>在实际项目中，我们有时候会遇到限制某函数调用频率的需求。例如，防止一个按钮短时间的的重复点击，防止 resize、scroll 和 mousemove 事件过于频繁地触发等。</p>
<p><strong>throttle</strong></p>
<p>throttle 可以限制函数调用的频率，常用来防止按钮被重复点击。</p>
<pre><code class="hljs lang-js"><span class="hljs-comment">//限制button在500ms内只能被点击一次</span>
$(<span class="hljs-string">"#btn"</span>).click(throttle(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">evt</span>)</span>{
    <span class="hljs-keyword">do</span> sth.
}, <span class="hljs-number">500</span>);
</code></pre>
<p>throttle 的简单实现</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">throttle</span>(<span class="hljs-params">fn, wait</span>)</span>{
    <span class="hljs-keyword">var</span> timer;
    <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
        <span class="hljs-keyword">if</span>(!timer){
            timer = setTimeout(()=&gt;timer=<span class="hljs-literal">null</span>, wait);
            <span class="hljs-keyword">return</span> fn.apply(<span class="hljs-keyword">this</span>, args);
        }
    }
}

<span class="hljs-comment">//按钮每500ms一次点击有效</span>
btn.onclick = throttle(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"button clicked"</span>);
}, <span class="hljs-number">500</span>);
</code></pre>
<p><strong>debounce</strong></p>
<p>有时候我们希望函数在某些操作执行完成之后被触发。例如，实现搜索框的 Suggest 效果，如果数据是从服务器端读取的，为了限制从服务器读取数据的频率，我们可以等待用户输入结束 100ms 之后再触发 Suggest 查询：</p>
<pre><code class="hljs lang-actionscript">searchBox.addEventListener(<span class="hljs-string">"input"</span>, debounce(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span></span>{
    loadSuggestion();
}, <span class="hljs-number">100</span>));
</code></pre><p>debouce 的简单实现：</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">debounce</span>(<span class="hljs-params">fn, delay</span>)</span>{
    <span class="hljs-keyword">var</span> timer = <span class="hljs-literal">null</span>;
    <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
        clearTimeout(timer);
        timer = setTimeout(() =&gt; fn.apply(<span class="hljs-keyword">this</span>, args), delay);
    }
}

<span class="hljs-built_in">window</span>.addEventListener(<span class="hljs-string">"scroll"</span>, debounce(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"scrolled"</span>);
}, <span class="hljs-number">500</span>));
</code></pre>
<h3 id="toc-63c"><a class="anchor" href="#toc-63c"></a>DOM的批量操作</h3>
<p>jQuery 的语法糖特点包括批量操作与链式调用。我们可以看一下，实际上用函数式的过程抽象思想很容易实现类似的“语法糖”。我们先考虑如何支持函数的“批量操作”。</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">multicast</span>(<span class="hljs-params">fn</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">list, ...args</span>)</span>{
    <span class="hljs-keyword">if</span>(<span class="hljs-built_in">Array</span>.isArray(list)){
      <span class="hljs-keyword">return</span> list.map((item)=&gt;fn.apply(<span class="hljs-keyword">this</span>, [item,...args]));
    }<span class="hljs-keyword">else</span>{
      <span class="hljs-keyword">return</span> fn.apply(<span class="hljs-keyword">this</span>, [list,...args]);
    }
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x + y;
}

add = multicast(add);

<span class="hljs-built_in">console</span>.log(add([<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>], <span class="hljs-number">4</span>)); <span class="hljs-comment">//5,6,7</span>
</code></pre>
<p>有了 multicast，我们来尝试用它批量操作一个 ul 中的 li 元素：</p>
<pre><code class="hljs lang-html"><span class="hljs-tag">&lt;<span class="hljs-name">ul</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>2<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>3<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>4<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>5<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>6<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-name">li</span>&gt;</span>7<span class="hljs-tag">&lt;/<span class="hljs-name">li</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-name">ul</span>&gt;</span>
</code></pre>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">multicast</span>(<span class="hljs-params">fn</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">list, ...args</span>)</span>{
    <span class="hljs-keyword">if</span>(<span class="hljs-built_in">Array</span>.isArray(list)){
      <span class="hljs-keyword">return</span> list.map((item)=&gt;fn.apply(<span class="hljs-keyword">this</span>, [item,...args]));
    }<span class="hljs-keyword">else</span>{
      <span class="hljs-keyword">return</span> fn.apply(<span class="hljs-keyword">this</span>, [list,...args]);
    }
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">setColor</span>(<span class="hljs-params">el, color</span>)</span>{
  <span class="hljs-keyword">return</span> el.style.color = color;
}

setColor = multicast(setColor);

<span class="hljs-keyword">var</span> list = <span class="hljs-built_in">document</span>.querySelectorAll(<span class="hljs-string">"li:nth-child(2n+1)"</span>);
setColor(<span class="hljs-built_in">Array</span>.from(list), <span class="hljs-string">"red"</span>);
</code></pre>
<p><a href="http://code.w3ctech.com/detail/3218">演示例子</a></p>
<h3 id="wrapperanddecorators"><a class="anchor" href="#wrapperanddecorators"></a>Wrapper and decorators</h3>
<p>我们可以对上面的例子进行进一步的处理，添加对 selector 参数的支持。这一次，我们使用一个叫做 wrap 的新的高阶函数，它可以对原始函数的参数和返回值进行再包装。</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">multicast</span>(<span class="hljs-params">fn</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">list, ...args</span>)</span>{
    <span class="hljs-keyword">if</span>(<span class="hljs-built_in">Array</span>.isArray(list)){
      <span class="hljs-keyword">return</span> list.map((item)=&gt;fn.apply(<span class="hljs-keyword">this</span>, [item,...args]));
    }<span class="hljs-keyword">else</span>{
      <span class="hljs-keyword">return</span> fn.apply(<span class="hljs-keyword">this</span>, [list,...args]);
    }
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">wrap</span>(<span class="hljs-params">fn, before, after</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">if</span>(before){
        args = before.apply(<span class="hljs-keyword">this</span>, args);
    }
    <span class="hljs-keyword">let</span> ret = fn.apply(<span class="hljs-keyword">this</span>, args);
    <span class="hljs-keyword">if</span>(after){
        ret = after.call(<span class="hljs-keyword">this</span>, [ret, ...args]);
    }
    <span class="hljs-keyword">return</span> ret;
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">setColor</span>(<span class="hljs-params">el, color</span>)</span>{
  <span class="hljs-keyword">return</span> el.style.color = color;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">setFontSize</span>(<span class="hljs-params">el, size</span>)</span>{
  <span class="hljs-keyword">return</span> el.style.fontSize = size;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">cast</span>(<span class="hljs-params">fn</span>)</span>{
  <span class="hljs-keyword">return</span> wrap(multicast(fn), (...args)=&gt;{
      <span class="hljs-keyword">if</span>(<span class="hljs-keyword">typeof</span> args[<span class="hljs-number">0</span>] === <span class="hljs-string">"string"</span>){
          args[<span class="hljs-number">0</span>] = <span class="hljs-built_in">Array</span>.from(<span class="hljs-built_in">document</span>.querySelectorAll(args[<span class="hljs-number">0</span>]));
      }
      <span class="hljs-keyword">return</span> args;
  });
}

[setColor, setFontSize] = multicast(cast)([setColor, setFontSize]);

setColor(<span class="hljs-string">"li:nth-child(2n+1)"</span>, <span class="hljs-string">"red"</span>);

setFontSize(<span class="hljs-string">"li:nth-child(2n+1)"</span>, <span class="hljs-string">"32px"</span>);
</code></pre>
<p><a href="http://code.w3ctech.com/detail/3219">演示例子</a></p>
<p>接下来，我们继续对上面的代码进行优化，我们可以构造一个高阶函数 zip，它结合面向对象和过程抽像：</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">zip</span>(<span class="hljs-params">props</span>)</span>{
  <span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">Class</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">this</span>.__args = args;
  }
  <span class="hljs-keyword">let</span> keys = <span class="hljs-built_in">Object</span>.keys(props);
  keys.forEach((key) =&gt; {
    Class.prototype[key] = <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
      <span class="hljs-keyword">return</span> props[key].apply(<span class="hljs-keyword">this</span>, [...this.__args, ...args]);
    };
  });
  <span class="hljs-keyword">return</span> (...args)=&gt;<span class="hljs-keyword">new</span> Class(...args);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
  <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">sub</span>(<span class="hljs-params">x, y</span>)</span>{
  <span class="hljs-keyword">return</span> x - y;
}

<span class="hljs-keyword">let</span> N = zip({add, sub});

<span class="hljs-built_in">console</span>.log(N(<span class="hljs-number">9</span>).add(<span class="hljs-number">8</span>)); <span class="hljs-comment">//17</span>

<span class="hljs-built_in">console</span>.log(N(<span class="hljs-number">3</span>).sub(<span class="hljs-number">5</span>)); <span class="hljs-comment">//-2</span>
</code></pre>
<p>有了 zip，我们就可以实现链式调用：</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">zip</span>(<span class="hljs-params">props</span>)</span>{
  <span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">Class</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">this</span>.__args = args;
  }
  <span class="hljs-keyword">let</span> keys = <span class="hljs-built_in">Object</span>.keys(props);
  keys.forEach((key) =&gt; {
    Class.prototype[key] = <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
      <span class="hljs-keyword">return</span> props[key].apply(<span class="hljs-keyword">this</span>, [...this.__args, ...args]);
    };
  });
  <span class="hljs-keyword">return</span> (...args)=&gt;<span class="hljs-keyword">new</span> Class(...args);
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">multicast</span>(<span class="hljs-params">fn</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">list, ...args</span>)</span>{
    <span class="hljs-keyword">if</span>(<span class="hljs-built_in">Array</span>.isArray(list)){
      <span class="hljs-keyword">return</span> list.map((item)=&gt;fn.apply(<span class="hljs-keyword">this</span>, [item,...args]));
    }<span class="hljs-keyword">else</span>{
      <span class="hljs-keyword">return</span> fn.apply(<span class="hljs-keyword">this</span>, [list,...args]);
    }
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">wrap</span>(<span class="hljs-params">fn, before, after</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">if</span>(before){
    args = before.apply(<span class="hljs-keyword">this</span>, args);
    }
    <span class="hljs-keyword">let</span> ret = fn.apply(<span class="hljs-keyword">this</span>, args);
    <span class="hljs-keyword">if</span>(after){
        ret = after.apply(<span class="hljs-keyword">this</span>, [ret, ...args]);
    }
    <span class="hljs-keyword">return</span> ret;
  }
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">setColor</span>(<span class="hljs-params">el, color</span>)</span>{
  <span class="hljs-keyword">return</span> el.style.color = color;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">setFontSize</span>(<span class="hljs-params">el, size</span>)</span>{
  <span class="hljs-keyword">return</span> el.style.fontSize = size;
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">cast</span>(<span class="hljs-params">fn</span>)</span>{
  <span class="hljs-keyword">return</span> wrap(multicast(fn), (...args)=&gt;{
      <span class="hljs-keyword">if</span>(<span class="hljs-keyword">typeof</span> args[<span class="hljs-number">0</span>] === <span class="hljs-string">"string"</span>){
          args[<span class="hljs-number">0</span>] = <span class="hljs-built_in">Array</span>.from(<span class="hljs-built_in">document</span>.querySelectorAll(args[<span class="hljs-number">0</span>]));
      }
      <span class="hljs-keyword">return</span> args;
  }, (ret,...args)=&gt;$(args[<span class="hljs-number">0</span>]));
}

[setColor, setFontSize] = multicast(cast)([setColor, setFontSize]);

<span class="hljs-keyword">let</span> $ = zip({setColor, setFontSize});

$(<span class="hljs-string">"li:nth-child(2n+1)"</span>).setColor(<span class="hljs-string">"red"</span>).setFontSize(<span class="hljs-string">"32px"</span>);
</code></pre>
<p><a href="http://code.w3ctech.com/detail/3220">演示例子</a></p>
<p>通过上面的例子，我们体会一下函数式编程的基本思想：</p>
<ul>
<li>设计高阶函数：操作函数的函数，例如例子中的 multicast、wrap、cast、zip</li>
<li>高阶函数之间可以组合调用，例如 cast 调用 wrap， wrap 调用 multicast，cast 后的函数再被 zip 调用。组合调用可以给函数扩展出复杂的功能。</li>
</ul>
<h3 id="toc-e7c"><a class="anchor" href="#toc-e7c"></a>防御性编程</h3>
<p>我们还可以使用上面的 wrap 高阶函数进行防御性编程：</p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">wrap</span>(<span class="hljs-params">fn, before, after</span>)</span>{
  <span class="hljs-keyword">return</span> <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">...args</span>)</span>{
    <span class="hljs-keyword">if</span>(before){
    args = before.apply(<span class="hljs-keyword">this</span>, args);
    }
    <span class="hljs-keyword">let</span> ret = fn.apply(<span class="hljs-keyword">this</span>, args);
    <span class="hljs-keyword">if</span>(after){
        ret = after.call(<span class="hljs-keyword">this</span>, [ret, ...args]);
    }
    <span class="hljs-keyword">return</span> ret;
  }
}

<span class="hljs-built_in">Object</span>.defineProperty(<span class="hljs-built_in">window</span>, <span class="hljs-string">"ERROR_IF_MISSING"</span>, {
  get: <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> <span class="hljs-built_in">TypeError</span>(<span class="hljs-string">"missing parameter"</span>)
  },
  writeable: <span class="hljs-literal">false</span>
});


<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">add</span>(<span class="hljs-params">x, y</span>)</span>{
    <span class="hljs-keyword">return</span> x + y;
}

<span class="hljs-keyword">var</span> add = wrap(add, 
          (x = ERROR_IF_MISSING, y = ERROR_IF_MISSING)=&gt;[x, y]);

<span class="hljs-comment">//Uncaught TypeError: missing parameter</span>
<span class="hljs-built_in">console</span>.log(add());
</code></pre>
<p><a href="http://code.w3ctech.com/detail/3222">演示例子</a></p>
<p>上面的代码如果不给 x 或 y 赋值，就会强制抛出一个 TypeError。</p>
<h2 id="toc-25f">总结</h2>
<p>我们说<strong>函数式编程</strong>是一种<strong>编程思想</strong>或者<strong>编程范式</strong>，上面的例子演示了函数式编程思想本身的基本应用场景。其实不管是号称支持函数式编程的 <a href="lodash.com">lodash</a>、<a href="underscorejs.org">underscore</a> 或者更强大一些的 <a href="https://github.com/ramda/ramda">ramdajs</a> 库，它们的基本原理和使用场景也都包括上面的这些点。</p>
<p>通过上面的讨论我们还可以得出结论，函数式编程拥有强大的抽象能力，也正是因为抽象能力强，函数式编程的模型才拥有巨大的潜力。</p>
<p>函数式编程是程序设计范式的一种，就像面向对象编程一样，它是我们解决问题可以选择的模式和思路，它和命令式编程（面向过程、面向对象）之间并不意味着非此即彼的选择，而是可以并存。所以，关键问题不在于函数式编程实不实用，而在于学习一种新的思考模式，这种思考模式能够帮助我们更深入理解程序设计原理和本质，深入了解函数式编程的优点和缺点，从而写出更通用抽象能力更强质量更好的代码。</p>
