---
title: 使用Gulp构建网站的小白教程
date: 2016-05-28 21:31:00
tags: Gulp
---

![](http://7xsgf8.com1.z0.glb.clouddn.com/image/5687ce6435155def8f79.jpg)


       Gulp基于流的自动化构建工具

<!--more-->

[月影](https://www.h5jun.com/)推荐，完成于2016年04月07日

    原文：https://www.h5jun.com/post/functional-how-far.html

<p>行业里有一种批评，说前端太浮躁，总是追逐新技术，感觉 <a href="http://www.gruntjs.net/">grunt</a> 还不熟悉，突然一夜之间满大街都在谈论 <a href="http://www.gulpjs.com.cn/">gulp</a> 了。月影觉得不能怪技术发展太快，技术发展总是带来好处多于坏处，有时候我们确实需要鼓起勇气去“追求”技术潮流，当然理由是为了弄明白为什么有这些技术工具，而无关于什么浮躁之类的事儿。</p>
<p>也许是从业很多年有点累了，月影也对技术有些后知后觉，感觉 gulp 已经火了很久，才终于想起来写这篇文章，也许现在，很多工程师早已又去追求其他的什么类似的构建工具了。不管怎么样，如果你是一位前端工程师，你从来没有想过用构建工具优化网站这种事儿，或者你在工作中所在的团队和平台已经有成熟的工具，工作中不用自己再去琢磨 gulp 。你仍然可以暂时停下来阅读这篇文章，看看 gulp 这样的构建工具如何能帮你更简单地在构建的时候自动优化你的网站，也许你的个人博客也需要优化，也许你换了工作，要和之前熟悉不一样的构建工具，然而基本原理终归是“一招鲜吃遍天”的，不是吗？</p>
<!--more-->
<h2 id="toc-197">什么是 Gulp？</h2>
<p>Gulp 的官网title上对这个工具有一个比较准确的定义，叫做：<strong>基于流的自动化构建工具</strong>。如果你查看它的网页源代码，还会看到在<code>&lt;meta&gt;</code>标签里有一段更详细的描述：</p>
<blockquote>
<p> Gulp.js 是一个自动化构建工具，开发者可以使用它在项目开发过程中自动执行常见任务。Gulp.js 是基于 Node.js 构建的，利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。Gulp.js 源文件和你用来定义任务的 Gulp 文件都是通过 JavaScript（或者 CoffeeScript ）源码来实现的。</p>
</blockquote>
<p>所以，Gulp 是在项目开发过程中自动执行任务的一个工具，通过它可以方便地在开发时（或者发布前），对目标文件的内容进行I/O操作。</p>
<p>由于 Gulp 是基于流的，所以 Gulp 对于文件内容的操作就像是水槽对于水流，水流流经水槽，水槽将水流塑造成不同的形状。</p>
<p>既然是基于流的，在进一步理解 Gulp 前，我们最好先来理解什么是流。</p>
<h2 id="toc-84a">Stream 流</h2>
<p>在计算机系统中文件的大小是变化很大的，有的文件内容非常多非常大，而 Node.js 里文件操作是异步的，如果用一个形象的比喻来形容将一个文件的内容读取出来并写入另一个文件中，可以将它想象成文件内容像是水流，从一个文件“流”入另一个文件。</p>
<p><img src="https://p.ssl.qhimg.com/d/inn/106d26c5/6c224f4a20a446230144a30f9822720e0df3d7d4.jpg" alt="stream"></p>
<p>在node里面，读写文件可以用“流”来描述：</p>
<pre><code class="hljs lang-js"><span class="hljs-meta">"use strict"</span>;

<span class="hljs-keyword">let</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);

fs.createReadStream(<span class="hljs-string">"./in.txt"</span>)
  .pipe(fs.createWriteStream(<span class="hljs-string">"./out.txt"</span>));
</code></pre>
<p>上面的代码除了将 in.txt 文件中的内容输出到 out.txt 之外，不做其他任何事情，相当于复制了一份数据，从语法形式上可以看到，“数据流”从 fs.createReadStream 创建然后经过 pipe 流出，最后到 fs.createWriteStream。</p>
<p>在这输入流到输出流的中间，我们可以对“流”做一些事情：</p>
<pre><code class="hljs lang-js"><span class="hljs-meta">"use strict"</span>;

<span class="hljs-keyword">let</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);
<span class="hljs-keyword">let</span> through = <span class="hljs-built_in">require</span>(<span class="hljs-string">"through2"</span>);

fs.createReadStream(<span class="hljs-string">"./in.txt"</span>)
  .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">contents, enc, done</span>) </span>{
    <span class="hljs-keyword">if</span>(enc === <span class="hljs-string">"buffer"</span>){
      contents = contents.toString(<span class="hljs-string">"utf-8"</span>);
      enc = <span class="hljs-string">"utf-8"</span>;
    }
    done(<span class="hljs-literal">null</span>, contents, enc);
   }))
  .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">contents, enc, done</span>) </span>{
    done(<span class="hljs-literal">null</span>, contents.toUpperCase(), enc);
   }))
  .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span> (<span class="hljs-params">contents, enc, done</span>) </span>{
    contents = contents.split(<span class="hljs-string">""</span>).reverse().join(<span class="hljs-string">""</span>);
    done(<span class="hljs-literal">null</span>, contents, enc);
   }))
  .pipe(fs.createWriteStream(<span class="hljs-string">"./out.txt"</span>));
</code></pre>
<p>在上面的代码里，我们通过 Node.js 的 <a href="https://github.com/rvagg/through2">through2</a> 库（这是一个针对“流”的包装库），将输入流一步步转换成输出流，在中间的 pipes 中我们先是将 Buffer 转成 String，然后将它变成大写，最后再 reverse 然后传给输出流。</p>
<p>所以如果 in.txt 的文件内容是 <code>hello world~</code>，那么 out.txt 的文件内容将是： <code>~DLROW OLLEH</code>。</p>
<h2 id="toc-e10">基于流的 Gulp</h2>
<p>月影觉得 Gulp 的文档其实写得挺烂的，点中文文档页面除了让你看<a href="http://www.gulpjs.com.cn/docs/getting-started/">入门指南</a>、<a href="http://www.gulpjs.com.cn/docs/api/">API文档</a>、<a href="http://www.gulpjs.com.cn/docs/cli/">CLI文档</a>、<a href="http://www.gulpjs.com.cn/docs/writing-a-plugin/">编写插件文档</a>之外就没什么了，但实际上真要用 Gulp 的高级功能，这些文档简直就和教人如何画马一样：</p>
<p><img src="https://p.ssl.qhimg.com/d/inn/657e563f/original_LFQk_5e4400003dc3125d.jpg" alt="画马"></p>
<p>既然 Gulp 是基于流的，我们就要理解 Gulp 如何控制和操作流。</p>
<p>然而在这之前，我们还要先看最基础的（还没安装 Gulp 的同学可以照前面那个入门指南安装一下~）</p>
<pre><code class="hljs lang-js"><span class="hljs-keyword">var</span> gulp = <span class="hljs-built_in">require</span>(<span class="hljs-string">"gulp"</span>);

gulp.task(<span class="hljs-string">"sync1"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
  <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"我是一个同步任务"</span>);
});

gulp.task(<span class="hljs-string">"async"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">done</span>) </span>{
  setTimeout(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"我是一个异步任务"</span>);
    done();
  }, <span class="hljs-number">2000</span>);  
});
</code></pre>
<p>我们可以看到 Gulp 是基于任务的，gulp.task 可以定义一个任务，这样的话，我们在命令行下就可以通过 <code>gulp 任务名</code> 的方式来执行命令了：</p>
<pre><code class="hljs lang-bash">$ gulp sync1
[18:27:12] Using gulpfile ~/Workspace/yuntu/myblog-master/item/photohome/gulpfile.js
[18:27:12] Starting <span class="hljs-string">"sync1"</span>...
我是一个同步任务
[18:27:12] Finished <span class="hljs-string">"sync1"</span> after 122 μs

$ gulp async
[18:27:48] Using gulpfile ~/Workspace/yuntu/myblog-master/item/photohome/gulpfile.js
[18:27:48] Starting <span class="hljs-string">"async"</span>...
我是一个异步任务
[18:27:50] Finished <span class="hljs-string">"async"</span> after 2 s
</code></pre>
<p>Gulp 的任务可以是同步和异步，在异步任务中确定任务完成，可以通过调用函数参数 done 来实现。</p>
<p>Gulp 也允许我们将任务组合起来执行：</p>
<pre><code class="hljs lang-js"><span class="hljs-keyword">var</span> gulp = <span class="hljs-built_in">require</span>(<span class="hljs-string">"gulp"</span>);
<span class="hljs-keyword">var</span> through = <span class="hljs-built_in">require</span>(<span class="hljs-string">"through2"</span>);

gulp.task(<span class="hljs-string">"sync1"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
  <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"我是一个同步任务"</span>);
});

gulp.task(<span class="hljs-string">"sync2"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
  <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"我是另一个同步任务"</span>);
});

gulp.task(<span class="hljs-string">"sync3"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>) </span>{
  <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"我是又一个同步任务"</span>);
});

gulp.task(<span class="hljs-string">"async"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">done</span>) </span>{
  <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"老大喊我去搬砖"</span>);
  setTimeout(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"我是一个异步任务"</span>);
    done();
  }, <span class="hljs-number">2000</span>);  
});

gulp.task(<span class="hljs-string">"syncs"</span>, [<span class="hljs-string">"async"</span>, <span class="hljs-string">"sync1"</span>, <span class="hljs-string">"sync2"</span>, <span class="hljs-string">"sync3"</span>], 
  <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
    <span class="hljs-built_in">console</span>.log(<span class="hljs-string">"砖搬完了！"</span>);
  });
</code></pre>
<pre><code class="hljs lang-bash">$ gulp syncs
[18:30:30] Using gulpfile ~/Workspace/yuntu/myblog-master/item/photohome/gulpfile.js
[18:30:30] Starting <span class="hljs-string">"async"</span>...
老大喊我去搬砖
[18:30:30] Starting <span class="hljs-string">"sync1"</span>...
我是一个同步任务
[18:30:30] Finished <span class="hljs-string">"sync1"</span> after 142 μs
[18:30:30] Starting <span class="hljs-string">"sync2"</span>...
我是另一个同步任务
[18:30:30] Finished <span class="hljs-string">"sync2"</span> after 55 μs
[18:30:30] Starting <span class="hljs-string">"sync3"</span>...
我是又一个同步任务
[18:30:30] Finished <span class="hljs-string">"sync3"</span> after 43 μs
我是一个异步任务
[18:30:32] Finished <span class="hljs-string">"async"</span> after 2 s
[18:30:32] Starting <span class="hljs-string">"syncs"</span>...
砖搬完了！
[18:30:32] Finished <span class="hljs-string">"syncs"</span> after 38 μs
</code></pre>
<p>我们看到说 gulp.task 可以有依赖，只要第二个参数传一个数组，中间加上依赖的任务就行了，而数组里面的这些任务是<strong>并行处理</strong>的，不会一个执行完才执行另一个（同步任务的输出比异步任务的结束早）。</p>
<p>以上是 Gulp 基本的任务模型。对于每个 task，Gulp 通常用来操作文件输入和输出流，因此 Gulp 封装了批量操作文件流的 api：</p>
<pre><code class="hljs lang-js">gulp.task(<span class="hljs-string">"src-dist"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./*.html"</span>)
      .pipe(gulp.dest(<span class="hljs-string">"./dist"</span>));
});
</code></pre>
<p>上面的命令表示将当前目录下所有的 .html 文件匹配出来，依次输出到目标文件夹 ./dist 中去。</p>
<p>我们还可以用更高级的通配符：</p>
<pre><code class="hljs lang-js">gulp.task(<span class="hljs-string">"src-dist"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./**/*.html"</span>)
      .pipe(gulp.dest(<span class="hljs-string">"./dist"</span>));
});
</code></pre>
<p>这样处理的 html 文件不仅仅匹配当前目录下的，还包括所有子目录里。关于输入这块，具体的用法还有很多，遵循的规范是glob模式，可以参考 <a href="https://github.com/isaacs/node-glob">node-glob</a></p>
<h2 id="toc-246">处理文件的内容</h2>
<p>与上面说的 FileSystem 文件流类似，如果我们不做什么别的事情，那么我们就只是将文件从源 src，拷贝到了目的地 dest，其他的啥也没做，那么显然，我们可以做那么一些事情，在这里，我们尝试处理一下 index.html：</p>
<pre><code class="hljs lang-js">gulp.task(<span class="hljs-string">"build-index"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./index.html"</span>)
    .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">file, encode, cb</span>) </span>{
        <span class="hljs-keyword">var</span> contents = file.contents.toString(encode);

        <span class="hljs-keyword">var</span> HTMLMinifier = <span class="hljs-built_in">require</span>(<span class="hljs-string">"html-minifier"</span>).minify;

        <span class="hljs-keyword">var</span> minified = HTMLMinifier(contents, {
          minifyCSS: <span class="hljs-literal">true</span>,
          minifyJS: <span class="hljs-literal">true</span>,
          collapseWhitespace: <span class="hljs-literal">true</span>,
          removeAttributeQuotes: <span class="hljs-literal">true</span>
        });

        <span class="hljs-comment">//console.log(minified);</span>
        file.contents = <span class="hljs-keyword">new</span> Buffer(minified, encode);
        cb(<span class="hljs-literal">null</span>, file, encode);
      }))
    .pipe(gulp.dest(<span class="hljs-string">"./dist"</span>));
});
</code></pre>
<p>gulp.src 的输入流和 fileReadStream 会有一点点不一样，它的第一个参数不是一个 Buffer，而是一个包含文件信息和文件内容的对象，第二个参数是文件的编码，因此我们可以通过</p>
<pre><code class="hljs lang-js"><span class="hljs-keyword">var</span> contents = file.contents.toString(encode);
</code></pre>
<p>将文件内容转成字符串。之后，我们使用 <a href="https://github.com/search?utf8=%E2%9C%93&amp;q=html-minifier">html-minifier</a> 对文件的HTML内容和内联的样式、脚本进行压缩，这样就简单完成了首页 index.html 的优化！</p>
<h2 id="toc-aea">进一步优化</h2>
<p>前面只完成了优化的第一步，我们还没考虑外链资源该怎么处理呢，外链资源包括 js、 css 和图片。在处理之前，我们来约定一些规范：</p>
<ul>
<li><p>页面 js 存放在 ./static/js 下，公共的库放在 ./static/js/lib 下，公共库只压缩不合并，页面 js 压缩并合并。</p>
</li>
<li><p>页面 css 存放在 ./static/css 下，公共的css放在 ./static/css/common 下，公共 css 只压缩不合并，页面 css 压缩并合并。</p>
</li>
<li><p>图片资源中小于3kb的图片以 base64 方式内联，图片放在 ./static/img 下。</p>
</li>
</ul>
<p><strong>压缩 js</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">minifyAndComboJS</span>(<span class="hljs-params">name, encode, files</span>)</span>{
  <span class="hljs-keyword">var</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);
  <span class="hljs-keyword">var</span> UglifyJS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"uglify-js"</span>);
  <span class="hljs-keyword">var</span> content = <span class="hljs-string">""</span>;

  files.forEach(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">js</span>)</span>{
    <span class="hljs-keyword">var</span> minified = UglifyJS.minify(js).code;
    content += minified;
  });

  <span class="hljs-keyword">if</span>(content){
    <span class="hljs-keyword">var</span> combo = <span class="hljs-string">"static/js/"</span> + name;
  }
  fs.writeFileSync(combo, content);

  gulp.src(combo)
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/js"</span>));
}
</code></pre>
<p><strong>压缩 js lib</strong></p>
<pre><code class="hljs lang-js">gulp.task(<span class="hljs-string">"build-js-lib"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./static/js/lib/**/*.js"</span>)
      .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">file, encode, cb</span>) </span>{
        <span class="hljs-keyword">var</span> UglifyJS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"uglify-js"</span>);

        <span class="hljs-keyword">var</span> contents = file.contents.toString(encode);
        <span class="hljs-keyword">var</span> minified = UglifyJS.minify(contents, 
          {fromString:<span class="hljs-literal">true</span>}).code;

        file.contents = <span class="hljs-keyword">new</span> Buffer(minified, encode);
        cb(<span class="hljs-literal">null</span>, file, encode);
      }))
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/js/lib"</span>));
});
</code></pre>
<p><strong>压缩 css</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">minifyAndComboCSS</span>(<span class="hljs-params">name, encode, files</span>)</span>{
  <span class="hljs-keyword">var</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);
  <span class="hljs-keyword">var</span> CleanCSS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"clean-css"</span>);
  <span class="hljs-keyword">var</span> content = <span class="hljs-string">""</span>;

  files.forEach(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">css</span>)</span>{
    <span class="hljs-keyword">var</span> contents = fs.readFileSync(css, encode);
    <span class="hljs-keyword">var</span> minified = <span class="hljs-keyword">new</span> CleanCSS().minify(contents).styles; 
    content += minified;
  });

  <span class="hljs-keyword">if</span>(content){
    <span class="hljs-keyword">var</span> combo = <span class="hljs-string">"static/css/"</span> + name;
  }
  fs.writeFileSync(combo, content);

  gulp.src(combo)
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/css"</span>));
}
</code></pre>
<p><strong>压缩公共 css</strong></p>
<pre><code class="hljs lang-js">gulp.task(<span class="hljs-string">"build-common-css"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./static/css/common/**/*.css"</span>)
      .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">file, encode, cb</span>) </span>{
        <span class="hljs-keyword">var</span> CleanCSS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"clean-css"</span>);

        <span class="hljs-keyword">var</span> contents = file.contents.toString(encode);
        <span class="hljs-keyword">var</span> minified = <span class="hljs-keyword">new</span> CleanCSS().minify(contents).styles;  

        file.contents = <span class="hljs-keyword">new</span> Buffer(minified, encode);
        cb(<span class="hljs-literal">null</span>, file, encode);
      }))
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/css/common"</span>));
});
</code></pre>
<p><strong>处理图片</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-comment">//内联小图片</span>
<span class="hljs-keyword">var</span> imgs = $(<span class="hljs-string">"img"</span>);
<span class="hljs-keyword">for</span>(<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i &lt; imgs.length; i++){
  <span class="hljs-keyword">var</span> img = $(imgs[i]);
  <span class="hljs-keyword">var</span> src = img.attr(<span class="hljs-string">"src"</span>);
  <span class="hljs-keyword">if</span>(<span class="hljs-regexp">/^static\/img/</span>.test(src)){
    <span class="hljs-keyword">var</span> stat = fs.statSync(src);
    <span class="hljs-keyword">var</span> ext = <span class="hljs-built_in">require</span>(<span class="hljs-string">"path"</span>).parse(src).ext;

    <span class="hljs-keyword">if</span>(stat.size &lt;= <span class="hljs-number">3000</span>){
      <span class="hljs-keyword">var</span> head = ext === <span class="hljs-string">".png"</span> ? <span class="hljs-string">"data:image/png;base64,"</span> : <span class="hljs-string">"data:image/jpeg;base64,"</span>;
      <span class="hljs-keyword">var</span> datauri = fs.readFileSync(src).toString(<span class="hljs-string">"base64"</span>);
      img.attr(<span class="hljs-string">"src"</span>, head + datauri);
    }
  }
}
</code></pre>
<p><strong>压缩 HTML</strong></p>
<pre><code class="hljs lang-js">contents = $.html();

<span class="hljs-comment">//压缩 HTML</span>
<span class="hljs-keyword">var</span> HTMLMinifier = <span class="hljs-built_in">require</span>(<span class="hljs-string">"html-minifier"</span>).minify;

<span class="hljs-keyword">var</span> minified = HTMLMinifier(contents, {
  minifyCSS: <span class="hljs-literal">true</span>,
  minifyJS: <span class="hljs-literal">true</span>,
  collapseWhitespace: <span class="hljs-literal">true</span>,
  removeAttributeQuotes: <span class="hljs-literal">true</span>
});
</code></pre>
<p>然后，在处理 index.html 的时候，我们可以使用 <a href="https://github.com/cheeriojs/cheerio">cheerio</a> 来解析文件，将要处理的外链从文档中提取出来。</p>
<p><strong>提取 js 和 css 外链处理</strong></p>
<pre><code class="hljs lang-js"><span class="hljs-keyword">var</span> $ = <span class="hljs-built_in">require</span>(<span class="hljs-string">"cheerio"</span>).load(contents, {decodeEntities: <span class="hljs-literal">false</span>});

<span class="hljs-comment">//处理外链 css</span>
<span class="hljs-keyword">var</span> links = $(<span class="hljs-string">"link"</span>);
<span class="hljs-keyword">var</span> cssToCombo = [];

<span class="hljs-keyword">for</span>(<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i &lt; links.length; i++){
  <span class="hljs-keyword">var</span> link = $(links[i]);
  <span class="hljs-keyword">if</span>(link.attr(<span class="hljs-string">"rel"</span>) === <span class="hljs-string">"stylesheet"</span>){
    <span class="hljs-keyword">var</span> href = link.attr(<span class="hljs-string">"href"</span>);
    <span class="hljs-keyword">if</span>(<span class="hljs-regexp">/^static\/css\/(?!common)/</span>.test(href)){
      cssToCombo.push(href);
      <span class="hljs-keyword">if</span>(cssToCombo.length == <span class="hljs-number">1</span>){
        link.attr(<span class="hljs-string">"href"</span>, <span class="hljs-string">"static/css/index.min.css"</span>);
      }<span class="hljs-keyword">else</span>{
        link.remove();
      }
    }
  }
}
minifyAndComboCSS(<span class="hljs-string">"index.min.css"</span>, encode, cssToCombo);

<span class="hljs-comment">//处理外链 js</span>
<span class="hljs-keyword">var</span> scripts = $(<span class="hljs-string">"script"</span>);
<span class="hljs-keyword">var</span> jsToCombo = [];
<span class="hljs-keyword">for</span>(<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i &lt; scripts.length; i++){
  <span class="hljs-keyword">var</span> s = $(scripts[i]);

  <span class="hljs-comment">//判断script标签确实是js</span>
  <span class="hljs-keyword">if</span>(s.attr(<span class="hljs-string">"type"</span>) == <span class="hljs-literal">null</span> 
    || s.attr(<span class="hljs-string">"type"</span>) === <span class="hljs-string">"text/javascript"</span>){
    <span class="hljs-keyword">var</span> src = s.attr(<span class="hljs-string">"src"</span>);

    <span class="hljs-keyword">if</span>(src){
      <span class="hljs-comment">//外链的js，默认只处理以/static/开头的资源</span>
      <span class="hljs-keyword">if</span>(<span class="hljs-regexp">/^static\/js\/(?!lib)/</span>.test(src)){
        jsToCombo.push(src);
        <span class="hljs-keyword">if</span>(jsToCombo.length == <span class="hljs-number">1</span>){
          s.attr(<span class="hljs-string">"src"</span>, <span class="hljs-string">"static/js/index.min.js"</span>);
        }<span class="hljs-keyword">else</span>{
          s.remove();  
        }   
      }
    }
  }
}
minifyAndComboJS(<span class="hljs-string">"index.min.js"</span>, encode, jsToCombo);
</code></pre>
<p>最后是完整的代码：</p>
<pre><code class="hljs lang-js"><span class="hljs-keyword">var</span> gulp = <span class="hljs-built_in">require</span>(<span class="hljs-string">"gulp"</span>);
<span class="hljs-keyword">var</span> through = <span class="hljs-built_in">require</span>(<span class="hljs-string">"through2"</span>);

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">minifyAndComboCSS</span>(<span class="hljs-params">name, encode, files</span>)</span>{
  <span class="hljs-keyword">var</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);
  <span class="hljs-keyword">var</span> CleanCSS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"clean-css"</span>);
  <span class="hljs-keyword">var</span> content = <span class="hljs-string">""</span>;

  files.forEach(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">css</span>)</span>{
    <span class="hljs-keyword">var</span> contents = fs.readFileSync(css, encode);
    <span class="hljs-keyword">var</span> minified = <span class="hljs-keyword">new</span> CleanCSS().minify(contents).styles; 
    content += minified;
  });

  <span class="hljs-keyword">if</span>(content){
    <span class="hljs-keyword">var</span> combo = <span class="hljs-string">"static/css/"</span> + name;
  }
  fs.writeFileSync(combo, content);

  gulp.src(combo)
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/css"</span>));
}

<span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">minifyAndComboJS</span>(<span class="hljs-params">name, encode, files</span>)</span>{
  <span class="hljs-keyword">var</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);
  <span class="hljs-keyword">var</span> UglifyJS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"uglify-js"</span>);
  <span class="hljs-keyword">var</span> content = <span class="hljs-string">""</span>;

  files.forEach(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">js</span>)</span>{
    <span class="hljs-keyword">var</span> minified = UglifyJS.minify(js).code;
    content += minified;
  });

  <span class="hljs-keyword">if</span>(content){
    <span class="hljs-keyword">var</span> combo = <span class="hljs-string">"static/js/"</span> + name;
  }
  fs.writeFileSync(combo, content);

  gulp.src(combo)
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/js"</span>));
}


gulp.task(<span class="hljs-string">"build-index"</span>, [<span class="hljs-string">"build-js-lib"</span>, <span class="hljs-string">"build-common-css"</span>], <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./index.html"</span>)
    .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">file, encode, cb</span>) </span>{
        <span class="hljs-keyword">var</span> fs = <span class="hljs-built_in">require</span>(<span class="hljs-string">"fs"</span>);
        <span class="hljs-keyword">var</span> contents = file.contents.toString(encode);

        <span class="hljs-keyword">var</span> $ = <span class="hljs-built_in">require</span>(<span class="hljs-string">"cheerio"</span>).load(contents, {decodeEntities: <span class="hljs-literal">false</span>});

        <span class="hljs-comment">//处理外链 css</span>
        <span class="hljs-keyword">var</span> links = $(<span class="hljs-string">"link"</span>);
        <span class="hljs-keyword">var</span> cssToCombo = [];

        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i &lt; links.length; i++){
          <span class="hljs-keyword">var</span> link = $(links[i]);
          <span class="hljs-keyword">if</span>(link.attr(<span class="hljs-string">"rel"</span>) === <span class="hljs-string">"stylesheet"</span>){
            <span class="hljs-keyword">var</span> href = link.attr(<span class="hljs-string">"href"</span>);
            <span class="hljs-keyword">if</span>(<span class="hljs-regexp">/^static\/css\/(?!common)/</span>.test(href)){
              cssToCombo.push(href);
              <span class="hljs-keyword">if</span>(cssToCombo.length == <span class="hljs-number">1</span>){
                link.attr(<span class="hljs-string">"href"</span>, <span class="hljs-string">"static/css/index.min.css"</span>);
              }<span class="hljs-keyword">else</span>{
                link.remove();
              }
            }
          }
        }
        minifyAndComboCSS(<span class="hljs-string">"index.min.css"</span>, encode, cssToCombo);

        <span class="hljs-comment">//处理外链 js</span>
        <span class="hljs-keyword">var</span> scripts = $(<span class="hljs-string">"script"</span>);
        <span class="hljs-keyword">var</span> jsToCombo = [];
        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i &lt; scripts.length; i++){
          <span class="hljs-keyword">var</span> s = $(scripts[i]);

          <span class="hljs-comment">//判断script标签确实是js</span>
          <span class="hljs-keyword">if</span>(s.attr(<span class="hljs-string">"type"</span>) == <span class="hljs-literal">null</span> 
            || s.attr(<span class="hljs-string">"type"</span>) === <span class="hljs-string">"text/javascript"</span>){
            <span class="hljs-keyword">var</span> src = s.attr(<span class="hljs-string">"src"</span>);

            <span class="hljs-keyword">if</span>(src){
              <span class="hljs-comment">//外链的js，默认只处理以/static/开头的资源</span>
              <span class="hljs-keyword">if</span>(<span class="hljs-regexp">/^static\/js\/(?!lib)/</span>.test(src)){
                jsToCombo.push(src);
                <span class="hljs-keyword">if</span>(jsToCombo.length == <span class="hljs-number">1</span>){
                  s.attr(<span class="hljs-string">"src"</span>, <span class="hljs-string">"static/js/index.min.js"</span>);
                }<span class="hljs-keyword">else</span>{
                  s.remove();  
                }   
              }
            }
          }
        }
        minifyAndComboJS(<span class="hljs-string">"index.min.js"</span>, encode, jsToCombo);

        <span class="hljs-comment">//处理内联图片</span>
        <span class="hljs-keyword">var</span> imgs = $(<span class="hljs-string">"img"</span>);
        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">var</span> i = <span class="hljs-number">0</span>; i &lt; imgs.length; i++){
          <span class="hljs-keyword">var</span> img = $(imgs[i]);
          <span class="hljs-keyword">var</span> src = img.attr(<span class="hljs-string">"src"</span>);
          <span class="hljs-keyword">if</span>(<span class="hljs-regexp">/^static\/img/</span>.test(src)){
            <span class="hljs-keyword">var</span> stat = fs.statSync(src);
            <span class="hljs-keyword">var</span> ext = <span class="hljs-built_in">require</span>(<span class="hljs-string">"path"</span>).parse(src).ext;

            <span class="hljs-keyword">if</span>(stat.size &lt;= <span class="hljs-number">3000</span>){
              <span class="hljs-keyword">var</span> head = ext === <span class="hljs-string">".png"</span> ? <span class="hljs-string">"data:image/png;base64,"</span> : <span class="hljs-string">"data:image/jpeg;base64,"</span>;
              <span class="hljs-keyword">var</span> datauri = fs.readFileSync(src).toString(<span class="hljs-string">"base64"</span>);
              img.attr(<span class="hljs-string">"src"</span>, head + datauri);
            }
          }
        }        

        contents = $.html();

        <span class="hljs-comment">//压缩 HTML</span>
        <span class="hljs-keyword">var</span> HTMLMinifier = <span class="hljs-built_in">require</span>(<span class="hljs-string">"html-minifier"</span>).minify;

        <span class="hljs-keyword">var</span> minified = HTMLMinifier(contents, {
          minifyCSS: <span class="hljs-literal">true</span>,
          minifyJS: <span class="hljs-literal">true</span>,
          collapseWhitespace: <span class="hljs-literal">true</span>,
          removeAttributeQuotes: <span class="hljs-literal">true</span>
        });

        file.contents = <span class="hljs-keyword">new</span> Buffer(minified, encode);
        cb(<span class="hljs-literal">null</span>, file, encode);
      }))
    .pipe(gulp.dest(<span class="hljs-string">"./dist"</span>));
});

gulp.task(<span class="hljs-string">"build-js-lib"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./static/js/lib/**/*.js"</span>)
      .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">file, encode, cb</span>) </span>{
        <span class="hljs-keyword">var</span> UglifyJS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"uglify-js"</span>);

        <span class="hljs-keyword">var</span> contents = file.contents.toString(encode);
        <span class="hljs-keyword">var</span> minified = UglifyJS.minify(contents, 
          {fromString:<span class="hljs-literal">true</span>}).code;

        file.contents = <span class="hljs-keyword">new</span> Buffer(minified, encode);
        cb(<span class="hljs-literal">null</span>, file, encode);
      }))
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/js/lib"</span>));
});

gulp.task(<span class="hljs-string">"build-common-css"</span>, <span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params"></span>)</span>{
  gulp.src(<span class="hljs-string">"./static/css/common/**/*.css"</span>)
      .pipe(through.obj(<span class="hljs-function"><span class="hljs-keyword">function</span>(<span class="hljs-params">file, encode, cb</span>) </span>{
        <span class="hljs-keyword">var</span> CleanCSS = <span class="hljs-built_in">require</span>(<span class="hljs-string">"clean-css"</span>);

        <span class="hljs-keyword">var</span> contents = file.contents.toString(encode);
        <span class="hljs-keyword">var</span> minified = <span class="hljs-keyword">new</span> CleanCSS().minify(contents).styles;  

        file.contents = <span class="hljs-keyword">new</span> Buffer(minified, encode);
        cb(<span class="hljs-literal">null</span>, file, encode);
      }))
      .pipe(gulp.dest(<span class="hljs-string">"./dist/static/css/common"</span>));
});
</code></pre>
<h2 id="toc-25f">总结</h2>
<p>我们用 Gulp 创建了一个非常简单的构建脚本，它可以压缩合并我们项目的 js 和 css 并处理小图片，我们还可以给它进一步增加其他功能，例如给压缩的文件添加版本号，或者根据内容计算签名以实现更新后不被缓存，我们还可以用 CDN 服务的 sdk 将资源发布到 CDN 并替换原始链接，同时，我们可以不用每次发布所有的文件，我们可以在开发的时候用 gulp.watch 来监控文件的修改，以实现增量的编译发布。</p>
<p>总之，我们可以用 gulp 来做许多有用的事情，来完善我们的构建脚本，而这一切都因为 gulp 基于流的构建以及 NPM 丰富的库变得非常简单。最后的最后，由于我们从头使用 through2 来处理任务，所以我们在具体实现功能的时候还是略微繁琐，事实上 gulp 提供了不少有用的<a href="http://www.gulpjs.com.cn/docs/writing-a-plugin/">插件</a>，这些插件直接返回 stream 对象，可以让构建过程变得更简单，具体的可以多研究官方的文档。</p>
 </div> <p>本文链接：<a href="https://www.h5jun.com/post/gulp-build.html">https://www.h5jun.com/post/gulp-build.html</a></p> <p>-- <acronym title="End of File">EOF</acronym> --</p> 