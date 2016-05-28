---
title: V8 团队眼中的 ES6、ES7及未来
date: 2016-05-16 20:51:00
tags: 文章精选
---

![](http://7xsgf8.com1.z0.glb.clouddn.com/image/2bb5550b48674647bb5f.png)


       V8 团队眼中的 ES6、ES7及未来

<!--more-->


[月影](http://www.zcfy.cc/@akira)推荐并翻译，完成于2016年05月09日16:52:24 

    原文：http://www.zcfy.cc/article/236



<p>V8团队致力于让 JavaScript 演变成一门表达能力强，定义明确，更容易开发高效、安全、正确的Web应用的编程语言。2015年6月，<a href="http://www.ecma-international.org/ecma-262/6.0/">ES6规范</a> 经由TC39标准委员会的批准，成为 JavaScript 语言版本的一次最大的升级。这次升级为 JavaScript 带来了许多新特新包括 <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes">classes</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions">arrow functions</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">promises</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators">iterators / generators</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy">proxies</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#Well-known_symbols">well-known symbols</a> 和一些额外的语法糖。TC39标准委员会也加快了新规范定稿的节奏并于2016年2月发布了ES7的草案，该草案预计将在今年夏天定稿。由于发布周期较短，与ES6相比，ES7并没有增加太多的新特性，比较引人注意的是它增加了 <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Exponentiation">乘方运算符</a> 和 <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes">Array.prototype.includes( )</a>。</p>
<p>今天，JavaScript 引擎发展到了一个重要的里程碑：V8 支持了 ES6 和 ES7。你可以通过安装 Chrome Canary 版本（Chrome 金丝雀版，一个比 Dev 还要更新得更快的版本 —— 译者注）使用这些新的语言特性，而这些新特性将在 Chrome 52 正式版中默认支持。</p>
<p>由于规范在不断演变，Web兼容性、实现一致性等各种复杂，使得决定什么特性在哪个 JavaScript 引擎版本被充分支持成为个难题。接下来我们讨论为什么引擎考虑对规范的支持相比于升级版本号要复杂得多，为什么尾调用优化到目前为止依然在讨论中，以及还有什么附加工作还在进行中。</p>
<h2 id="-">演变的规范</h2>
<p>当TC39标准委员会决定加快升级 JavaScript 的脚步后，JavaScript 语言的最新版本成为了非正式的草稿版本。虽然 ECMAScript 规范以年为周期提出草稿和正式发布，但 V8 引擎不仅仅实现了最新的正式版本（例如：ES6），还包括一些已经几乎成为标准，不会再有大的变化，实现足够安全（以后应该不会再大改）的特性（例如：乘方运算符和Array.prototype.includes()从ES7草稿中实现）。V8引擎遵循的一个基本的原则是，<strong>浏览器中的语言特性实现要遵守现有规范，或者至少是即将成为的规范</strong>。事实上，实现一个正式版本的语言规范的过程涵盖了对一些特性的修正和完善，这些修正许多会被包含到下一版本的 ECMAScript 规范中去。</p>
<p><img src="https://dn-h5jun.qbox.me/matrix/jA-cmQYT3xgKcoY6-HujvMK9.png" alt=""></p>
<p>&gt;如图：当前实现的特性中包含部分还在进展中的规范</p>
<p>举一个现实的例子，如果我们要实现 ES6 规范里规定的正则表达式的<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky">粘滞匹配</a>，V8引擎团队发现这个新规范一旦支持将使得许多之前正常的网站出现错误（比如那些使用了<a href="(https://www.npmjs.com/package/xregexp">XRegExp</a>这个流行的npm库的网站全不好使了）。由于保证兼容性是web的重要考量，V8和Safari JavaScriptCore团队的工程师们提出了一个<a href="https://github.com/tc39/ecma262/pull/511">修正案</a>给正则表达式规范来防止之前的网站出错，这个修正得到TC39标准委员会的认同。这个修正案预计在ES8中由TC39标准委员会正式提出，但它已然成为ECMAScript语言的一部分，V8引擎已经实现了它。</p>
<p>语言规范的不断细化意味着每一个版本（包括仍在评估中的草案）不断修正和完善之前的版本，引擎的升级表面上在不断支持 ES6 和 ES7 特性，事实上底下的工作非常复杂。不考虑实际情况仅根据语言规范一刀切是不可能的，可能对 V8 引擎最恰当的描述是，V8 的实现遵循“<em>尽可能贴近未来ECMAScript标准</em>”这一原则。</p>
<h2 id="-">衡量一致性</h2>
<p>有许多方法可以衡量JavaScript引擎对ECMAScript标准的兼容性，从而评估实现该规范有多复杂。V8团队，以及其他浏览器厂商，使用 <a href="https://github.com/tc39/test262">test262 测试用例</a>作为持续保持与未来 ECMAScript 标准草案相一致的金科玉律。这组测试用例随着规范持续升级，并提供超过 16000 个单元测试，用来充分测试所有的语言特性，涵盖了边界条件。当前 V8 引擎通过了大约 98% 的测试用例，剩下的 2% 之所以没通过是因为有少数边界情况以及有部分还没有准备好支持的特性。</p>
<p>由于test262用例数目非常庞大，浏览结果成本也很高，所以还可以考虑其他可选方案，例如检查<a href="http://kangax.github.io/compat-table/es6/">Kangax compatibility table</a>。kangax 整理的兼容性速查表能够非常方便地查看一个特性是否被特定浏览器引擎实现（比如<a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions">箭头函数</a>），但是Kangax表没有充分测试所有的边界条件。目前为止，Chrome Canary 版本在Kangax表上支持了 98% 的 ES6 规范和 100% 的Kangax表列出的 ES7 规范（例如，在表上在ESnext tab页中标记为“2016特性”和“2016杂项”的部分）。</p>
<p>Kangax ES6兼容表剩余的2%测试是关于<a href="http://www.2ality.com/2015/06/tail-call-optimization.html">尾调用优化</a>，这个特性其实在V8引擎中已经实现了，但是特意在 Chrome Canary 版本中关闭了，具体关闭这个特性的原因和开发体验有关，下面会详细说。如果想要把这个特性加上，可以在设置里面把“实验的JavaScript特性”选项开启，这样就可以强制打开这个特性，那样 Canary 就完全支持Kangax表上的ES6规范了。</p>
<h2 id="-">尾调用优化</h2>
<p>尾调用优化已经被实现但是没有在特性中默认支持的理由目前<a href="https://github.com/tc39/proposal-ptc-syntax">正在TC39标准委员会中讨论</a>。ES6规范要求在严格模式下，函数尾调用不会出现堆栈溢出。这对某些编程范式是非常有用的（例如函数式编程——译者注），然而现在的实现方式有两个问题。首先，由于引擎消除尾递归是隐式的，函数是否符合尾调用而被消除了尾递归<a href="http://www.2ality.com/2015/06/tail-call-optimization.html#checking_whether_a_function_call_is_in_a_tail_position">很难被程序员自己辨别</a>。这意味着开发者可能很难发现一些死的递归，如果它们恰好出现在末尾，因为这些递归的堆栈将不再溢出。其次，尾调用优化要求除掉尾调用执行时的调用堆栈，这将导致执行流中的堆栈信息丢失。这又进一步导致了两个后果：</p>
<ol>
<li>这使得调试过程中信息更加难懂，因为堆栈不连续。</li>
<li><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/Stack">Error.prototype.stack</a> 包含的执行流信息不完整可能会导致依赖于这些错误信息的收集分析用户端信息的一些遥测软件出错。</li>
</ol>
<p>实现一个<a href="https://bugs.webkit.org/attachment.cgi?id=274472&amp;action=review">影子堆栈</a>可以改善堆栈信息缺失问题，但是V8引擎和开发者工具团队还是认为当堆栈信息在调试过程中是完全确定的，并始终符合实际虚拟机堆栈的真实状态时，调试是最容易，最可靠和最准确的。何况，影子堆栈功能如果默认开启，会带来很大的性能开销。</p>
<p>基于以上原因，V8团队强烈建议用特殊的语法来指定尾递归优化。TC39标准委员会有一个<a href="https://github.com/tc39/proposal-ptc-syntax">还没有结论的提案</a>叫做从语法上指定尾部调行为，这个提案由来自 Mozilla 和微软的委员提出。我们已经准备好了ES6的尾递归优化实现，我们也开始实现基于这一提案的尾调用优化语法。我们计划在下一次TC39会议中解决这一问题，从而决定究竟默认支持隐式尾调用优化还是显示使用尾调用优化语法。你可以测试每一种实现，通过 V8 启动参数 --harmony-tailcalls 和 --harmony-explicit-tailcalls。</p>
<h2 id="-">模块化</h2>
<p>ES6中最激动人心的承诺是 JavaScript 模块将支持通过名字空间来组织和区分不同的子系统。ES6 <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import">import 规范</a> 和 <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export">export 规范</a> 声明了模块，但是并没有说明在一个JavaScript程序中该如何加载模块。在浏览器中，最新的模块加载行为是通过<a href="https://blog.whatwg.org/js-modules">新标签</a>来指定。尽管还需要额外的标准化工作来支持高级的动态模块加载API，Chromium已经<a href="https://groups.google.com/a/chromium.org/forum/#!msg/blink-dev/uba6pMr-jec/tXdg6YYPBAAJ">开始着手</a>支持模块化的script标签了。你可以在 <a href="https://bugs.chromium.org/p/v8/issues/detail?id=1569">launch bug</a> 关注我们的实现工作，在 <a href="https://github.com/whatwg/loader">whatwg/loader</a> 仓库了解更多关于实验的模块加载API的具体思路。</p>
<h2 id="esnext-">ESnext 未来</h2>
<p>可以预见在将来 ECMAScript 升级会变得更频繁而细碎。V8 团队已经开始实现更新的特性比如 <a href="https://github.com/tc39/ecmascript-asyncawait">async /await</a> 关键字, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values">Object.values( )</a> / <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries">Object.entries( )</a>, <a href="http://tc39.github.io/proposal-string-pad-start-end/">String.prototype.padStart( ) /String.prototype.padEnd( )</a> 以及 <a href="http://v8project.blogspot.com/2016/02/regexp-lookbehind-assertions.html">RegExp lookbehind</a> 等等，同时我们也经常检查ESnext实现进展和针对现有的ES6和ES7做性能优化。</p>
<p>我们力争继续推动 JavaScript 的演变，以及在尽早实现新特性和保持现有Web兼容和稳定性之间力求平衡，向TC39标准委员会提出设计问题和实现反馈。我们期待着看这些新特性能为开发者们带来不同寻常的美妙体验。</p>