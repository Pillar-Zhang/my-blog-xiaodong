---
title: 编写可维护的 JavaScript
date: 2016-05-26 20:51:00
tags: 文章精选
---

![](http://7xsgf8.com1.z0.glb.clouddn.com/image/c7068379619dcada5826.png)


       编写可维护的 JavaScript

<!--more-->

寻找头绪：编写可维护的 JavaScript

[月影](http://www.zcfy.cc/@akira)推荐并翻译，完成于2016年05月24日16:08:19 

    原文：http://www.zcfy.cc/article/312
<p>几乎每个程序员都有接手维护别人遗留项目的经历。或者，有可能一个老项目某一天又被重新启动。通常情况下，接手老项目都会让人恨不得抛弃掉整个代码库从头开始。老代码凌乱、文档缺失、需要研究很多天才能完全搞明白它。然而，通过合适的规划、分解和好的工作流，项目代码可以变得干净、有组织和可扩展。</p>
<p>我曾经接手清理许多项目的代码，让我不得不重头开始的项目真心不多，不过我最近就遇到了一个。我从中学到了很多关于 JavaScript 代码组织的内容，以及最重要的是冷静，不要为你的前任抓狂。在这篇文章里，我想要让你知道我是怎么一步步处理项目代码的，告诉你我的经验。</p>
<h2 id="-">分析项目</h2>
<p>第一步是先概览整个项目，弄明白问题所在。如果这是一个网站，通过点击测试所有功能点：打开模块、提交表单以及其他的。在做这件事的时候，打开开发者工具，看看是否有任何报错，看看控制台有没有日志。如果这是一个Node.js项目，打开<a href="https://en.wikipedia.org/wiki/Command-line_interface">命令行界面</a>然后检查各个API。最好的情况是项目通过统一的入口（例如：<code>main.js</code>, <code>index.js</code>, <code>app.js</code>, ……）来初始化所有的模块，最差的情况是整个业务逻辑散落于各处。</p>
<p>搞清楚项目采用了哪些工具。是 <a href="https://jquery.com/">jQuery</a>、<a href="https://facebook.github.io/react/">React</a> 或是 <a href="http://expressjs.com/">Express</a>？将所有重要的信息整理在一个清单里。假设这个项目是用 <a href="https://angular.io/">Angular 2</a> 写的，你之前对 Angular 2 不熟悉，先去查看文档，对它有一个初步的了解，并寻找最佳实践的范例。</p>
<h3 id="-">从更高层面上理解项目</h3>
<p>了解技术点是一个好开端，但要进一步深入理解，我们需要看一看它的<a href="https://en.wikipedia.org/wiki/Unit_testing"><strong>单元测试</strong></a>。单元测试通过测试代码的功能函数与方法来保证代码如预期的那样运行。不同于仅仅阅读代码，查看和运行单元测试能让你更加深入理解代码的期望运行结果。如果接手的项目没写单元测试，没关系，我们可以自己来写。</p>
<h2 id="-">创建基线</h2>
<p>这样做是为了确立一致性。你已经了解了项目的工具链、代码结构、模块的逻辑关系，现在该为项目创建基线了。我推荐添加一个 <a href="http://editorconfig.org/"><code>.editorconfig</code></a> 文件让代码风格在不同的编辑器、IED和不同的开发者之间保持一致。</p>
<h3 id="-">一致的缩进</h3>
<p>使用 tab 还是空格来缩进是一个<a href="http://programmers.stackexchange.com/questions/57/tabs-versus-spaces-what-is-the-proper-indentation-character-for-everything-in-e">老问题</a> ，常引发程序员争论不休，不过没关系，不管项目用的是空格还是 tab，继续使用之前的就好了。除非代码库既有空格缩进又有 tab 缩进的代码，那就只好在两者中做出一个取舍。每个人都可以保持自己的观点，但一个好的项目要保证所有的开发者都可以无争议地协同工作。</p>
<p>为什么这个很重要？因为每一个人都有自己使用编辑器或 IDE 的习惯。比如，我是个<a href="https://en.wikipedia.org/wiki/Code_folding">代码折叠</a>控，要是编辑器没有正确的代码折叠功能，我整个人都会迷失在文件里。如果代码的缩进不一致，就会影响到折叠功能，因此每一次我打开一个文件，不得不先修复那些缩进然后才能开始工作，这十分浪费时间。</p>
<pre><code>// 虽然这段 JavaScript 代码是合法的, 但这段代码块没办法正常折叠
// 因为这段代码的缩进不一致
 function foo (data) {
  let property = String(data);

if (property === &#39;bar&#39;) {
   property = doSomething(property);
  }
  //... more logic.
 }

// 修复缩进后，这段代码才可以被正确折叠
// 从而获得更好的编码体验和更整洁的代码库
function foo (data) {
 let property = String(data);

 if (property === &#39;bar&#39;) {
  property = doSomething(property);
 }
 //... more logic.
}
</code></pre><h3 id="-">命名</h3>
<p>确保项目中使用命名约定是值得推崇的做法。<a href="https://en.wikipedia.org/wiki/CamelCase">驼峰命名</a>通常在 JavaScript 代码中使用，但是我看到过许多不一致的命名。例如：jQuery 项目经常会有代码在命名上混淆 jQuery 对象和其他对象。</p>
<pre><code>// 不一致的命名让代码变得难以检查和理解
// 它还会误导维护者
const $element = $(&#39;.element&#39;);

function _privateMethod () {
  const self = $(this);
  const _internalElement = $(&#39;.internal-element&#39;);
  let $data = element.data(&#39;foo&#39;);
  //... more logic.
}

// 这样改就更易于理解了
const $element = $(&#39;.element&#39;);

function _privateMethod () {
  const $this = $(this);
  const $internalElement = $(&#39;.internal-element&#39;);
  let elementData = $element.data(&#39;foo&#39;);
  //... more logic.
}
</code></pre><h3 id="-">代码检查</h3>
<p>之前所做的一切是在美化代码，主要是让它变得更易于检查。接下来我们介绍保证代码质量的通用最佳实践。<a href="http://eslint.org/">ESLint</a>，<a href="http://www.jslint.com/">JSLint</a>，还有 <a href="http://jshint.com/">JSHint</a> 是目前最受欢迎的三个 JavaScript 代码检查工具。我个人用 JSHint 最多，但我现在最喜欢 ESLint，主要是因为它可以自定义规则，也较早地支持了 ES2015。</p>
<p>一旦你开始代码检查，如果有很多错误信息出现，立即修复它们。别跳过这些步骤，直到你的代码检查工具对你的代码彻底满意了。</p>
<h3 id="-">升级依赖</h3>
<p>升级依赖需要仔细些，如果你不注意依赖本身的升级带来的一些变化，就很容易导致错误。一些项目可能只能依赖某些库的固定版本（例如：<code>v1.12.5</code>），而另一些则使用版本通配符（例如：<code>v1.12.x</code>）。如果你要快速升级依赖，你需要知道依赖模块的版本号通常按如下规则建立：<code>主版本.小版本.补丁</code>。如果你对 <a href="http://semver.org/">semantic versioning</a> 的方式不熟悉，我推荐你先阅读 Tim Oxley 的<a href="https://nodesource.com/blog/semver-a-primer/">这篇文章</a>。</p>
<p>升级依赖没有通用方法。每个项目是不一样的，需要区别对待。升级依赖的<code>补丁</code>版本通常不会出什么问题，<code>小版本</code>一般也还OK。但如果你要升级依赖的<code>主版本</code>，你就需要仔细检查版本升级带来的改变。有可能 API 完全改变了，那样你就得重写你项目的一大堆代码。如果非必要，我一般避免将依赖升级到下一个主版本。</p>
<p>如果你的项目使用 <a href="https://www.npmjs.com/">npm</a> 来管理依赖，你可以很方便地使用 <code>npm outdated</code> 命令来检查你的依赖是否已经过时了。我用一个项目 <a href="https://github.com/morkro/FrontBook">FrontBook</a> 来举例说明，在这个项目里，我经常升级所有的依赖：</p>
<p><img src="https://p5.ssl.qhimg.com/d/inn/13f1cdac/1459594112npm-outdated.png" alt="Image of npm outdated"></p>
<p>如你所见，我这个项目里的依赖有很多主版本升级。我不会一次将他们全部升级，但是会一次升级一个。虽然这会耗费许多时间，但这是确保不会出问题的唯一办法（尤其是如果这个项目没有任何测试）。</p>
<h2 id="-">下面该干脏活了</h2>
<p>我必须让你知道的非常重要的一点是，清理代码并不意味着需要移除和重写大量的代码片段。当然，有时候这可能是唯一的解决办法，但是这不应该是你的首选方案。JavaScript 特别灵活，因此难以给出一般性的建议，通常情况下你必须对症下药。</p>
<h3 id="-">建立单元测试</h3>
<p>单元测试能保证你理解代码是如何工作的，这样避免一些意外导致错误。JavaScript 单元测试的内容足够写另一篇文章，所以我在这里不能详细介绍。目前被广泛使用的单元测试框架有 <a href="http://karma-runner.github.io/0.13/index.html">Karma</a>、<a href="http://jasmine.github.io/">Jasmine</a>、<a href="http://mochajs.org/">Mocha</a> 以及 <a href="https://github.com/sindresorhus/ava">Ava</a>。如果你还要测试你的用户界面，<a href="http://nightwatchjs.org/">Nightwatch.js</a> 和 <a href="http://dalekjs.com/">DalekJS</a> 是适合浏览器自动化测试的工具。</p>
<p>单元测试和浏览器自动化测试的区别是，前者测试你的 JavaScript 代码本身，来确保你所有的模块和主要逻辑运行无误。后者，测试用户界面，确保界面元素在正确的位置，且如预期地工作。</p>
<p>在你开始动手重构代码之前，认真对待单元测试，那样你的项目的稳定性将得到改善，而你甚至还没有开始考虑可扩展性。单元测试带来的另一个好处是你不再需要无时无刻担心你的改动会无意中破坏原有功能。</p>
<p>Rebecca Murphey 写了一篇很棒的文章关于<a href="https://rmurphey.com/blog/2014/07/13/unit-tests">如何为现有代码写单元测试</a>。</p>
<h3 id="-">架构</h3>
<p>JavaScript 架构是另一个大话题。重构和清理架构归结于你在这方面积累了多少经验。我们可以选择许多不同的<a href="https://addyosmani.com/resources/essentialjsdesignpatterns/book/">设计模式</a>，但不是所有的模式都适合于提升可扩展性。限于篇幅，我不能涵盖所有模式，但我至少可以给你一些通用的建议。</p>
<p>首先，你需要找出哪些设计模式在你的项目中已经使用到了。阅读有关这些模式的部分，确保它们在项目中使用上保持一致性。可扩展性的关键之一便是坚持一致的模式，避免混搭。当然，你可以针对项目中的不同目的采用不同的设计模式（例如，将<a href="https://addyosmani.com/resources/essentialjsdesignpatterns/book/#singletonpatternjavascript">单例模式</a>用于数据结构和短命名空间的辅助功能函数，以及将<a href="https://addyosmani.com/resources/essentialjsdesignpatterns/book/#observerpatternjavascript">观察者模式</a>用于与模块），但是别对一个模块使用了一种设计模式，对另一个模块又用另一种不同的设计模式。</p>
<p>如果你的项目没有任何架构（可能一切都堆在一个巨大的<code>app.js</code>文件里），从现在开始让它有架构。不过别指望一口吃成胖子，需要一点一点来。再次强调，没有对任何项目都适用的万精油方案，每一个项目的情况都是不同的。根据规模和复杂度不同，项目文件目录结构各有不同。通常，最基本的原则是，目录结构应当将第三方库、模块、数据以及负责初始化模块与逻辑的入口文件（比如：<code>index.js</code>、<code>main.js</code>）分开来。</p>
<p>简而言之就是<strong>模块化</strong>。</p>
<h3 id="-">将一切模块化？</h3>
<p>模块化不是解决 JavaScript 扩展性问题的唯一选择。模块化增加了一层 API，开发者不得不额外去熟悉它们。这虽然增加了麻烦，但是值得去做的。模块化的基本原则是将所有功能拆分为小模块。这么做了以后，不仅让你更容易解决代码里的问题，也让项目组的其他成员更容易协同工作。每个模块只做一件事，它们不用关心外部逻辑，可以被复用在不同的地方。</p>
<p>如何将一大堆功能拆分成许多逻辑关联的小模块？让我们来做做看：</p>
<pre><code>// 这个例子使用 Fetch API 来请求一个服务器的 API
// 让我们假设它返回一个 JSON 文件，包含一些基本信息
// 然后我们创建一个新的元素，统计 json 所有属性的
// content 字段中的字符数，然后将结果插入 DOM 的某个位置。
fetch(&#39;https://api.somewebsite.io/post/61454e0126ebb8a2e85d&#39;, { method: &#39;GET&#39; })
  .then(response =&gt; {
    if (response.status === 200) {
      return response.json();
    }
  })
  .then(json =&gt; {
    if (json) {
      Object.keys(json).forEach(key =&gt; {
        const item = json[key];
        const count = item.content.trim().replace(/\s+/gi, &#39;&#39;).length;
        const el = `
          &lt;div class=&quot;foo-${item.className}&quot;&gt;
            &amp;lt;p&amp;gt;Total characters: ${count}&amp;lt;/p&amp;gt;
          &amp;lt;/div&amp;gt;
        `;
        const wrapper = document.querySelector(&#39;.info-element&#39;);

        wrapper.innerHTML = el;
      });
    }
  })
  .catch(error =&gt; console.error(error));
</code></pre><p>上面的代码不是模块化的。所有的功能都耦合在一起。想象一下，如果这是更复杂的函数，由于出了一些错误你必须调试它们，可能 API 没返回，可能某些原因 JSON 内部的值被改变或者别的什么问题。调试这一大坨代码如同噩梦般，不是吗？</p>
<p>让我们将代码按不同的职责拆分开来：</p>
<pre><code>// 在前一个例子里，有一个功能是统计字符串的字符数
// 让我们将它单独抽出来成为一个模块
function countCharacters (text) {
  const removeWhitespace = /\s+/gi;
  return text.trim().replace(removeWhitespace, &#39;&#39;).length;
}

// 这一部分，我们也独立成一个模块，使用 DOM API 来创建 HTML
// 取代之前直接插入字符串的做法
function createWrapperElement (cssClass, content) {
  const className = cssClass || &#39;default&#39;;
  const wrapperElement = document.createElement(&#39;div&#39;);
  const textElement = document.createElement(&#39;p&#39;);
  const textNode = document.createTextNode(`Total characters: ${content}`);

  wrapperElement.classList.add(className);
  textElement.appendChild(textNode);
  wrapperElement.appendChild(textElement);

  return wrapperElement;
}

// 前一个例子 .forEach 中的匿名函数也被我们抽出来形成一个模块
function appendCharacterCount (config) {
  const wordCount = countCharacters(config.content);
  const wrapperElement = createWrapperElement(config.className, wordCount);
  const infoElement = document.querySelector(&#39;.info-element&#39;);

  infoElement.appendChild(wrapperElement);
}
</code></pre><p>好了，我们现在有了三个新模块，让我们看看重构之后的 <code>fetch</code> 调用：</p>
<pre><code>fetch(&#39;https://api.somewebsite.io/post/61454e0126ebb8a2e85d&#39;, { method: &#39;GET&#39; })
  .then(response =&gt; {
    if (response.status === 200) {
      return response.json();
    }
  })
  .then(json =&gt; {
    if (json) {
      Object.keys(json).forEach(key =&gt; appendCharacterCount(json[key]))
    }
  })
  .catch(error =&gt; console.error(error));
</code></pre><p>当然我们还可以进一步将 <code>.then( )</code> 中的逻辑也抽出来形成模块，不过我想我已经充分表达了模块化的含义。</p>
<h3 id="-">如果不想模块化呢？</h3>
<p>如我前面提到的，将你的代码拆成小模块会增加额外的一层 API。如果你不想这么做，但是又想要让代码易于与其他开发者一起维护，不拆函数也没问题，你依然可以将你的代码分解成更简单的部分并把重点放在可测试的代码上。</p>
<h3 id="-">为你的代码撰写文档</h3>
<p>文档化是一个老生常谈的话题。程序员社区的一部分人提倡将一切文档化，而另一部分人认为<a href="http://stackoverflow.com/questions/209015/what-is-self-documenting-code-and-can-it-replace-well-documented-code">好代码即是文档</a>。我奉行中庸之道，我觉得代码的可读和可扩展之间需要保持平衡。你可以使用 <a href="http://usejsdoc.org/">JSDoc</a> 来帮助你实现文档化。</p>
<p>JSDoc 是一个 JavaScript 的 API 文档生成器。常用的编辑器和 IDE 都有支持它的插件。我们看一个例子：</p>
<pre><code>function properties (name, obj = {}) {
  if (!name) return;
  const arr = [];

  Object.keys(obj).forEach(key =&gt; {
    if (arr.indexOf(obj[key][name]) &lt;= -1) {
      arr.push(obj[key][name]);
    }
  });

  return arr;
}
</code></pre><p>这个函数有两个参数，遍历一个对象，返回一个数组。这也许不是一个过于复杂的方法，但是对于没写过这段代码的人来说，搞懂它还是有点费劲。此外，这个方法具体的作用也不是很明确。让我们对它文档化：</p>
<pre><code>/**
 * 遍历一个对象，将将所有属性对象的 &#39;name&#39; push 到一个新数组中
 * 如果有重复，只 push 一次
 * @param  {String}  propertyName - 属性的名字
 * @param  {Object}  obj          - 你想要遍历的对象
 * @return {Array}
 */
function getArrayOfProperties (propertyName, obj = {}) {
  if (!propertyName) return;
  const properties = [];
  Object.keys(obj).forEach(child =&gt; {
    if (properties.indexOf(obj[child][propertyName]) &lt;= -1) {
      properties.push(obj[child][propertyName]);
    }
  });
  return properties;
}
</code></pre><p>我没有改变任何代码，只是改了一下函数名，添加了一段简短的注释，就已经让这段代码的可读性变得好多了。</p>
<h3 id="-">有条理的代码提交工作流</h3>
<p>重构本身是一项艰巨的任务。为了能随时回滚你的修改（假如你破坏了一些原有功能，过了一会才意识到，你可能就需要回滚代码到之前版本），你的每一部分修改，比如重写了一个方法、重命名了一个名字空间，都应该及时提交到 git （或者 svn）。这么做也许会让你觉得麻烦，但这么做有助于让你的清理工作更有条理。</p>
<p>为你的重构工作开一个新的分支，千万别总是在主线 (master) 上改。因为主线版本你有可能需要临时做一些更新或者随时发布一些 bug fixes 到线上环境，而你又不能将你没有经过测试的和未完成的重构一同发布到线上，因此我建议你还是应该在不同的分支上工作。</p>
<p>在 GitHub 上有一份<a href="https://guides.github.com/introduction/flow/">有趣的指导</a>，是关于如何使用他们的版本控制流程的。</p>
<h2 id="-">别失去理智</h2>
<p>除了用技术解决问题之外，有一个很重要的点很少被人提及：别为你的前任抓狂。我无意指责任何人，但是我知道一些人经历过这种情况。我花了很多年的时间去理解和克服心理上的不爽。我曾经对前任开发者们留下的代码、解决方案感到有些抓狂，他们做的一切在我眼里看来都造成混乱。</p>
<p>结果，这些消极情绪没带给我任何好处。消极情绪会导致你过度重构，浪费你的时间，而且可能破坏一些原有功能，而这一切又导致你越来越恼怒。你可能会花费额外的时间去重写一个本来毫无问题的模块，没有人会因此感谢你，因为你在做无用功。先分析状况，然后做有价值的重构。在任何时候，你随时可以对一个模块做一些细节的改进。</p>
<p>一段代码为什么写成这样往往是有历史原因的，也许前任程序员没有足够的时间将代码写得足够好、或者不知道有更好的写法，或者别的什么原因。我们都是过来人。</p>
<h2 id="-">整理一下</h2>
<p>让我们从头回顾一下所有的步骤，为你的下一个项目创建一个 checklist：</p>
<ol>
<li><p><strong>分析项目</strong></p>
<ul>
<li>先忘掉自己的开发者身份，以一个用户的身份来看清它的全貌。</li>
<li>浏览代码库，列出项目使用的工具。</li>
<li>阅读项目相关工具的文档和最佳实践。</li>
<li>浏览单元测试，从更高层面上了解项目。</li>
</ul>
</li>
<li><p><strong>创建基线</strong></p>
<ul>
<li>引入 <a href="http://editorconfig.org/"><code>.editorconfig</code></a> 以保证在所有的编辑器和 IDE 下保持代码风格一致。</li>
<li>使缩进风格一致，至于是用 tab 还是空格，无所谓。</li>
<li>执行命名约定。</li>
<li>如果代码检查工具不存在, 添加一个，可以是 <a href="http://eslint.org/">ESLint</a>、 <a href="http://www.jslint.com/">JSLint</a> 或者 <a href="http://jshint.com/">JSHint</a>。</li>
<li>升级依赖，但是需要格外小心，弄清楚到底升级了什么。</li>
</ul>
</li>
<li><p><strong>清理代码</strong></p>
<ul>
<li>建立单元测试与浏览器自动化测试，可以使用一些工具，例如 <a href="http://karma-runner.github.io/0.13/index.html">Karma</a>、<a href="http://jasmine.github.io/">Jasmine</a>、或者 <a href="http://nightwatchjs.org/">Nightwatch.js</a>。</li>
<li>确保架构和设计模式保持一致。</li>
<li>不要混用 <a href="https://addyosmani.com/resources/essentialjsdesignpatterns/book/">设计模式</a>，坚持使用已经存在的设计模式。</li>
<li>决定你是否需要将代码库拆分成模块。每一个模块应当具有单一的目的，模块不用关心自身之外的其他逻辑。</li>
<li>如果你不想拆分模块，把重点放在可测试的代码上，把它们分解成更简单的代码块。</li>
<li>恰当地命名你的函数，为代码适当撰写文档，保持可读和可扩展的平衡。</li>
<li>使用 <a href="http://usejsdoc.org/">JSDoc</a> 来生成文档。</li>
<li>定期提交代码，特别在有重要改变时。这样如果有什么改错了，可以方便回滚。</li>
</ul>
</li>
<li><p><strong>别失去理智</strong></p>
<ul>
<li>别为你的前任开发者抓狂。负面情绪只会导致过度重构而浪费时间。</li>
<li>一段代码为什么写成这样总是有原因的。要牢记我们都是过来人。</li>
</ul>
</li>
</ol>
<p>我非常希望这篇文章能帮到你。如果你正在为代码重构做这些努力，或者你有一些我没有提到过的好建议，我希望你可以告诉我。</p>