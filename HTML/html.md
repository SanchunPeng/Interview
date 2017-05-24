前端面试整理之HTML
=========
1、有哪些常见的meta标签?  
----------
* 指定字符集   
\<meta charset="utf-8"\>  
* 向搜索引擎说明你的网页的关键词    
\<meta name="keywords" content=""\>    
* 告诉搜索引擎你的站点的主要内容    
\<meta name="description" content=""\>    
* 告诉搜索引擎你的站点的制作的作者     
\<meta name="author" content="your name"\>    
* 响应式页面    
\<meta name="viewport" content="width=device-width, initial-scale=1.0"\>    
* 定时让网页在3秒内跳转到mozilla首页(http-equiv 属性为名称/值对提供了名称。并指示服务器在发送实际的文档之前先在要传送给浏览器的 MIME 文档头部包含名称/值对。)         
\<meta http-equiv="refresh" content="3" url=https://www.mozilla.org"\>
* 如果安装了GCF (Google Chrome Frame)，则使用GCF来渲染页面 ("chrome=1"), 如果没有安装GCF，则使用最高版本的IE内核进行渲染 ("IE=edge")。X-UA-Compatible(浏览器采取何种版本渲染当前页面)
\<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"\>
* 浏览器的内核控制
\<meta name="renderer" content="webkit|ie-comp|ie-stand"\>

2、媒介查询
---------
html5要适应各种分辨率的移动设备，应该使用rem这样的尺寸单位。这样页面在不同设备下就能保持一致的网页布局。但是从工作量和复杂度方面来考虑，确有不足。
简单的解决办法是：文字流式布局，控件弹性布局，图片等比缩放。             
\<meta name="viewport"   content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no"\>    
通过document.documentElement.clientWidth获得deviceWidth，然后通过js动态设置html的font-size。            
布局的时候，各元素的css尺寸=设计稿标注尺寸/设计稿横向分辨率/10     
对于容器的font-size，需要通过媒介查询设置font-size,不使用rem：             
@media screen and (max-width:321px){    
    .m-navlist{font-size:15px}            
}      
@media screen and (min-width:321px) and (max-width:400px){   
    .m-navlist{font-size:16px}     
}    
@media screen and (min-width:400px){    
    .m-navlist{font-size:18px}    
}     

3.HTML5 为什么只需要写 <!DOCTYPE HTML>？
-------
Html5不基于SGML，因此不需要对DTD进行引用，但是需要DOCTYPE来规范浏览器的行为（让浏览器按照他们应该的方式来运行）而HTML4.01基于SGML，所以需要对DTD进行引用，才能告知浏览器文档所使用的文档类型。XHTML创建于XML，他被使用在HTML4.0中。      

4、页面导入样式时，使用link和@import有什么区别？
-------------
（1）link属于XHTML标签，除了加载CSS外，还能用于定义RSS, 定义rel连接属性等作用；而@import是CSS提供的，只能用于加载CSS;    
（2）页面被加载的时，link会同时被加载，而@import引用的CSS会等到页面被加载完再加载;    
（3）import是CSS2.1 提出的，只在IE5以上才能被识别，而link是XHTML标签，无兼容问题;    
（4）link方式的样式的权重高于@import的权重。    

5、什么是 FOUC（无样式内容闪烁）？你如何来避免 FOUC？
---------
FOUC - Flash Of Unstyled Content 文档样式闪烁         
 \<style type="text/css" media="all"\>@import "../fouc.css";\</style\> 
而引用CSS文件的@import就是造成这个问题的罪魁祸首。IE会先加载整个HTML文档的DOM，然后再去导入外部的CSS文件，因此，在页面DOM加载完成到CSS导入完成中间会有一段时间页面上的内容是没有样式的，这段时间的长短跟网速，电脑速度都有关系。      
 解决方法简单的出奇，只要在\<head\>之间加入一个\<link\>或者\<script\>元素就可以了。

6、介绍一下你对浏览器内核的理解？
---------
主要分成两部分：渲染引擎(layout engineer或Rendering Engine)和JS引擎。       
渲染引擎：负责取得网页的内容（HTML、XML、图像等等）、整理讯息（例如加入CSS等），以及计算网页的显示方式，然后会输出至显示器或打印机。浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不相同。所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。      
JS引擎则：解析和执行javascript来实现网页的动态效果。     
最开始渲染引擎和JS引擎并没有区分的很明确，后来JS引擎越来越独立，内核就倾向于只指渲染引擎。     

7、常见的浏览器内核有哪些？
---------
Trident内核：IE,MaxThon,TT,The World,360,搜狗浏览器等。[又称MSHTML]   
Gecko内核：Netscape6及以上版本，FF,MozillaSuite/SeaMonkey等     
Presto内核：Opera7及以上。      [Opera内核原为：Presto，现为：Blink;]   
Webkit内核：Safari,Chrome等。   [ Chrome的：Blink（WebKit的分支）]    

8、Label的作用是什么？是怎么用的？
-------
label标签来定义表单控制间的关系,当用户选择该标签时，浏览器会自动将焦点转到和标签相关的表单控件上。   
\<label for="Name"\>Number:\</label\>    
\<input type=“tex e" id="Name"/\>    
\<label>Date:<input type="text" name="B"/\>\</label\>    

9、HTML5的form如何关闭自动完成功能？
---------
给不想要提示的 form 或某个 input 设置为 autocomplete=off。      

10、页面可见性（Page Visibility API） 可以有哪些用途？
------------
通过 visibilityState 的值检测页面当前是否可见，以及打开网页的时间等;     
在页面被切换到其他后台进程的时候，自动暂停音乐或视频的播放；


11、如何在页面上实现一个圆形的可点击区域？
-------
1）、map+area或者svg   
2）、border-radius   
3）、纯js实现 需要求一个点在不在圆上简单算法、获取鼠标坐标等等    


12、实现不使用 border 画出1px高的线，在不同浏览器的标准模式与怪异模式下都能保持一致的效果。 
------
\<div style="height:1px;overflow:hidden;background:red"\>\</div\>

13、网页验证码是干嘛的，是为了解决什么安全问题。
--------
区分用户是计算机还是人的公共全自动程序。可以防止恶意破解密码、刷票、论坛灌水；    
有效防止黑客对某一个特定注册用户用特定程序暴力破解方式进行不断的登陆尝试。    

14、title与h1的区别、b与strong的区别、i与em的区别？
--------
title属性没有明确意义只表示是个标题，H1则表示层次明确的标题，对页面信息的抓取也有很大的影响；    
strong是标明重点内容，有语气加强的含义，使用阅读设备阅读网络时：\<strong\>会重读，而\<B\>是展示强调内容。    
i内容展示为斜体，em表示强调的文本；    
Physical Style Elements -- 自然样式标签    
b, i, u, s, pre    
Semantic Style Elements -- 语义样式标签    
strong, em, ins, del, code   
应该准确使用语义样式标签, 但不能滥用, 如果不能确定时首选使用自然样式标签    

15、Canvas和SVG图形的区别是什么？
--------
SVGCanvas这个就好像绘制和记忆，换句话说任何使用SVG绘制的形状都能被记忆和操作，浏览器可以再次显示Canvas就像绘制和忘记，一旦绘制完成你不能访问像素和操作它SVG对于创建图形例如CAD软件是良好的，一旦东西绘制，用户就想去操作它Canvas在绘制和忘却的场景例如动画和游戏是良好的因为为了之后的操作，需要记录坐标，所以比较缓慢因为没有记住以后事情的意向，所以更快我们可以用绘制对象的相关事件处理我们不能使用绘制对象的相关事件处理，因为我们没有他们的参考分辨率无关分辨率相关

16、行内元素有哪些？块级元素有哪些？ 空(void)元素有那些？
-------
行内元素：span  a  b  img  input  select  strong i  em    
块级元素：div  ul  ol  li  dl  dt  dd  h1  h2  h3  h4  p  等    
空元素：\<br\>  \<hr\>  \<img\>  \<link\> \<meta\>    
鲜为人知的是：     
    \<area\> \<base\> \<col\> \<command\> \<embed\> \<keygen\> \<param\> \<source\> \<track\> \<wbr\>

17、html5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？
-------
是什么：     
HTML5指的是包括 HTML 、CSS 和 JavaScript 在内的一套技术组合。它希望能够减少网页浏览器对于需要插件的丰富性网络应用服务（ Plug-in-Based Rich Internet Application ， RIA ），例如： AdobeFlash 、 Microsoft Silverlight 与 Oracle JavaFX 的需求，并且提供更多能有效加强网络应用的标准集。 HTML5 是 HTML 最新版本， 2014 年 10 月由万维网联盟（ W3C ）完成标准制定。目标是替换 1999 年所制定的 HTML 4.01 和XHTML 1.0 标准，以期能在互联网应用迅速发展的时候，使网络标准达到匹配当代的网络需求。   
为什么：     
HTML4陈旧不能满足日益发展的互联网需要，特别是移动互联网。为了增强浏览器功能 Flash 被广泛使用，但安全与稳定堪忧，不适合在移动端使用（耗电、触摸、不开放）。       
HTML5增强了浏览器的原生功能，符合 HTML5 规范的浏览器功能将更加强大，减少了 Web 应用对插件的依赖，让用户体验更好，让开发更加方便，另外 W3C 从推出 HTML4.0 到 5.0 之间共经历了 17 年， HTML 的变化很小，这并不符合一个好产品的演进规则。      

Html5新增了 27 个元素，废弃了 16 个元素，根据现有的标准规范，把 HTML5 的元素按优先级定义为结构性属性、级块性元素、行内语义性元素和交互性元素 4 大类。      
结构性元素主要负责web上下文结构的定义    
section：在 web 页面应用中，该元素也可以用于区域的章节描述。   
header：页面主体上的头部， header 元素往往在一对 body 元素中。   
footer：页面的底部（页脚），通常会标出网站的相关信息。   
nav：专门用于菜单导航、链接导航的元素，是 navigator 的缩写。   
article：用于表现一篇文章的主体内容，一般为文字集中显示的区域。   
级块性元素主要完成web页面区域的划分，确保内容的有效分割   
aside：用于表达注记、贴士、侧栏、摘要、插入的引用等作为补充主体的内容。   
figure：是对多个元素进行组合并展示的元素，通常与 ficaption 联合使用。   
code：表示一段代码块。   
dialog：用于表达人与人之间的对话，该元素包含 dt 和 dd 这两个组合元素， dt 用于表示说话者，而 dd 用来表示说话内容。    
行内语义性元素主要完成web页面具体内容的引用和描述，是丰富内容展示的基础。    
meter：表示特定范围内的数值，可用于工资、数量、百分比等。   
time：表示时间值。    
progress：用来表示进度条，可通过对其 max 、 min 、 step 等属性进行控制，完成对进度的表示和监事。   
video：视频元素，用于支持和实现视频文件的直接播放，支持缓冲预载和多种视频媒体格式。   
audio：音频元素，用于支持和实现音频文件的直接播放，支持缓冲预载和多种音频媒体格式。   
交互性元素主要用于功能性的内容表达，会有一定的内容和数据的关联，是各种事件的基础。   
details：用来表示一段具体的内容，但是内容默认可能不显示，通过某种手段（如单击）与 legend 交互才会显示出来。   
datagrid：用来控制客户端数据与显示，可以由动态脚本及时更新。   
menu：主要用于交互菜单（曾被废弃又被重新启用的元素）。    
command：用来处理命令按钮。    

新特性，新增元素：   
1）内容元素：article、footer、header、nav、section   
  ● \<header\>：代表HTML的头部数据   
  ● \<footer\>：页面的脚部区域   
  ● \<nav\>：页面导航元素   
  ● \<article\>：自包含的内容   
  ● \<section\>：使用内部article去定义区域或者把分组内容放到区域里   
  ● \<aside\>：代表页面的侧边栏内容   
2）表单控件：calendar、date、time、email、url、search、Telephone、Range(显示范围控制)、Number、Color    
3）控件元素：新的技术：webworker，websockt，Geolocation   
4）多媒体：video、audio、    
5）游戏：绘画canvas、webgl、     
6）存储：localstorage、sessonstorage、websql、indexedDB（使用对象进行存储数据，indexedDB.open()打开或新建某个数据库，     database=indexedDB.open("mydatabase").result。对象存储空间-表，对象-表中的记录，使用keyPath指定键，使用add()，put()向对象存储空间中存储对象，当存储的对象相同时add报错。put则是更新，可以使用database.transaction("users")(即操作users表)进行读取和修改数据。）         

\<audio\> 标签定义声音，比如音乐或其他音频流。 
\<canvas\> 标签定义图形，比如图表和其他图像。\<canvas\> 标签只是图形容器，您必须使用脚本来绘制图形。 
\<article\>标签定义外部的内容。比如来自一个外部的新闻提供者的一篇新的文章，或者来自blog 的文本，或者是来自论坛的文本。亦或是来自其他外部源内容。
\<menu\> 标签定义命令的列表或菜单。\<menu\> 标签用于上下文菜单、工具栏以及用于列出表单控件和命令。 
command 元素表示用户能够调用的命令。\<command\> 标签可以定义命令按钮，比如单选按钮、复选框或 按钮。只有当 command 元素位于 menu 元素内时，该元素才 可见的。否则不会显示这个元素，但是 可以用它规定键盘快捷键。  

移除元素：   
1）显现层元素：basefont，big，center，font，s，strike，tt，u    
2）性能较差元素：frame，frameset，noframes   

处理兼容问题有两种方式：   
1）IE6/IE7/IE8支持通过document.createElement方法产生的标签，利用这一特性让这些浏览器支持HTML5新标签。浏览器支持新标签后，还需要添加标签默认的样式；   
2）使用是html5shim框架    
\<!--[if lt IE 9]\>
  \<script\> src="http://html5shim.googlecode.com/svn/trunk/html5.js"\</script\>
 \<![endif]--\>
另外，DOCTYPE声明的方式是区分HTML和HTML5标志的一个重要因素，此外，还可以根据新增的结构元素header等，功能元素audio等来加以区分。   

HTML5中的datalist是什么？   
HTML5中的Datalist元素有助于提供文本框自动完成特性。    

18、HTML5中什么是输出元素?
----
当你需要计算两个输入的和值到一个标签中的时候你需要输出元素。显示a+b  
\<form onsubmit="return false" oninput="o.value = parseInt(a.value) + parseInt(b.value)"\>
  \<input name="a" type="number"\> +
  \<input name="b" type="number"\> =
  \<output name="o" /\>
\</form\>
\<output name="o" for="a b"\>\</output\>

19、如何区分 HTML 和 HTML5？
------
DOCTYPE声明\新增的结构元素\功能元素     
1）在文档类型声明上不同：   
HTML是很长的一段代码，很难记住，而HTML5却只有简简单单的声明，方便记忆。    
2）在结构语义上不同：   
HTML：没有体现结构语义化的标签，通常都是这样来命名的\<div id="header"\>\</div\>，这样表示网站的头部。   
HTML5：在语义上却有很大的优势。提供了一些新的标签，比如：\<header\>\<article\>\<footer\>   

20、HTML与XHTML——二者有什么区别
-------
1)所有的标记都必须要有一个相应的结束标记  
2)所有标签的元素和属性的名字都必须使用小写
3)所有的XML标记都必须合理嵌套 
4)所有的属性必须用引号""括起来  
5)把所有<和&特殊符号用编码表示  
6)给所有属性赋一个值   
7)不要在注释内容中使“--”   
8)图片必须有说明文字  

21、Doctype作用？标准模式与兼容模式各有什么区别?
--------
（1）、\<!DOCTYPE\>声明位于位于HTML文档中的第一行，处于 \<html\> 标签之前。告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE不存在或格式不正确会导致文档以兼容模式呈现。    
（2）、标准模式（严格模式）的排版和JS运作模式都是以该浏览器支持的最高标准运行。在兼容模式（在混杂模式中）中，页面以宽松的向后兼容的方式显示，模拟老式浏览器的行为以防止站点无法工作。    

你知道多少种Doctype文档类型？  
该标签可声明三种DTD类型，分别表示严格版本、过渡版本以及基于框架的 HTML 文档。   
HTML 4.01 规定了三种文档类型：Strict、Transitional 以及 Frameset   
XHTML 1.0 规定了三种 XML 文档类型：Strict、Transitional 以及 Frameset。    
Standards （标准）模式（也就是严格呈现模式）用于呈现遵循最新标准的网页，而 Quirks（包容）模式（也就是松散呈现模式或者兼容模式）用于呈现为传统浏览器而设计的网页。    

22、简述一下你对HTML语义化的理解？
-------
1）用正确的标签做正确的事情；
2）html语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析；
3）即使在没有样式css情况下也以一种文档格式显示，并且是容易阅读的；
4）搜索引擎的爬虫也依赖于HTML标记来确定上下文和各个关键字的权重，利于SEO；
5）使阅读源代码的人对网站更容易将网站分块，便于阅读维护理解。

23、HTML5的优点与缺点？
---------
优点： 
a、网络标准统一、HTML5本身是由W3C推荐出来的。   
b、多设备、跨平台    
c、即时更新。   
d、提高可用性和改进用户的友好体验；   
e、有几个新的标签，这将有助于开发人员定义重要的内容；   
f、可以给站点带来更多的多媒体元素(视频和音频)；     
g、可以很好的替代Flash和Silverlight；  
h、涉及到网站的抓取和索引的时候，对于SEO很友好；    
i、被大量应用于移动应用程序和游戏。    
缺点：    
a、安全：像之前Firefox4的web socket和透明代理的实现存在严重的安全问题，同时web storage、web socket 这样的功能很容易被黑客利用，来盗取用户的信息和资料。    
b、完善性：许多特性各浏览器的支持程度也不一样。    
c、技术门槛：HTML5简化开发者工作的同时代表了有许多新的属性和API需要开发者学习，像web worker、web socket、web storage 等新特性，后台甚至浏览器原理的知识，机遇的同时也是巨大的挑战    
d、性能：某些平台上的引擎问题导致HTML5性能低下。    
e、浏览器兼容性：最大缺点，IE9以下浏览器几乎全军覆没。    

23、如何实现浏览器内多个标签页之间的通信? (阿里)
--------
1)WebSocket、SharedWorker；   
2)也可以调用localstorge、cookies等本地存储方式；   
localstorge另一个浏览上下文里被添加、修改或删除时，它都会触发storage事件，我们通过监听事件，控制它的值来进行页面信息通信；    
注意quirks：Safari 在无痕模式下设置localstorge值时会抛出 QuotaExceededError 的异常；     

24、WebSocket
-----
WebSocket ：WebSocket protocol 是HTML5一种新的协议。     
目标：在一个单独的持久连接上提供全双工，双向通信。webSocket是一种与服务器进行全双工、双向通信的信道。需要支持这种协议的专门服务器，HTTP服务器无法实现WebSocket，未加密的协议是ws://，加密的协议是wss://,使用自定义协议的好处是传递的数据包很小，适合移动应用。缺点是制定协议的时间比js API时间还要长，必须给websocket构造函数传入绝对URL，同源策略对于Web Sockets完全不适用，因此可以通过它打开到任何站点的连接。     

webSocket如何兼容低浏览器？(阿里)    
Adobe Flash Socket 、   
ActiveX HTMLFile (IE) 、   
基于 multipart 编码发送 XHR 、   
基于长轮询的 XHR（组合SSE和XHR也可以实现双向通信）    
引用WebSocket.js这个文件来兼容低版本浏览器    




18、客户端存储
请你谈谈Cookie的弊端
cookie虽然在持久保存客户端数据提供了方便，分担了服务器存储的负担，但还是有很多局限性的。 第一：每个特定的域名下最多生成20个cookie，在客户端使用document.cookie访问cookie
1）IE6或更低版本最多20个cookie
2）IE7和之后的版本最后可以有50个cookie。
3）Firefox最多50个cookie
4）Opera限制每个域最多30个cookie
5）chrome和Safari没有做硬性限制
IE和Opera 会清理近期最少使用的cookie，Firefox会随机清理cookie。cookie的最大大约为4096字节4KB，为了兼容性，一般不能超过4095字节。IE 提供了一种存储可以持久化用户数据，叫做userData，从IE5.0就开始支持。每个数据最多128K，每个域名下最多1M。这个持久化数据放在缓存中，如果缓存没有清理，那么会一直存在。
优点：极高的扩展性和可用性
1）通过良好的编程，控制保存在cookie中的session对象的大小。
2）通过加密和安全传输技术（SSL），减少cookie被破解的可能性。
3）只在cookie中存放不敏感数据，即使被盗也不会有重大损失。
4）控制cookie的生命期，使之不会永远有效。偷盗者很可能拿到一个过期的cookie。
缺点：
1）`Cookie`数量和长度的限制。每个domain最多只能有20条cookie，每个cookie长度不能超过4KB，否则会被截掉。
2）安全性问题。如果cookie被人拦截了，那人就可以取得所有的session信息。即使加密也与事无补，因为拦截者并不需要知道cookie的意义，他只要原样转发cookie就可以达到目的了。
3）有些状态不可能保存在客户端。例如，为了防止重复提交表单，我们需要在服务器端保存一个计数器。如果我们把这个计数器保存在客户端，那么它起不到任何作用。
所以为了绕开cookie个数限制，一些开发人员使用了子cookie，使用cookie存储多个名值对，用&连接。
cookie的构造：cookie的名称不区分大小写，可以设置cookie域，路径，，失效时间（没定义的话是会话结束失效），安全标志（SSL）e.g.Set-Cookie:name-value;domain=.wrox.com;path=/;secure，指定在.erox.com域或子域下的所有路径发送https请求时都会带上cookie。都会进行URL编码。

web storage和cookie的区别
Web Storage的概念和cookie相似，区别是它是为了更大容量存储，在cookie之外存储会话数据的途径设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。除此之外，Web Storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。

Web Storage定义了两种用于存储数据的对象，sessionStorage和localStorage，前者严格用于在一个浏览器会话中存储数据，浏览器关闭后立即删除，后者用户跨会话持久化数据并遵循跨域安全策略。要访问同一个localStorage对象，页面必须来自同一个域名（子域名无效），同一种协议，同一端口。

但是Cookie也是不可以或缺的：Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。浏览器的支持除了IE７及以下不支持外，其他标准浏览器都完全支持(ie及FF需在web服务器里运行)，值得一提的是IE总是办好事，例如IE7、IE6中的UserData（可以应用在页面的某个元素上，每个文档最多128KB，每个域名最多1M，在使用userData之前需要再DOM元素上使用style的behavior属性，通过setAttribute()保存数据，最后必须使用save(数据空间名称)指定数据空间名称，最后就可以使用load(数据空间名称)方法指定同样的数据空间名称来获取数据，每次添加或者删除后都要使用save进行提交更改，用户数据会跨越会话永久存在，所以需要使用removeAttribute删除释放，也不安全）其实就是javascript本地存储的解决方案。所以通过简单的代码封装可以统一到所有的浏览器都支持web storage。很容就能够兼容。

在较高版本的浏览器（IE8+）中，js提供了sessionStorage和globalStorage。在HTML5中提供了localStorage来取代globalStorage。html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
localStorage和sessionStorage都具有相同的操作方法，例如（setItem、getItem）（也可以用点的方式存取）和removeItem,key(index)：获得index位置处的值的名字等，也可以通过Storage对象间接调用，因为localStorage，sessionStorage都是Storage对象的实例。。

sessionStorage：可以跨页面刷新存在，浏览器崩溃后重启依然可用（IE不行）只能由最初给对象存储数据的页面可以访问，所以对多页应用有限制。IE8可以通过这个对象将数据写入磁盘，设置数据之前使用sessionStorage.begin()。成功之后使用sessionStorage.commit()，主要用于仅针对会话的小段数据存储，如果需要跨越会话存储数据，用localStorage或globalStorage更合适。

globalStorage：跨越会话存储数据，globalStorage不是Storage实例，使用globalStorage["wrox.com"]确定针对特定域名的存储空间，globalStorage["wrox.com"]是Storage实例，也就是该域可以访问存储在上面的数据。同时也要遵从类似同源策略的规则设置和访问。没有使用removeItem()或者delete删除，或者用户没有清除浏览器缓存，globalStorage中存储的数据会一直保存在磁盘上，适合存储文档或用户偏好设置。

localStorage：是Storage实例，该对象在HTML5规范中作为持久保存客户端的数据的方案取代了globalStorage。访问规则事先也设定，相当于globalStorage[location.host]。
对localStorage，globalStorage，sessionStorage进行操作都会触发storage事件。

请描述一下 cookies，sessionStorage 和 localStorage 的区别
cookie是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。
cookie数据始终在同源的http请求中携带（即使不需要），记会在浏览器和服务器间来回传递。
sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。

CookiesLocal storage客户端/服务端客户端和服务端都能访问数据。Cookie的数据通过每一个请求发送到服务端只有本地浏览器端可访问数据，服务器不能访问本地存储直到故意通过POST或者GET的通道发送到服务器大小每个cookie有4095byte每个域5MB，chrome和safari限制是2.5MB过期Cookies有有效期，所以在过期之后cookie和cookie数据会被删除没有过期数据，无论最后用户从浏览器删除或者使用Javascript程序删除，我们都需要删除
存储大小：
    cookie数据大小不能超过4k。
    sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5MB或更大，Chrome和Safari是2.5MB。
有期时间：
    localStorage    存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；
    sessionStorage  数据在当前浏览器窗口关闭后自动删除。
    cookie          设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭,或者过期时间

本地存储和事务存储之间的区别:
本地存储数据持续永久，但是会话在浏览器打开时有效知道浏览器关闭时会话变量重置

19、HTML5的离线储存怎么使用，工作原理能不能解释一下？
离线检测：
HTML5定义了一个navigator.onLine属性，ture表示能上网，由于浏览器兼容问题，配合使用online和offline事件，当网络从在线转为离线触发offline，转为在线触发online，都在window对象上触发。
离线储存：
所谓的离线存储就是将一些资源文件保存在本地，这样后续的页面加载将使用本地的资源文件，在 离线的情况下可以继续访问web应用。在用户与因特网连接时，更新用户机器上的缓存文件。
原理：HTML5的离线存储是基于一个新建的.appcache文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像cookie一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。
localStorage 长期存储数据，浏览器关闭后数据不丢失；
localStorage.setItem("key","value");//数据添加到本地存储采用键值对,value也可以是一个js对象。localStorage.setItem(“I001”,JSON.stringify(country));//也可以存储json格式的数据
var item = localStorage.getItem("key");//从本地存储中检索数据
本地存储没有生命周期，它将保留直到用户从浏览器清除或者使用Javascript代码移除。
sessionStorage 数据在浏览器关闭后自动删除。
如何使用：
1)、页面头部像下面一样加入一个manifest的属性；将描述文件与页面关联起来<html manifest="/offine.manifest">
2)、在cache.manifest文件的编写离线存储的资源;现在推荐描述文件扩展名为appcache.
    CACHE MANIFEST
    #v0.11
    CACHE:
    js/app.js
    css/style.css
    NETWORK:
    resourse/logo.png
    FALLBACK:
    / /offline.html
3)、在离线状态时，操作window.applicationCache进行需求实现。


HTML5中的应用缓存
一个最需要的事最终是用户的离线浏览，换句话说，如果网络连接不可用时，页面应该来自浏览器缓存，离线应用缓存可以帮助你达到这个目的，应用缓存可以帮助你指定哪些文件需要缓存，哪些不需要。
1)如何实现应用缓存：首先我们需要指定”manifest”文件，“manifest”文件帮助你定义你的缓存如何工作
CACHE MANIFEST          //所有manifest文件都以“CACHE MANIFEST”语句开始.
# version 1.0     //#（散列标签）有助于提供缓存文件的版本.
CACHE :           //CACHE 命令指出哪些文件需要被缓存.Mainfest文件的内容类型应是“text/cache-manifest”.
Login.aspx
2)创建一个缓存manifest文件以后，接下来的事情实在HTML页面中提供mainfest连接，如下所示：
<html manifest="cache.aspx">
当以上文件第一次运行，他会添加到浏览器应用缓存中，在服务器宕机时，页面从应用缓存中获取
3)应用缓存通过变更“#”标签后的版本版本号而被移除
4)应用缓存中的回退帮助你指定在服务器不可访问的时候，将会显示某文件。例如在下面的manifest文件中，我们说如果谁敲击了”/home”同时服务器不可到达的时候，”homeoffline.html”文件应送达.
FALLBACK:
/home/ /homeoffline.html
5)NETWORK:                                                        //不需要缓存的文件
home.aspx

浏览器是怎么对HTML5的离线储存资源进行管理和加载
在线的情况下，浏览器发现html头部有manifest属性，它会请求manifest文件，如果是第一次访问app，那么浏览器就会根据manifest文件的内容下载相应的资源并且进行离线存储。如果已经访问过app并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的manifest文件与旧的manifest文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。
离线的情况下，浏览器就直接使用离线存储的资源。
步骤：
（1）html5是使用一个manifest文件来标明那些文件是需要被存储，对于manifest文件，文件的 
mime-type必须是text/cache-manifest类型。
（2）cache manifest下直接写需要缓存的文件，这里指明文件被缓存到浏览器本地；在network下指明 
的文件，强制必须通过网络资源获取的；在failback下指明是一种失败的回调方案，比如无法访问，就 
发出404.htm请求。

20、iframe有那些缺点？
1）在网页中使用框架结构最大的弊病是搜索引擎的检索程序无法解读这种页面，不利于SEO;
2）iframe会阻塞主页面的Onload事件；
3）iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。
4）框架结构有时会让人感到迷惑，页面很混乱；
使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题。

iframe的优缺点
1）<iframe>优点：
    解决加载缓慢的第三方内容如图标和广告等的加载问题
    Security sandbox
    并行加载脚本
2）<iframe>的缺点：
    *iframe会阻塞主页面的Onload事件；
    *即时内容为空，加载也需要时间
    *没有语意

21、如何居中div？ 如何居中一个浮动元素？
水平居中和垂直居中：
水平居中布局（text-align）：
1)、margin+定宽：width: 100px;margin: 0 auto;
2)、table + margin：display: table;margin: 0 auto;
(display: table 在表现上类似 block 元素，但是宽度为内容宽。)
3)、inline-block + text-align
.child {
		display: inline-block;
      }
      .parent {
text-align: center;
	}
兼容性佳（甚至可以兼容 IE 6 和 IE 7）
4)、 absolute + margin-left
.parent {
		position: relative;
  	}
.child {
		position: absolute;
   		left:50%;
        	width: 100px;
    		margin-left: -50px;  /* width/2 */
	}
宽度固定
相比于使用transform ，有兼容性更好
5)、 absolute + transform
  	.parent {
    		position: relative;
  	}
  	.child {
  	 	position: absolute;
   		left: 50%;
   		transform: translateX(-50%);
  	}

绝对定位脱离文档流，不会对后续元素的布局造成影响。
transform 为 CSS3 属性，有兼容性问题
6)、 flex + justify-content
	.parent {
		display: flex;
		justify-content: center;
	}

只需设置父节点属性，无需设置子元素
flex有兼容性问题
垂直居中
垂直居中：vertical-align:middle;
父元素高度不确定的文本，图片，块级元素的竖直居中：给父元素设置相同的上下边距实现
父元素高度确定的单行文本垂直居中：line-height值与父元素的高度值相同

1)、table-cell + vertical-align
.parent {
		display: table-cell;
		vertical-align: middle;
 	}

兼容性好(IE 8以下版本需要调整页面结构至 table)
2)、absolute + transform
强大的absolute对于这种小问题当然也是很简单的
.parent {
		position: relative;	
}
.child {
    		position: absolute;
    		top: 50%;
    		transform: translateY(-50%);
}

绝对定位脱离文档流，不会对后续元素的布局造成影响。但如果绝对定位元素是唯一的元素则父元素也会失去高度。
transform 为 CSS3 属性，有兼容性问题
同水平居中，这也可以用margin-top实现，原理水平居中
3)、flex + align-items
如果说absolute强大，那flex是最强的。但它有兼容问题
	.parent {
 		display: flex;
		align-items: center;
  	}


水平垂直居中
1)、absolute + transform
.parent {
		position: relative;
}
.child {
    		position: absolute;
    		left: 50%;
    		top: 50%;
    		transform: translate(-50%, -50%)
}

绝对定位脱离文档流，不会对后续元素的布局造成影响。
transform 为 CSS3 属性，有兼容性问题
2)、inline-block + text-align + table-cell + vertical-align
.parent {
    		text-align: center;
   		 display: table-cell;
    		vertical-align: middle;
}
.child {
   		display: inline-block;
}

兼容性好
3)、flex + justify-content + align-items
.parent {
		display: flex;
		justify-content: center; /* 水平居中 */
		align-items: center; /*垂直居中*/
}

只需设置父节点属性，无需设置子元素
兼容性问题
4)、jQuery、
$(window).resize(function(){ 
    $(".mydiv").css({ 
         position: "absolute", 
         left: ($(window).width() - $(".mydiv").outerWidth())/2, 
         top: ($(window).height() - $(".mydiv").outerHeight())/2 
    });        
 }); 
 此外在页面载入时，就需要调用resize()。
 $(function(){ 
	$(window).resize(); 
 }); 
##一列定宽，一列自适应
1)、float + margin
<div class="parent">
 	 <div class="left">
  	 </div>
    	 <div class="right">
  	 </div>
     </div>
     <style>
     .left {
float: left;
    	    width: 100px;
}
    .right {
    	     margin-left: 100px
   	     /*间距可再加入 margin-left */
}
    </style>

IE 6 中会有3像素的 BUG，解决方法可以在 .left 加入 margin-left:-3px 当然也有解决这个小bug的方案如下：
<div class="parent">
 	 <div class="left">
  	</div>
  	<div class="right-fix">
    		<div class="right">
    		</div>
  	</div>
    </div>
    <style>
  	.left {
    		float: left;
    		width: 100px;
  	}
  	.right-fix {
    		float: right;
    		width: 100%;
    		margin-left: -100px;
 	}
  	.right {
    		margin-left: 100px
    		/*间距可再加入 margin-left */
  	}
</style>

此方法不会存在 IE 6 中3像素的 BUG，但 .left 不可选择， 需要设置 .left {position: relative} 来提高层级。 注意此方法增加了不必要的 HTML 文本结构。
2)、float + overflow
<div class="parent">
  	 <div class="left">
  	 </div>
  	 <div class="right">
  	 </div>
      </div>
      <style>
  	.left {
    		float: left;
    		width: 100px;
  	}
  	.right {
    		overflow: hidden;
  	}
      </style>

设置 overflow: hidden 会触发 BFC 模式（Block Formatting Context）块级格式上下文。BFC是什么呢。用通俗的来讲就是，随便你在BFC 里面干啥，外面都不会受到影响 。此方法样式简单但不支持 IE 6
3)、table
<div class="parent">
  	<div class="left">
  	</div>
  	<div class="right">
  	</div>
     </div>
     <style>
  	.parent {
  		display: table;
 		width: 100%;
		table-layout: fixed;
  	}
 	.left {
		display: table-cell;
		width: 100px;
 	}
  .right {
	display: table-cell;
	/*宽度为剩余宽度*/
  }
</style>

table 的显示特性为每列的单元格宽度和一定等与表格宽度。 table-layout: fixed 可加速渲染，也是设定布局优先。table-cell 中不可以设置 margin 但是可以通过 padding 来设置间距
4)、flex
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
<style>
  .parent {
   display: flex;
  }
  .left {
	width: 100px;
   	margin-left: 20px;
  }
  .right {
    	flex: 1;
  }
</style>

低版本浏览器兼容问题
性能问题，只适合小范围布局
我们在学会一列定宽，一列自适应的布局后也可以方便的实现 多列定宽，一列自适应 多列不定宽加一列自适应

等分布局
1)、float
<div class="parent">
	<div class="column">
	</div>
	<div class="column">
 	</div>
 	<div class="column">
 	</div>
 	<div class="column">
 	</div>
</div>
<style>
  .parent {
	margin-left: -20px;
  }
  .column {
	float: left;
	width: 25%;
	padding-left: 20px;
	box-sizing: border-box;
  }
</style>

此方法可以完美兼容 IE8 以上版本
2)、flex
<div class="parent">
	<div class="column">
	</div>
	<div class="column">
 	</div>
 	<div class="column">
 	</div>
 	<div class="column">
 	</div>
</div>
<style>
  .parent {
   display: flex;
  }
  .column {
	flex: 1;
  }
  .column+.column { /* 相邻兄弟选择器 */
	margin-left: 20px;
  }
</style>

强大简单，有兼容问题
3)、 table
<div class='parent-fix'>
  	<div class="parent">
    		<div class="column">
    		</div>
    		<div class="column">
    		</div>
    		<div class="column">
   		 </div>
    		<div class="column">
    		</div>
  	</div>
</div>
<style>
  .parent-fix {
	margin-left: -20px;
  }
  .parent {
	display: table;
	width: 100%;
	/*可以布局优先，也可以单元格宽度平分在没有设置的情况下*/
	table-layout: fixed;
  }
  .column {
	display: table-cell;
	padding-left: 20px;
  }
</style>


等高布局
1)、table
table 的特性为每列等宽，每行等高可以用于解决此需求
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
<style>
  .parent {
    display: table;
    width: 100%;
    table-layout: fixed;
  }
  .left {
    display: table-cell;
    width: 100px;
  }
  .right {
    display: table-cell
    /*宽度为剩余宽度*/
  }
</style>

2)、flex
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
<style>
  .parent {
    display: flex;
  }
  .left {
    width: 100px;
    margin-left: 20px;
  }
  .right {
    flex: 1;
  }
</style>

注意这里实际上使用了 align-items: stretch，flex 默认的 align-items 的值为 stretch
3. float
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
<style>
  .parent {
    overflow: hidden;
  }
  .left,
  .right {
    padding-bottom: 9999px;
    margin-bottom: -9999px;
  }
  .left {
    float: left;
    width: 100px;
    margin-right: 20px;
  }
  .right {
    overflow: hidden;
  }
</style>

此方法为伪等高（只有背景显示高度相等），左右真实的高度其实不相等。 兼容性较好。布局实现方式多种多样。主要就使用position、flex 、table（从很久很久以前起，我们就抛弃了table布局页面，但display: table;是异常强大）、float等属性目前flex兼容性较差 
  
flex 的核心的概念就是容器和轴。容器包括外层的父容器和内层的子容器，轴包括主轴和交叉轴
父容器：
设置子容器沿主轴如何排列：justify-content
justify-content: flex-start | flex-end | center | space-between | space-around;
flex-start：起始端对齐；flex-end：末尾端对齐；center：居中对齐；space-around：子容器沿主轴均匀分布，位于首尾两端的子容器到父容器的距离是子容器间距的一半；space-between：子容器沿主轴均匀分布，位于首尾两端的子容器与父容器相切。
设置子容器沿交叉轴如何排列：align-items
 align-items: flex-start | flex-end | center | baseline | stretch;
有flex-start：起始端对齐；flex-end：末尾段对齐；center：居中对齐；stretch：子容器沿交叉轴方向的尺寸拉伸至与父容器一致。
设置换行方式：flex-wrap（决定子容器是否换行排列）
flex-wrap: nowrap | wrap | wrap-reverse;
nowrap：不换行；wrap：换行（沿着交叉轴的正方向换行）；wrap-reverse：逆序换行（沿着交叉轴的反方向换行）
align-content：当子容器多行排列时，设置行与行之间的对齐方式。沿交叉轴对齐，属性值和justify-content相同只是相对交叉轴，如果项目只有一根轴线，该属性不起作用

子容器：
在主轴上如何伸缩：flex
flex属性是flex-grow（放大比例）, flex-shrink（缩小比例） 和 flex-basis的简写，默认值为0 1 auto，该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
flex 即弹性，会自动填充剩余空间，子容器的伸缩比例由 flex属性确定。
单独设置子容器如何沿交叉轴排列：align-self
如果子容器和父容器同时设置了该值，以子容器为准。该属性允许单个子容器有与其他子容器不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch，其他属性值和align-items的属性值一样
order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0

轴：
在 flex 布局中，flex-direction 属性决定主轴的方向，交叉轴的方向由主轴确定。
flex-direction: row | row-reverse | column | column-reverse;
row，向右，column,向下，row-reverse，向左，column-reverse,向上,主轴沿逆时针方向旋转 90° 就得到了交叉轴
flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap

CSS3的flex布局
flex的作用是能够按照设置好的规则来排列容器内的项目，而不必去计算每一个项目的宽度和边距。甚至是在容器的大小发生改变的时候，都可以重新计算，以至于更符合预期的排版。
（1）   display：flex|inline-flex；flex：相当于block；inline-flex：相当于inline-block
（2）   flex-direction（流动布局的主轴方向）：row（默认）； row-reverse：行反方向；column：列方向；  column-reverse：列方向相反
（3）   flex-wrap如果轴线放不下，应该如何换行。nowrap（默认）：不换行；wrap：换行，第一行在上方；wrap-reverse:换行，第一张在下方。
（4）   flex-flow（“flex-direction”和“flex-wrap”属性的缩写），row nowrap为其默认属性值，分别表示flex-direction和flex-wrap属性。
（5）   justify-content（主轴方向内容对齐方式）；
flex-srart（默认）：与主轴起始方向对齐；flex-end：向主轴终点方向对齐。
center：向主轴中点方向对齐。
space-between：起始位置向主轴起始方向对齐，终点位置向主轴终点方向对齐，其余位置向主轴中点方向对齐。
space-around：与space-between类似，只是起始位置和终点位置保留一半空白。
（6）   align-content（多个主轴沿侧轴方向的内容堆栈对齐方式）
flex-start：多个主轴沿侧轴起始方向对齐；flex-end：多个主轴沿侧轴终点方向对齐。
center：多个主轴沿侧轴中点方向对齐；space-between：第一个主轴沿主轴起始方向对齐，末尾主轴沿主轴终点方向对齐，其他主轴均匀分布对齐。
space-around：与space-between类似，只是侧轴起始位置和侧轴终点位置保留一半空白；stretch（默认）：伸缩多个主轴，保持侧轴方向统一距离。
（7）   align-items（侧轴方向的内容对齐方式）
flex-srart：与侧轴起始方向对齐；flex-end：向侧轴终点方向对齐。
center：向侧轴中点方向对齐；baseline：在侧轴上保持基线对齐，以第一个项目的基线为准。
stretch（默认）：在侧轴方向拉伸每个项目，使每个项目保持相同的起始位置和终点位置。
（8）项目属性   order(排序，项目按照order值从小到大排列)
  flex-grow(空白空间分配比例)  flex-shrink(项目空间的分配比例)

22、线程与进程的区别
一个程序至少有一个进程,一个进程至少有一个线程. 
线程的划分尺度小于进程，使得多线程程序的并发性高。 
另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。 
线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。 
从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

23、documen.write和 innerHTML的区别
document.write只能重绘整个页面
innerHTML可以重绘页面的一部分

document.write是直接将内容写入页面的内容流，会导致页面全部重绘，innerHTML将内容写入某个DOM节点，不会导致页面全部重绘。document.write只能重绘整个页面；innerHTML可以重绘页面的一部分
document.write是直接写入到页面的内容流，如果在写之前没有调用document.open, 浏览器会自动调用open。每次写完关闭之后重新调用该函数，会导致页面被重写。
innerHTML则是DOM页面元素的一个属性，代表该元素的html内容。你可以精确到某一个具体的元素来进行更改。如果想修改document的内容，则需要修改document.documentElement.innerElement。

页面可见性（Page Visibility）API 可以有哪些用途？
（1）通过visibilitystate的值得检测页面当前是否可见，以及打开网页的时间
（2）在页面被切换到其他后台进程时，自动暂停音乐或视频的播放。
