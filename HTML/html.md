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

15、行内元素有哪些？块级元素有哪些？ 空(void)元素有那些？
-------
行内元素：span  a  b  img  input  select  strong i  em    
块级元素：div  ul  ol  li  dl  dt  dd  h1  h2  h3  h4  p  等    
空元素：\<br\>  \<hr\>  \<img\>  \<link\> \<meta\>    
鲜为人知的是：     
    \<area\> \<base\> \<col\> \<command\> \<embed\> \<keygen\> \<param\> \<source\> \<track\> \<wbr\>

16、html5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？
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

17、HTML5中什么是输出元素?
----
当你需要计算两个输入的和值到一个标签中的时候你需要输出元素。显示a+b  
\<form onsubmit="return false" oninput="o.value = parseInt(a.value) + parseInt(b.value)"\>
  \<input name="a" type="number"\> +
  \<input name="b" type="number"\> =
  \<output name="o" /\>
\</form\>
\<output name="o" for="a b"\>\</output\>

18、如何区分 HTML 和 HTML5？
------
DOCTYPE声明\新增的结构元素\功能元素     
1）在文档类型声明上不同：   
HTML是很长的一段代码，很难记住，而HTML5却只有简简单单的声明，方便记忆。    
2）在结构语义上不同：   
HTML：没有体现结构语义化的标签，通常都是这样来命名的\<div id="header"\>\</div\>，这样表示网站的头部。   
HTML5：在语义上却有很大的优势。提供了一些新的标签，比如：\<header\>\<article\>\<footer\>   

19、HTML与XHTML——二者有什么区别
-------
1)所有的标记都必须要有一个相应的结束标记  
2)所有标签的元素和属性的名字都必须使用小写
3)所有的XML标记都必须合理嵌套 
4)所有的属性必须用引号""括起来  
5)把所有<和&特殊符号用编码表示  
6)给所有属性赋一个值   
7)不要在注释内容中使“--”   
8)图片必须有说明文字  

20、Doctype作用？标准模式与兼容模式各有什么区别?
--------
（1）、\<!DOCTYPE\>声明位于位于HTML文档中的第一行，处于 \<html\> 标签之前。告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE不存在或格式不正确会导致文档以兼容模式呈现。    
（2）、标准模式（严格模式）的排版和JS运作模式都是以该浏览器支持的最高标准运行。在兼容模式（在混杂模式中）中，页面以宽松的向后兼容的方式显示，模拟老式浏览器的行为以防止站点无法工作。    

你知道多少种Doctype文档类型？  
该标签可声明三种DTD类型，分别表示严格版本、过渡版本以及基于框架的 HTML 文档。   
HTML 4.01 规定了三种文档类型：Strict、Transitional 以及 Frameset   
XHTML 1.0 规定了三种 XML 文档类型：Strict、Transitional 以及 Frameset。    
Standards （标准）模式（也就是严格呈现模式）用于呈现遵循最新标准的网页，而 Quirks（包容）模式（也就是松散呈现模式或者兼容模式）用于呈现为传统浏览器而设计的网页。    

21、简述一下你对HTML语义化的理解？
-------
1）用正确的标签做正确的事情；
2）html语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析；
3）即使在没有样式css情况下也以一种文档格式显示，并且是容易阅读的；
4）搜索引擎的爬虫也依赖于HTML标记来确定上下文和各个关键字的权重，利于SEO；
5）使阅读源代码的人对网站更容易将网站分块，便于阅读维护理解。

22、HTML5的优点与缺点？
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


23、如何居中div？ 如何居中一个浮动元素？
--------
水平居中和垂直居中：    
水平居中布局（text-align）：   
1)、margin+定宽：width: 100px;margin: 0 auto;
2)、table + margin：display: table;margin: 0 auto;
(display: table 在表现上类似 block 元素，但是宽度为内容宽。)
3)、inline-block + text-align
```css
.child {
	display: inline-block;
}
.parent {
	text-align: center;
}
```
兼容性佳（甚至可以兼容 IE 6 和 IE 7）
4)、 absolute + margin-left
```css
.parent {
	position: relative;
}
.child {
	position: absolute;
   	left:50%;
        width: 100px;
    	margin-left: -50px;  /* width/2 */
}
```
宽度固定
相比于使用transform ，有兼容性更好
5)、 absolute + transform
```css
.parent {
    	position: relative;
}
.child {
  	position: absolute;
   	left: 50%;
   	transform: translateX(-50%);
}
```
绝对定位脱离文档流，不会对后续元素的布局造成影响。
transform 为 CSS3 属性，有兼容性问题
6)、 flex + justify-content
```css
.parent {
	display: flex;
	justify-content: center;
}
```
只需设置父节点属性，无需设置子元素   
flex有兼容性问题   

垂直居中
----
垂直居中：vertical-align:middle;   
父元素高度不确定的文本，图片，块级元素的竖直居中：给父元素设置相同的上下边距实现    
父元素高度确定的单行文本垂直居中：line-height值与父元素的高度值相同    

1)、table-cell + vertical-align
```css
.parent {
	display: table-cell;
	vertical-align: middle;
}
```
兼容性好(IE 8以下版本需要调整页面结构至 table)
2)、absolute + transform
强大的absolute对于这种小问题当然也是很简单的
```css
.parent {
	position: relative;	
}
.child {
    	position: absolute;
    	top: 50%;
    	transform: translateY(-50%);
}
```
绝对定位脱离文档流，不会对后续元素的布局造成影响。但如果绝对定位元素是唯一的元素则父元素也会失去高度。   
transform 为 CSS3 属性，有兼容性问题   
同水平居中，这也可以用margin-top实现，原理水平居中    
3)、flex + align-items
如果说absolute强大，那flex是最强的。但它有兼容问题 
```css
.parent {
 	display: flex;
	align-items: center;
}
```

水平垂直居中
--------
1)、absolute + transform
```css
.parent {
	position: relative;
}
.child {
    	position: absolute;
    	left: 50%;
    	top: 50%;
    	transform: translate(-50%, -50%)
}
```
绝对定位脱离文档流，不会对后续元素的布局造成影响。
transform 为 CSS3 属性，有兼容性问题
2)、inline-block + text-align + table-cell + vertical-align
```css
.parent {
    	text-align: center;
   	display: table-cell;
    	vertical-align: middle;
}
.child {
   	display: inline-block;
}
```
兼容性好
3)、flex + justify-content + align-items
```css
.parent {
	display: flex;
	justify-content: center; /* 水平居中 */
	align-items: center; /*垂直居中*/
}
```
只需设置父节点属性，无需设置子元素
兼容性问题
4)、jQuery、
```javascript
$(window).resize(function(){ 
    $(".mydiv").css({ 
         position: "absolute", 
         left: ($(window).width() - $(".mydiv").outerWidth())/2, 
         top: ($(window).height() - $(".mydiv").outerHeight())/2 
    });        
 }); 
```
 此外在页面载入时，就需要调用resize()。
```javascript
 $(function(){ 
	$(window).resize(); 
 }); 
```
一列定宽，一列自适应
---------
1)、float + margin
```html
<div class="parent">
 	 <div class="left">
  	 </div>
    	 <div class="right">
  	 </div>
 </div>
```
```css
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
```
IE 6 中会有3像素的 BUG，解决方法可以在 .left 加入 margin-left:-3px 当然也有解决这个小bug的方案如下：
```html
<div class="parent">
 	<div class="left">
  	</div>
  	<div class="right-fix">
    		<div class="right">
    		</div>
  	</div>
</div>
```
```css
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
```
此方法不会存在 IE 6 中3像素的 BUG，但 .left 不可选择， 需要设置 .left {position: relative} 来提高层级。 注意此方法增加了不必要的 HTML 文本结构。
2)、float + overflow
```html
<div class="parent">
  	 <div class="left">
  	 </div>
  	 <div class="right">
  	 </div>
</div>
```
```css
 <style>
.left {
    	float: left;
    	width: 100px;
}
.right {
    	overflow: hidden;
}
</style>
```
设置 overflow: hidden 会触发 BFC 模式（Block Formatting Context）块级格式上下文。BFC是什么呢。用通俗的来讲就是，随便你在BFC 里面干啥，外面都不会受到影响 。此方法样式简单但不支持 IE 6   
3)、table
```html
<div class="parent">
  	<div class="left">
  	</div>
  	<div class="right">
  	</div>
</div>
```
```css
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
```
table 的显示特性为每列的单元格宽度和一定等与表格宽度。 table-layout: fixed 可加速渲染，也是设定布局优先。table-cell 中不可以设置 margin 但是可以通过 padding 来设置间距
4)、flex
```html
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
```
```css
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
```
低版本浏览器兼容问题
性能问题，只适合小范围布局
我们在学会一列定宽，一列自适应的布局后也可以方便的实现 多列定宽，一列自适应 多列不定宽加一列自适应

等分布局
-------
1)、float
```html
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
```
```css
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
```
此方法可以完美兼容 IE8 以上版本
2)、flex
```html
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
```
```css
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
```
强大简单，有兼容问题
3)、 table
```html
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
```
```css
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
```

等高布局
-------
1)、table
table 的特性为每列等宽，每行等高可以用于解决此需求
```html
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
```
```css
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
```
2)、flex
```html
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
```
```css
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
```

注意这里实际上使用了 align-items: stretch，flex 默认的 align-items 的值为 stretch
3. float
```html
<div class="parent">
  <div class="left">
  </div>
  <div class="right">
  </div>
</div>
```
```css
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
```
此方法为伪等高（只有背景显示高度相等），左右真实的高度其实不相等。 兼容性较好。布局实现方式多种多样。主要就使用position、flex 、table（从很久很久以前起，我们就抛弃了table布局页面，但display: table;是异常强大）、float等属性目前flex兼容性较差     
  
24、flex的基础知识
-----
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

24、documen.write和 innerHTML的区别
------
document.write只能重绘整个页面    
innerHTML可以重绘页面的一部分    

document.write是直接将内容写入页面的内容流，会导致页面全部重绘，innerHTML将内容写入某个DOM节点，不会导致页面全部重绘。document.write只能重绘整个页面；innerHTML可以重绘页面的一部分    
document.write是直接写入到页面的内容流，如果在写之前没有调用document.open, 浏览器会自动调用open。每次写完关闭之后重新调用该函数，会导致页面被重写。    
innerHTML则是DOM页面元素的一个属性，代表该元素的html内容。你可以精确到某一个具体的元素来进行更改。如果想修改document的内容，则需要修改document.documentElement.innerElement。     

25、页面可见性（Page Visibility）API 可以有哪些用途？
------
（1）通过visibilitystate的值得检测页面当前是否可见，以及打开网页的时间    
（2）在页面被切换到其他后台进程时，自动暂停音乐或视频的播放。    
