---
title: 理解HTTP
date: 2016-06-01 10:34:00
tags: Gulp
---

![](http://7xsgf8.com1.z0.glb.clouddn.com/image/k3JP8kJI424.jpg)


       理解HTTP走向服务端的第一步

<!--more-->

[LuckyJing](http://www.jianshu.com/p/81632fea327c)推荐，完成于2016-06-01






<h2 id="t1"><a name="t1"></a>URL与URI</h2>
<p>我们经常接触到的就是URL了，它就是我们访问web的一个字符串地址，那么URI是什么呢？他们是什么关系呢？<br />
URL：uniform resource location 统一资源定位符<br />
URI：uniform resource identifier 统一资源标识符<br />
这也就是说，URI是一种资源的标识；而URL也是一种URI，也是一种资源的标识，但它也指明了如何定位Locate到这个资源。<br />
URI是一种抽象的资源标识，<strong>既可以是绝对的，也可以是相对的</strong>。但是URL是一种URI，它指明了定位的信息，必须是绝对的。</p>
<h2 id="t2"><a name="t2"></a>报文-通信的桥梁</h2>
<p>客户端和服务器端通过相互发送<strong>报文</strong>进行通信，要深刻理解HTTP协议，就需要理解报文的格式和内容。</p>
<h3 id="t3"><a name="t3"></a>报文的组成</h3>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/12401"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/12401" alt="1240" width="1208" height="520" /></a></p>
<div class="image-caption"></div>
</div>
<p>无论是请求报文还是响应报文都需要有报文首部，当然报文主体并不是必需的。<br />
一般来说，请求报文的格式如下：</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/101010010"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/101010010" alt="10101001" width="473" height="205" /></a></p>
<div class="image-caption"></div>
</div>
<p>看一下百度网站的请求报文：</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/66606"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/66606" alt="6660" width="815" height="164" /></a></p>
<div class="image-caption"></div>
</div>
<p>简单的报文形式：</p>
<pre class="hljs cpp"><code class="cpp">GET / HTTP/<span class="hljs-number">1.1</span>    <span class="hljs-comment">//请求行，包含用于请求的方法，请求的URI，HTTP版本</span>
<span class="hljs-comment">//以下为各种首部字段</span>
Host: www.baidu.com
Connection: keep-alive
Upgrade-Insecure-Requests: <span class="hljs-number">1</span>
User-Agent: Mozilla/<span class="hljs-number">5.0</span>...
Accept-Encoding: gzip, deflate, sdch
Accept-Language: zh-CN,zh;q=<span class="hljs-number">0.8</span></code></pre>
<p>响应报文的格式如下：</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/1240"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/1240" alt="124" width="472" height="203" /></a></p>
<div class="image-caption"></div>
</div>
<p>看一下百度网站的响应报文：</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/22402"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/22402" alt="2240" width="800" height="242" /></a></p>
<div class="image-caption"></div>
</div>
<pre class="hljs cpp"><code class="cpp">HTTP/<span class="hljs-number">1.1</span> <span class="hljs-number">200</span> OK   <span class="hljs-comment">//状态行，包含表明响应结果的状态码，原因短语和HTTP版本</span>
<span class="hljs-comment">//以下为各种首部字段</span>
Server: bfe/<span class="hljs-number">1.0</span><span class="hljs-number">.8</span><span class="hljs-number">.5</span>
Date: Tue, <span class="hljs-number">06</span> Oct <span class="hljs-number">2015</span> <span class="hljs-number">14</span>:<span class="hljs-number">48</span>:<span class="hljs-number">28</span> GMT
Content-Type: text/html;charset=utf-<span class="hljs-number">8</span>
Transfer-Encoding: chunked
Connection: keep-alive
Cache-Control: <span class="hljs-keyword">private</span></code></pre>
<h2 id="t4"><a name="t4"></a>告知服务器意图的HTTP方法</h2>
<p>发送HTTP的方法有许多种，最常用的便是GET和POST，下面就这两种进行详细地说明。</p>
<ol>
<li><strong>GET</strong><br />
GET方法用来请求访问URI所指定的资源，<strong>（我想访问你的某个资源）</strong>并不对服务器上的内容产生任何作用结果；每次GET的内容都是相同的。GET方式把请求所需要的参数放到<code>URL</code>中，直接就可以在URL中看见，有大小限制。</li>
<li><strong>POST</strong><br />
POST方法用来传输实体主体，目的并不是获取响应的主体内容，<strong>（我要把这条信息告诉你）</strong>，POST方式则是把内容放在<code>报文内容</code>中，因此只要报文的内容没有限制，它的大小就没有限制。</li>
<li><strong>总结</strong><br />
GET用于获取某个内容，POST用于提交某种数据请求。<br />
按照使用场景来说，一般用户注册的内容属于私密的，这应该使用POST方式；而针对某一内容的查询，为了快速的响应，可以使用GET方式。</li>
</ol>
<h2 id="t5"><a name="t5"></a>无状态协议与Cookie</h2>
<p>HTTP是一种无状态协议，也就是每一次发送都是一次新的开始，服务器并不知道也没有必要知道当前连接的客户端是否之前有过交集，那么当需要进行保存用户登录状态时，则出现了麻烦，这个时候使用Cookie来保存状态。<br />
Cookie会根据服务器端发送的响应报文内的一个叫做<strong>Set-Cookie</strong>的首部字段，通知客户端保存Cookie（保存在自己的电脑里），当下次客户端发送请求时，<strong>Cookie值会被添加到请求报文中发送出去。</strong></p>
<h2 id="t6"><a name="t6"></a>持久连接</h2>
<p>使用浏览器浏览一个包含多张图片的<span class='wp_keywordlink'><a href="http://caibaojian.com/t/html" title="html">html</a></span>页面时，浏览器会发起多次请求，如图所示：</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/44404"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/44404" alt="4440" width="1016" height="444" /></a></p>
<div class="image-caption"></div>
</div>
<p>显而易见每次请求会造成<strong>无谓的TCP连接建立和断开，增加通信量的开销。</strong></p>
<h4 id="t7"><a name="t7"></a>引入持久连接</h4>
<p>持久连接的特点是，只要任意一端没有明确提出断开连接，则保持TCP连接状态。目前HTTP/1.1中默认为持久连接。</p>
<pre class="hljs ruby"><code class="ruby"><span class="hljs-constant">Connection</span><span class="hljs-symbol">:keep-alive</span></code></pre>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/55505"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/55505" alt="5550" width="1015" height="432" /></a></p>
<div class="image-caption"></div>
</div>
<h2 id="t8"><a name="t8"></a>管线化</h2>
<p>管线化可以同时并行发送多个请求，不需要一个一个等待响应了。</p>
<h2 id="t9"><a name="t9"></a>常见的状态码</h2>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/88808"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/88808" alt="8880" width="632" height="372" /></a></p>
<div class="image-caption"></div>
</div>
<h2 id="t10"><a name="t10"></a>确保安全的HTTPS</h2>
<p>HTTP+加密+认证+完整性保护 = HTTPS<br />
一些登陆界面和购物结算界面使用HTTPS通信，也就是改用<code>https://</code>，HTTPS说简单点就是它的通信接口部分被SSL和TLS协议代替而已。</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/77707"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/77707" alt="7770" width="991" height="198" /></a></p>
<div class="image-caption"></div>
</div>
<h2 id="t11"><a name="t11"></a>身份认证</h2>
<p>有一些网址或者服务需要用户的身份信息，因此需要随时知道这些消息，但是肯定不能每次都让用户输入用户密码，因此关于认证就有下面几种方式：</p>
<div class="image-package imagebubble"><a href="http://img.caibaojian.com/uploads/2016/06/99909"><img class="attachment-medium" src="http://img.caibaojian.com/uploads/2016/06/99909" alt="9990" width="389" height="220" /></a></p>
<div class="image-caption"></div>
</div>
<p>在这里主要说一下FormBase认证，也就是<strong>表单认证</strong>。</p>
<h4 id="t12"><a name="t12"></a>使用Cookie来管理Session</h4>
<ol>
<li>客户端把用户IE和密码等登录信息放入报文的实体部分，以<strong>POST</strong>方式发送给服务器。</li>
<li>服务器进行身份认证，产生SessionID，加入到Set-Cookie内，返回给客户端。</li>
<li>客户端接收到SessionID后，将其加入Cookie，下次请求时，浏览器会自动发送Cookie。</li>
</ol>
<blockquote><p>在传输过程中，一种安全地保存密码方式是，先利用给密码加盐的方式增加额外信息，再使用散列hash函数计算出散列值后保存。</p></blockquote>
<p>书籍推荐：《图解HTTP》，轻松理解更全面的HTTP知识。</p>
<blockquote><p>原文：<a href="http://www.jianshu.com/p/81632fea327c" target="_blank">全面理解HTTP</a></p></blockquote>
</div>
