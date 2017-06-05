前端面试整理之CSS
=======
1、css的display：none和visibility:hidden区别
---------
display:none使用后，元素的宽度，高度都会丢失，视为不存在不加载；元素原来占据的空间位置不保留；产生回流和重绘；<br/>
visibility:hidden:视觉上的不可见，但是保留占据的空间，还具有宽度和高度；<br/>

2、box-sizing ： content-box || border-box || inherit
-------
content-box：border和padding不计算入width之内;<br/>
padding-box：padding计算入width内;<br/>
border-box：border和padding计算入width之内，其实就是怪异模式了;<br/>
当为border-box,则让元素维持在了IE传统模式下的怪异模式，即，设置的元素的width和height都是包括元素的宽度和padding和border。可以运用到布局（因为当其内元素和父元素宽度相等时没有问题但是只要加了一点padding和margin整个布局就崩溃了）和表单元素（表单中除了checkbox和radio外默认都是2px的border）上。<br/>

3、浏览器渲染
---------
DOM:浏览器将HTML解析成树形结构，即DOM。<br/>
CSSDOM:将css解析成树形结构，即CSSDOM。<br/>
Render Tree:DOM 和 CSSDOM合并后生成Render Tree。<br/>
Layout：计算Render Tree每个节点的具体位置。<br/>
Painting：将layout后的节点内容呈现在屏幕上；<br/>
遇到外部的css文件和图片，浏览器会另外发出一个请求，来获取css文件和相应的图片，这个请求是异步的，并不会影响html文件。如果遇到javascript文件，html文件会挂起渲染的线程，等待javascript加载完毕后，html文件再继续渲染。<br/>

Repaint——(重绘)是在一个元素的外观被改变，但没有改变布局的情况下发生。如果只是改变某个元素的背景色、文字颜色、边框颜色等等不影响它周围或内部布局的属性，将只会引起浏览器repaint。<br/>
Reflow——（回流）：浏览器发现某个部分发生了点变化影响了布局，需要倒回去重新渲染，这个回退的过程就叫回流。意味着元件的几何尺寸变了，我们需要重新验证并计算 Render Tree。<br/>

4、display有哪些值？说明他们的作用。
---------
display 的属性值有：none|inline|block|inline-block|list-item|run-in|table|inline-table|table-row-group|table-header-group|table-footer-group|table-row|table-column-group|table-column|table-cell|table-caption|inherit
其中常用的的有none、inline、block、inline-block。分别的意思是：<br/>
1）none： 元素不会显示，而且该元素现实的空间也不会保留。但有另外一个 visibility: hidden， 是保留元素的空间的。<br/>
2）inline： display的默认属性。将元素显示为内联元素，元素前后没有换行符。我们知道内联元素是无法设置宽高的，所以一旦将元素的display 属性设为inline，设置属性height和width是没有用的。此时影响它的高度一般是内部元素的高度（font-size）和padding。<br/>
3）block： 将元素将显示为块级元素，元素前后会带有换行符。设置为block后，元素可以设置width和height了。元素独占一行。<br/>
4）inline-block：行内块元素。这个属性值融合了inline 和 block 的特性，即是它既是内联元素，又可以设置width和height。<br/>
5）inherit：规定应该从父元素继承 display 属性的值。<br/>
6）table：此元素会作为块级表格来显示（类似 \<table\>），表格前后带有换行符。<br/>
内联元素：\<a\>、\<span\>、\<br\>、\<i\>、\<em\>、\<strong\>、\<label\>、\<q\>、\<var\>、\<cite\>、\<code\><br/>
块级元素：\<div\>、\<p\>、\<h1\>...\<h6\>、\<ol\>、\<ul\>、\<dl\>、\<table\>、\<address\>、\<blockquote\> 、\<form\><br/>
内联块状元素：\<img\>、\<input\><br/>
其他display的属性值不是很常用，其具体的含义如下：<br/>
list-item：此元素会作为列表显示，块类型元素一样显示。<br/>
run-in：此元素会根据上下文作为块级元素或内联元素显示。<br/>
inline-table：此元素会作为内联表格来显示（类似 \<table\>），表格前后没有换行符。<br/>
table-row-group：此元素会作为一个或多个行的分组来显示（类似 \<tbody\>）。<br/>
table-header-group：此元素会作为一个或多个行的分组来显示（类似 \<thead\>）。<br/>
table-footer-group： 此元素会作为一个或多个行的分组来显示（类似 \<tfoot\>）。<br/>
table-row：此元素会作为一个表格行显示（类似 \<tr\>）。<br/>
table-column-group：此元素会作为一个或多个列的分组来显示（类似\<colgroup\>）。<br/>
table-column：此元素会作为一个单元格列显示（类似 \<col\>）<br/>
table-cell：此元素会作为一个表格单元格显示（类似 \<td\> 和 \<th\>）<br/>
table-caption：此元素会作为一个表格标题显示（类似 \<caption\>）<br/>
inherit： 规定应该从父元素继承 display 属性的值。<br/>

display:inline-block 什么时候会显示间隙？(携程)<br/>
移除空格、使用margin负值、使用font-size:0、letter-spacing、word-spacing<br/>

4、介绍一下 CSS 的盒子模型？
--------
1）有两种，IE 盒子模型、标准 W3C 盒子模型； IE 的 width包含了content，border 和 padding；<br/>
2）盒模型：内容（content）、填充（padding）、边界（margin）、边框（border）。<br/>


5、CSS 选择符有哪些？哪些属性可以继承？优先级算法如何计算？ CSS3 新增伪类有哪些？
-------
1）id 选择器（#myid）<br/>
2）类选择器（.myclassname）<br/>
3）标签选择器（div，h1，p）<br/>
4）相邻选择器（h1 + p）<br/>
5）子选择器（ul > li）<br/>
6）后代选择器（li a）<br/>
7）通配符选择器<br/>
8）属性选择器（ a[rel = "external"]）<br/>
9）伪类选择器（a: hover, li: nth - child）<br/>
可继承的样式： font-size font-family color, UL LI DL DD DT text-indent（一些font类和text类都可以继承）<br/>
不可继承的样式：border padding margin width height<br/>
优先级就近原则，同权重情况下样式定义最近者为准 载入样式以最后载入的定位为准; <br/>
载入样式以最后载入的定位为准;<br/>
优先级为: !important >  id > class=伪类 > tag  ；   important 比内联优先级高，内联比id优先级高 <br/>
继承得到的样式的优先级最低。<br/>

6、CSS3新特性以及新增伪类举例：
---------
新增各种CSS选择器  （: not(.input)：所有 class 不是“input”的节点）<br/>
圆角           （border-radius:8px） <br/>
多列布局        （multi-column layout） <br/>
阴影和反射        （box-Shadow\Reflect） <br/>
文字特效      （text-shadow、） <br/>
文字渲染      （Text-decoration）<br/>
线性渐变      （gradient）<br/>
旋转          （transform）<br/>
transform:rotate(9deg) scale(0.85,0.90) translate(0px,-30px) skew(-9deg,0deg)  Animation rgba;//旋转，缩放，定位，倾斜，动画 多背景 
增加了更多的 css 选择器 <br/>

p:first-of-type   选择该p元素是其父元素的首个 \<p\> 元素；<br/>
p:last-of-type   选择该p元素是其父元素的最后 \<p\> 元素；<br/>
p:only-of-type  选择该p元素是其父元素唯一的 \<p\> 元素，还是可以有其他的元素，只要p元素只有一个；<br/>
p:only-child    选择该p元素是其父元素的唯一子元素；<br/>
p:nth-child(n)  选择属于其父元素的第n个p子元素(排序的时候是和其他子元素一起的排序中选取第几个元素，不是单独p元素排序)；p:nth-child(odd) 匹配所以奇数行;p:nth-child(even) 匹配所有偶数行，元素的第一个子元素索引为“1”。p:nth-child(3n+1)三层循环中的第一行，可以是数字，关键词或公式：使用公式 (an + b)。描述：表示周期的长度，n 是计数器（从 0 开始），b 是偏移值。<br/>
:after          在元素之后添加内容,也可以用来做清除浮动。<br/>
:before         在元素之前添加内容<br/>
:enabled        <br/>
:disabled       控制表单控件的禁用状态。<br/>
:checked        单选框或复选框被选中。<br/>

7、position的值relative和absolute定位原点是？
------------
absolute<br/>
    生成绝对定位的元素，相对于值不为 static的第一个父元素进行定位。<br/>
fixed （老IE不支持）<br/>
    生成绝对定位的元素，相对于浏览器窗口进行定位。<br/>
relative<br/>
    生成相对定位的元素，相对于其正常位置进行定位。<br/>
static<br/>
    默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right z-index 声明）。<br/>
inherit<br/>
    规定从父元素继承 position 属性的值。<br/>


8、position的absolute与fixed共同点与不同点
-------------
A：共同点：<br/>
1）.改变行内元素的呈现方式，display被置为block；<br/>
2）.让元素脱离普通流，不占据空间；<br/>
3）.默认会覆盖到非定位元素上;<br/>
B：不同点：<br/>
absolute的”根元素“是可以设置的，而fixed的”根元素“固定为浏览器窗口。当你滚动网页，fixed元素与浏览器窗口之间的距离是不变的。<br/>

9、请解释一下CSS3的Flexbox（弹性盒布局模型）,以及适用场景？
---------
 一个用于页面布局的全新CSS3功能，Flexbox可以把列表放在同一个方向（从上到下排列，从左到右），并让列表能延伸到占用可用的空间。<br/>
该布局模型的目的是提供一种更加高效的方式来对容器中的条目进行布局、对齐和分配空间。在传统的布局方式中，block 布局是把块在垂直方向从上到下依次排列的；而inline 布局则是在水平方向来排列。弹性盒布局并没有这样内在的方向限制，可以由开发人员自由操作。<br/>
试用场景：弹性布局适合于移动前端开发，在Android和ios上也完美支持<br/>

10、用纯CSS创建一个三角形的原理是什么？
-------
把上、左、右三条边隐藏掉（颜色设为 transparent）<br/>
```css
div#demo {
  width: 0;
  height: 0;
  border-width: 20px;
  border-style: solid;
  border-color: transparent transparent red transparent;
}
```
注：IE6下将边框设置为transparent并不会透明，会产生阴影，可设置为border-style:dashed dashed solid dashed;<br/>

用CSS3 transfrom旋转45度实现<br/>
```css
-webkit-transform: rotate(45deg); 
-moz-transform: rotate(45deg);
-ms-transform: rotate(45deg);
-o-transform: rotate(45deg);
transform: rotate(45deg);
```

11、常用的css hack的技巧 ？
--------- 
##### 条件HACK：<br/>
\<!--[if 符号(gt,gte,lt,lte,!) IE 版本(6,7...)]
Html/Css
--\>

##### 属性级HACK：<br/>
     _：选择IE6及以下。连接线（中划线）（-）亦可使用，为了避免与某些带中划线的属性混淆，所以使用下划线更为合适。<br/>
        *选择IE7及以下。诸如：（+）与（#）之类的均可使用，不过业界对（*）的认知度更高<br/>
\9：选择IE6+<br/>
\0：选择IE8+和Opera15以下的浏览器<br/>
e.g.<br/>
```css
.bb{
    background-color:#f1ee18;/*所有识别*/
    .background-color:#00deff\9; /*IE6、7、8识别*/
    *background-color:#a200ff;/*IE6、7识别*/（+和#也可）
    _background-color:#1e0bd1;/*IE6识别*/
}
```

##### 选择符级HACK：<br/>
在选择符前面加*，+构成选择符级HACK。<br/>

12、li与li之间有看不见的空白间隔是什么原因引起的？有什么解决办法？
-------
行框的排列会受到中间空白（回车\空格）等的影响，因为空格也属于字符,这些空白也会被应用样式，占据空间，所以会有间隔，把字符大小设为0，就没有空格了。<br/>
解决方法：<br/>
（1）浮动li中float：left；<br/>
（2）在ul中用font-size：0（谷歌不支持）；可以使用letter-space：-3px；<br/>

13、CSS里的visibility属性有个collapse属性值是干嘛用的？在不同浏览器下以后什么区别？
--------
当一个元素的visibility属性被设置成collapse值后，对于一般的元素，它的表现跟hidden是一样的。<br/>
（1）谷歌浏览器中，使用collapse值和使用hidden没有区别。<br/>
（2）火狐，opera和IE，使用collapse值和使用display：none没有什么区别。<br/>

14、position跟display、margin collapse、overflow、float这些特性相互叠加后会怎么样？
----------
display属性规定元素应该生成的框的类型；<br/>
position属性规定元素的定位类型；<br/>
float属性是一种布局方式，定义元素在哪个方向浮动。<br/>
类似于优先级机制：position：absolute/fixed优先级最高，有他们在时，float浮动不起作用，display值需要调整。<br/>
注：浮动或者absolute定位的元素，只能是块元素或表格。<br/>

15、对BFC规范(块级格式化上下文：block formatting context)的理解？
---------
BFC，块级格式化上下文，一个创建了新的BFC的盒子是独立布局的，盒子里面的子元素的样式不会影响到外面的元素。在同一个BFC中的两个毗邻的块级盒在垂直方向（和布局方向有关系）的margin会发生折叠。<br/>
（W3C CSS 2.1 规范中的一个概念,它是一个独立容器，决定了元素如何对其内容进行定位,以及与其他元素的关系和相互作用。）<br/>
一个页面是由很多个 Box 组成的,元素的类型和 display 属性,决定了这个 Box 的类型。<br/>
不同类型的 Box,会参与不同的 Formatting Context（决定如何渲染文档的容器）,因此Box内的元素会以不同的方式渲染,也就是说BFC内部的元素和外部的元素不会互相影响。<br/>
定位方案：<br/>
（1）普通流（normal flow）按照html中的先后位置至上而下布局，行内元素水平排列 ，当前行被占满后换行，块级元素会被渲染为完整的新行。<br/>
（2）浮动（floats）。<br/>
元素首先按照普通流位置出现，然后根据浮动的方向尽可能的向左向右偏移，与印刷的文本环绕相似。<br/>
（3）绝对定位（absolute position）。<br/>
绝对定位中，元素会整体脱离普通流BFC正是属于普通流，因此对兄弟元素也不会造成什么影响。具有 BFC 的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并 且 BFC 具有普通容器没有的一些特性。浮动元素，绝对定位元素，display，overflow会触发BFC。<br/>
特性：<br/>
（1）会阻止外边距折叠。<br/>
（2）会包含浮动元素。<br/>
（3）阻止元素被浮动元素覆盖。<br/>

16、css定义的权重
---------
以下是权重的规则：标签的权重为1，class的权重为10，id的权重为100，以下例子是演示各种定义的权重值：<br/>
```css
/*权重为10+1=11*/
.class1 div{
}
/*权重为10+10+1=21*/
.class1 .class2 div{
}
```
如果权重相同，则最后定义的样式会起作用，但是应该避免这种情况出现<br/>

17、设置元素浮动后，该元素的display值是多少？请解释一下为什么会出现浮动和什么时候需要清除浮动？清除浮动的方式
----------
设置元素浮动后，display：block。<br/>
IE出现双边框的原因：浮动元素的浮动方向与margin的方向一致会出现双边框。<br/>
解决bug：（1）给浮动元素添加一个display：inline（2）给IE6写一个hack，其值为正常值的一半。<br/>
浮动元素脱离文档流，不占据空间。浮动元素碰到包含它的边框或者浮动元素的边框停留。<br/>

为什么会出现浮动:<br/>
出现浮动之后，我们可以很好的进行页面布局。<br/>

浮动元素引起的问题：<br/>
（1）在非IE浏览器（如Firefox）下，当容器的高度为auto，且容器的内容中有浮动（float为left或 right）的元素，在这种情况下，容器的高度不能自动伸长撑开以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动。<br/>
（2）与浮动元素同级的非浮动元素会跟随其后。<br/>
（3）若非第一个元素浮动，则该元素之前的元素也需要浮动，否则会影响页面显示的结构。<br/>

清除浮动的方式：<br/>
1）使用空标签清除浮动。<br/>
   这种方法是在所有浮动标签后面添加一个空标签<br/>
   ```html
   <div style="clear:both;"></div>
   ```
   （缺点：不过这个办法会增加无意义标签使HTML结构看起来不够简洁。）<br/>
2）使用overflow。<br/>
   给包含浮动元素的父标签添加css属性 overflow:auto; zoom:1; zoom:1用于兼容IE6。|| overflow:hidden。<br/>
3）使用after伪对象清除浮动。<br/>
   该方法只适用于非IE浏览器。具体写法可参照以下示例。使用中需注意以下几点。该方法中必须为需要清除浮动元素的伪对象中设置 height:0，否则该元素会比实际高出若干像素；<br/>
   清除浮动是为了清除使用浮动元素产生的影响。浮动的元素，高度会塌陷，而高度的塌陷使我们页面后面的布局不能正常显示。<br/>
4）、父级div定义height；<br/>
5）、父级div 也一起浮动；<br/>
6）、父级div 定义display:table；<br/>
7）、常规的使用一个class；<br/>
```css
.clearfix:before, .clearfix:after {
   content: ".";
   display: table;
}
.clearfix:after {
   clear: both;
}
.clearfix {
   *zoom: 1;
}
```
8）、SASS编译的时候，在浮动元素的父级div定义伪类:after
```css
&:after,&:before{
   content: ".";
   visibility: hidden;
   display: block;
   height: 0;
   clear: both;
}
```
解析原理：<br/>

1)display:block 使生成的元素以块级元素显示,占满剩余空间。<br/>
2)height:0 避免生成内容破坏原有布局的高度。<br/>
3)visibility:hidden 使生成的内容不可见，并允许可能被生成内容盖住的内容可以进行点击和交互。<br/>
4）通过 content:"."生成内容作为最后一个元素，至于content里面是点还是其他都是可以的，例如oocss里面就有经典的 content:".",有些版本可能content 里面内容为空,是不推荐这样做的,firefox直到7.0 content:”" 仍然会产生额外的空隙。<br/>
5）zoom：1 触发IE hasLayout。<br/>
通过分析发现，除了clear：both用来闭合浮动的，其他代码无非都是为了隐藏掉content生成的内容，这也就是其他版本的闭合浮动为什么会有font-size：0，line-height：0。<br/>

zoom:1的清楚浮动原理?<br/>
清除浮动，触发hasLayout。Zoom属性是IE浏览器的专有属性，它可以设置或检索对象的缩放比例。解决ie下比较奇葩的bug。譬如外边距（margin）的重叠，浮动清除，触发ie的haslayout属性等。<br/>
来龙去脉大概如下：<br/>
当设置了zoom的值之后，所设置的元素就会就会扩大或者缩小，高度宽度就会重新计算了，这里一旦改变zoom值时其实也会发生重新渲染，运用这个原理，也就解决了ie下子元素浮动时候父元素不随着自动扩大的问题。<br/>
Zoom属是IE浏览器的专有属性，火狐和老版本的webkit核心的浏览器都不支持这个属性。然而，zoom现在已经被逐步标准化，出现在 CSS 3.0 规范草案中。<br/>
目前非ie由于不支持这个属性，它们又是通过什么属性来实现元素的缩放呢？<br/>
可以通过css3里面的动画属性scale进行缩放。<br/>

18、使用 CSS 预处理器吗？喜欢那个？
-------
预处理器：less，sass等等（给css像其他程序语言一样，加入一些编程元素，让css能像其他程序语言一样做一些预定的处理，然后就有了css预处理器）喜欢Sass，less，stylus。Sass 是采用 Ruby 语言编写的一款 CSS 预处理语言<br/>
```css
$color:red
.test{
   color：$color;
}
/*执行结果：*/
.test{
   color：red;
}
```
(1)Sass 语法
```css
$font-stack: Helvetica, sans-serif //定义变量
$primary-color: #333 //定义变量
body
font: 100% $font-stack
color: $primary-color
```
(2)SCSS 语法
```css
$font-stack: Helvetica, sans-serif;
$primary-color: #333;
body {
   font: 100% $font-stack;
   color: $primary-color;
}
/*编译出来的 CSS*/
body {
   font: 100% Helvetica, sans-serif;
   color: #333;
}
```

19、CSS优化、提高性能的方法有哪些？
---------
关键选择器（key selector）。选择器的最后面的部分为关键选择器（即用来匹配目标元素的部分）；<br/>
如果规定拥有 ID 选择器作为其关键选择器，则不要为规则增加标签。过滤掉无关的规则（这样样式系统就不会浪费时间去匹配它们了）；<br/>
提取项目的通用公有样式，增强可复用性，按模块编写组件；增强项目的协同开发性、可维护性和可扩展性;<br/>
使用预处理工具或构建工具（gulp对css进行语法检查、自动补前缀、打包压缩、自动优雅降级）；<br/>
（1）避免过度约束<br/>
```css
/*糟糕*/
ul#nav{..}
/*好的*/
#nav{..}
```
（2）后代选择符不好
```css
html div tr td {..}
```
（3）避免链式选择符
```css
/*糟糕*/
.menu.left.icon {..}
/*好的*/
.menu-left-icon {..}
```
（4）使用复合（紧凑）语法
```css
/*糟糕*/
.someclass {
    padding-top: 20px;
    padding-bottom: 20px;
    padding-left: 10px;
    padding-right: 10px;
    background: #000;
    background-image: url(../imgs/carrot.png);
    background-position: bottom;
    background-repeat: repeat-x;
}
/*好的*/
.someclass {
    padding: 20px 10px 20px 10px;
    background: #000 url(../imgs/carrot.png) repeat-x bottom;
}
```
（5）避免不必要的命名空间
```css
/*糟糕*/
.someclass table tr.otherclass td.somerule {..}
/*好的*/
.someclass .otherclass td.somerule {..}
```
（6）避免不必要的重复
```css
.someclass {
    color: red;
    background: blue;
    font-size: 15px;
}
.otherclass {
    color: red;
    background: blue;
    font-size: 15px;
}
/*好的*/
.someclass, .otherclass {
    color: red;
    background: blue;
    font-size: 15px;
}
```
（7）最好使用表示语义的名字。一个好的类名应该是描述他是什么而不是像什么。<br/>
（8）避免！important，可以选择其他选择器。<br/>
（9）尽可能的精简规则，你可以合并不同类里的重复规则。<br/>

19、浏览器是怎样解析CSS选择器的？
-------
样式系统从关键选择器开始匹配，然后左移查找规则选择器的祖先元素。<br/>
只要选择器的子树一直在工作，样式系统就会持续左移，直到和规则匹配，或者是因为不匹配而放弃该规则。<br/>
而在 CSS 解析完毕后，需要将解析的结果与 DOM Tree 的内容一起进行分析建立一棵 Render Tree，最终用来进行绘图。在建立 Render Tree 时（WebKit 中的「Attachment」过程），浏览器就要为每个 DOM Tree 中的元素根据 CSS 的解析结果（Style Rules）来确定生成怎样的 renderer。<br/>

20、在网页中的应该使用奇数还是偶数的字体？为什么呢？
---------
使用的是偶数字体。偶数字号相对更容易和 web 设计的其他部分构成比例关系。Windows 自带的点阵宋体（中易宋体）从 Vista 开始只提供 12、14、16 px 这三个大小的点阵，而 13、15、17 px时用的是小一号的点阵（即每个字占的空间大了 1 px，但点阵没变），于是略显稀疏。<br/>

21、margin和padding分别适合什么场景使用？
------
margin是用来隔开元素与元素的间距；padding是用来隔开元素与内容的间隔。<br/>
何时使用margin：<br/>
（1）需要在border外侧添加空白。<br/>
（2）空白处不需要背景色。<br/>
（3）上下相连的两个盒子之间的空白，需要相互抵消时。<br/>
何时使用padding：<br/>
（1）需要在border内侧添加空白。<br/>
（2）空白处需要背景颜色。<br/>
（3）上下相连的两个盒子的空白，希望为两者之和。<br/>
兼容性的问题：在IE5 IE6中，为float的盒子指定margin时，左侧的margin可能会变成两倍的宽度。通过改变padding或者指定盒子的display：inline解决。<br/>

22、元素竖向的百分比设定是相对于容器的高度吗？
-----
PS：当按百分比设定一个元素的宽度时，它是相对于父容器的宽度计算的。<br/>
竖向距离的属性，如padding-top,padding-bottom,margin-top,margin-bottom等，当按百分比设定它们时，依据的也是父容器的宽度，而不是高度。<br/>

23、全屏滚动的原理是什么？用到了CSS的那些属性？<br/>
-----
（1）原理：方法一是整体的元素一直排列下去，假设有5个需要展示的全屏页面，那么高度是500%，只是展示100%，剩下的可以通过transform进行y轴定位，也可以通过margin-top实现。<br/>
（2）overflow：hidden；transition：all 1000ms ease；<br/>

24、什么是响应式设计？响应式设计的基本原理是什么？如何兼容低版本的IE？
------
（1）响应式网站设计(Responsive Web design)的理念是：集中创建页面的图片排版大小，可以智能地根据用户行为以及使用的设备环境（系统平台、屏幕尺寸、屏幕定向等）进行相对应的布局。<br/>
（2）基本原理: 媒体查询 @media。<br/>
（3）兼容IE可以使用JS辅助一下来解决。<br/>

25、视差滚动效果，如何给每页做不同的动画？（回到顶部，向下滑动要再次出现，和只出现一次分别怎么做？）
-------
视差滚动（Parallax Scrolling）就是这样的效果之一。这种技术通过在网页向下滚动的时候，控制背景的移动速度比前景的移动速度慢来创建出令人惊叹的3D效果。<br/>
原理：<br/>
（1）CSS3实现<br/>
优点：开发时间短、性能和开发效率比较好，缺点是不能兼容到低版本的浏览器。<br/>
（2）jquery实现<br/>
通过控制不同层滚动速度，计算每一层的时间，控制滚动效果。<br/>
优点：能兼容到各个版本的，效果可控性好。<br/>
缺点：开发起来对制作者要求高。<br/>
（3）插件实现方式<br/>
例如：parallax-scrolling，兼容性十分好。<br/>

26、::before 和 :after中双冒号和单冒号 有什么区别？解释一下这2个伪元素的作用。
-------
单冒号(:)用于CSS3伪类，双冒号(::)用于CSS3伪元素。（伪元素由双冒号和伪元素名称组成）双冒号是在当前规范中引入的，用于区分伪类和伪元素。不过浏览器需要同时支持旧的已经存在的伪元素写法。比如:first-line、:first-letter、:before、:after等。<br/>
在css2之前用的是单冒号，之后css3使用时双冒号。目前除了IE外不兼容双冒号，其他的浏览器兼容双冒号，建议还是使用单冒号。<br/>
::before就是以一个子元素的存在，定义在元素主体内容之前的一个伪元素。并不存在与dom之中，只存在在页面之中。同理，after是在主体内容之后显示的。想让插入的内容出现在其它内容前，使用::before，之后使用::after；在代码顺序上，::after生成的内容也比::before生成的内容靠后。如果按堆栈视角，::after生成的内容会在::before生成的内容之上<br/>

27、如何修改chrome记住密码后自动填充表单的黄色背景 ？
------
```css
input:-webkit-autofill, textarea:-webkit-autofill, select:-webkit-autofill {
    background-color: rgb(250, 255, 189); /* #FAFFBD; */
    background-image: none;
    color: rgb(0, 0, 0);
}
```
这黄色背景是chrome会默认给自动填充的input表单加上input：-webkit-autofill私有属性。<br/>
```css
input：-webkit-autofill{
    background-color : #FAFFBD ;
    background-image : none ;
    color : #000 
}
```
第一种情况：input文本框是纯色背景的<br/>
可以对input：-webkit-autofill使用足够大的纯色内阴影来覆盖input输入框的黄色背景。<br/>
```css
input:-webkit-autofill{
    -webkit-box-shadow:0 0 0px 1000px white inset;
    border：1px solid #ccc ！important;
}
```
除了chrome默认定义的background-color，background-image，color不能用!important提升其优先级 ，所以只能用覆盖了。其他的属性均可使用!important提升其优先级。<br/>
第二种情况：input文本框使用背景图片<br/>
1)、图片不复杂可以使用第一种情况解决，纯色内阴影覆盖。<br/>
2)、使用js实现;存在一个问题是使用js方法会导致提交表单的时候无法将value值传过去。<br/>
3)、使用form标签上的关闭自动填充功能：autocomplete="off"。<br/>

28、让页面里的字体变清晰，变细用CSS怎么做？（-webkit-font-smoothing: antialiased;）
-----
-webkit-font-smoothing在window系统下没有起作用，但是在IOS设备上起作用<br/>
-webkit-font-smoothing：antialiased是最佳的，灰度平滑。<br/>

29、你对line-height是如何理解的？
-----
行高是指一行文字的高度，具体说是两行文字间基线的距离。css中起高度作用的因该是height和line-height，一个没有定义height属性，最终其表现作用一定是 line-height。<br/>
单行文本垂直居中：把line-height值设置为height一样大小的值可以实现单行文字的垂直居中，其实也可以把height删除。<br/>
多行文本垂直居中：需要设置display属性为inline-block。<br/>

30、怎么让Chrome支持小于12px 的文字？
------
用图片：如果是内容固定不变情况下，使用将小于12px文字内容切出做图片，这样不影响兼容也不影响美观。<br/>
方法一：首先取消浏览器自动调整功能。<br/>
```css
.classstyle{ 
    -webkit-text-size-adjust:none; font-size:9px; 
} 
```
方法二：现在可以使用css3里的一个属性：transform：scale()<br/>
```css
p{
    font-size:10px;
    -webkit-transform:scale(0.8);
}//0.8是缩放比例
```

31、font-style属性可以让它赋值为“oblique” oblique是什么意思？
------
在css规范中这么描述的，让一种字体表示为斜体（oblique），如果没有这样样式，就可以使用italic。oblique是一种倾斜的文字，不是斜体。<br/>

32、position:fixed;在android下无效怎么处理？
-----
fixed的元素是相对整个页面固定位置的，你在屏幕上滑动只是在移动这个所谓的viewport，原来的网页还好好的在那，fixed的内容也没有变过位置，所以说并不是iOS不支持fixed，只是fixed的元素不是相对手机屏幕固定的。<br/>
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no"/>
```

33、如果需要手动写动画，你认为最小时间间隔是多久，为什么？（阿里）
-----
多数显示器默认频率是60Hz，即1秒刷新60次，所以理论上最小间隔为1/60＊1000ms ＝ 16.7ms。 <br/>

34、overflow: scroll时不能平滑滚动的问题怎么处理？
-----
（1）高度尺寸不确定的时候，使用：overflow-y：scroll;<br/>
（2）高度尺寸确定的，要么没有滚动条，要么直接出现，不会出现跳动。<br/>
（3）css3计算calc和vw单位巧妙实现滚动条出现页面不跳动。<br/>
```css
.wrap-outer {
margin-left: calc(100vw - 100%);
}
```
或<br/>
```css
.wrap-outer {
padding-left: calc(100vw - 100%);
}
```
首先，.wrap-outer指的是居中定宽主体的父级，如果没有，创建一个，然后，calc是css3的计算。<br/>
100vw是浏览器的内部宽度，而100%是可用宽度，不含滚动条。<br/>
calc（100vw-100%）是浏览器的滚动条的宽度。<br/>

35、有一个高度自适应的div，里面有两个div，一个高度100px，希望另一个填满剩下的高度。
-----
```html
<div class="box">
    <div class="el1"></div>
    <div class="el2"></div>
</div>
```
```css
.box {width:200px; height: 100%;background:red;position:relative;}
.el1 {height:100px;background:green;}
.el2 {background:blue;width:100%;position:absolute;top:100px;bottom:0;}
```
外层position: relative；<br/>
百分百自适应元素直接position: absolute; top: 100px; width:100%;bottom: 0; left: 0<br/>

36、png、jpg、gif 这些图片格式解释一下，分别什么时候用。有没有了解过webp？
-----
（1）png是便携式网络图片（Portable Network Graphics）是一种无损数据压缩位图文件格式。<br/>
 优点是：压缩比高，色彩好。 大多数地方都可以用。<br/>
（2）jpg是一种针对相片使用的一种失真压缩方法，是一种破坏性的压缩，在色调及颜色平滑变化做的。在www上，被用来储存和传输照片的格式。<br/>
（3）gif是一种位图文件格式，以8位色重现真色彩的图像。可以实现动画效果时候。<br/>
（4）webp格式<br/>
是谷歌在2010年推出的图片格式，压缩率只有jpg的2/3，大小比png小了45%，缺点是压缩的时间更久了。兼容性不好，目前谷歌和opera支持。<br/>

37、什么是Cookie 隔离？（或者说：请求资源的时候不要让它带cookie怎么做）
-------
如果静态文件都放在主域名下，那静态文件请求的时候都带有的cookie的数据提交给server的，非常浪费流量,所以不如隔离开。<br/>
因为cookie有域的限制，因此不能跨域提交请求，故使用非主要域名的时候，请求头中就不会带有cookie数据，这样可以降低请求头的大小，降低请求时间，从而达到降低整体请求延时的目的。同时这种方式不会将cookie传入Web Server，也减少了Web Server对cookie的处理分析环节，提高了webserver的http请求的解析速度。<br/>
Cookie隔离问题，同一个网页,多个RemoteWebDriver会共享同一个Cookie。比如想要并行登陆并执行操作，这样是不行的。<br/>

38、style标签写在body后与body前有什么区别？
-----
页面加载自上而下 当然是先加载样式。<br/>

39、什么是CSS 预处理器 / 后处理器？
-----
预处理器例如：LESS、Sass、Stylus，用来预编译Sass或less，增强了css代码的复用性，还有层级、mixin、变量、循环、函数等，具有很方便的UI组件模块化开发能力，极大的提高工作效率。<br/>
后处理器例如：PostCSS，通常被视为在完成的样式表中根据CSS规范处理CSS，让其更有效；目前最常做的是给CSS属性添加浏览器私有前缀，实现跨浏览器兼容性的问题。

40、像素渲染流水线
-----
1）下载HTML文档<br/>
2）解析HTML文档，生成DOM<br/>
3）下载文档中引用的CSS、JS<br/>
4）解析CSS样式表，生成CSSOM<br/>
5）将JS代码交给JS引擎执行<br/>
6）合并DOM和CSSOM，生成Render Tree<br/>
7）根据Render Tree进行布局layout（为每个元素计算尺寸和位置信息）<br/>
8）绘制（Paint）每个层中的元素（绘制每个瓦片，瓦片这个词与GIS中的瓦片含义相同）<br/>
9）执行图层合并（Composite Layers）<br/>

41、重构、回流
-----
浏览器的重构指的是改变每个元素外观时所触发的浏览器行为，比如颜色，背景等样式发生了改变而进行的重新构造新外观的过程。重构不会引发页面的重新布局，不一定伴随着回流。<br/> 
回流指的是浏览器为了重新渲染页面的需要而进行的重新计算元素的几何大小和位置的，他的开销是非常大的，回流可以理解为渲染树需要重新进行计算，一般最好触发元素的重构，避免元素的回流；比如通过通过添加类来添加css样式，而不是直接在DOM上设置，当需要操作某一块元素时候，最好使其脱离文档流，这样就不会引起回流了，比如设置position：absolute或者fixed，或者display：none，等操作结束后再显示。<br/>

42、为什么要初始化 CSS 样式
-----
因为浏览器的兼容问题，不同浏览器对有些标签的默认值是不同的，如果没对 CSS 初始化往往会出现浏览器之间的页面显示差异。<br/>
当然，初始化样式会对 SEO 有一定的影响，但鱼和熊掌不可兼得，但力求影响最小的情况下初始化。<br/>
最简单的初始化方法是：*{padding:0;margin:0} (不建议)<br/>
淘宝的样式初始化：<br/>
```css
body, h1, h2, h3, h4, h5, h6, hr, p, blockquote, dl, dt, dd, ul, ol, li, pre, form, fieldset, legend, button, input, textarea, th, td { margin:0; padding:0; } 
body, button, input, select, textarea { font:12px/1.5tahoma, arial, \5b8b\4f53; } 
h1, h2, h3, h4, h5, h6{ font-size:100%; } 
 address, cite, dfn, em, var { font-style:normal; } 
code, kbd, pre, samp { font-family:couriernew, courier, monospace; } 
small{ font-size:12px; } 
ul, ol { list-style:none; } 
a { text-decoration:none; } 
a:hover { text-decoration:underline; } 
sup { vertical-align:text-top; } 
ub{ vertical-align:text-bottom; } 
legend { color:#000; } 
fieldset, img { border:0; } 
 button, input, select, textarea { font-size:100%; } table { border-collapse:collapse; border-spacing:0; } 
 ```

43、简述一下src与href的区别
-----
href 是指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，用于超链接。<br/>
src是指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置；在请求src资源时会将其指向的资源下载并应用到文档内，例如js脚本，img图片和frame等元素。当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将js脚本放在底部而不是头部。<br/>


44、px和em的区别
-----
px和em都是长度单位，区别是，px的值是固定的，指定是多少就是多少，计算比较容易。em得值不是固定的，并且em会继承父级元素的字体大小。<br/>
浏览器的默认字体高都是16px。所以未经调整的浏览器都符合: 1em=16px。那么12px=0.75em, 10px=0.625em<br/>

44、css属性overflow属性定义溢出元素内容区的内容会如何处理
-----
参数是scroll时候，必会出现滚动条。<br/>
参数是auto时候，子元素内容大于父元素时出现滚动条。<br/>
参数是visible时候，溢出的内容出现在父元素之外。<br/>
参数是hidden时候，溢出隐藏。<br/>

45、flash和js通过什么类如何交互?
-----
Flash提供了ExternalInterface接口与JavaScript通信，ExternalInterface有两个方法，call和addCallback，call的作用是让Flash调用js里的方法，addCallback是用来注册flash函数让js调用。<br/>

46、元素的alt和title有什么异同？
-----
这两个属性是有些重复了。在不同浏览器里面表现有些不同。在alt和title同时设置的时候，alt作为 图片的替代文字出现，title是图片的解释文字。<br/>

47、有效的javascript变量定义规则
-----
第一个字符必须是一个字母、下划线或一个美元符号（$）；其他字符可以是字母、下划线、美元符号或数字。<br/>
javascript系统方法<br/>
parseFloat方法：该方法将一个字符串转换成对应的小数<br/>
escape方法： 该方法返回对一个字符串编码后的结果字符串<br/>
eval方法：该方法将某个参数字符串作为一个JavaScript执行<br/>
NaN,即非数值（Not a Number）是一个特殊的数值，这个数值用来表示一个本来要返回数值的操作数未 返回数值的情况（这样就不会抛出错误了）。isNaN（）函数，而任何不能被转换为数值的值都会导致这个函数返回true。<br/>

48、JavaScript中 call和apply的描述
-----
call()方法和apply()方法的作用相同，他们的区别在于接收参数的方式不同。在使用call()方 法时，传递给函数的参数必须逐个列举出来。使用apply()时，传递给函数的是参数数组。<br/>
```javascript
function add(c, d){ 
return this.a + this.b + c + d; 
} 
var o = {a:1, b:3}; 
add.call(o, 5, 7); // 1 + 3 + 5 + 7 = 16 
add.apply(o, [10, 20]); // 1 + 3 + 10 + 20 = 34
```

57、阐述一下CSS sprities？
-----
它允许你将一个页面涉及到的所有零星图片都包含到一张大图中去，这样一来，当访问该页面时，载入的图片就不会像以前那样一幅一幅地慢慢显示出来了。<br/>
利用CSS的“background-image”，“background- repeat”，“background-position”的组合进行背景定位,background-position可以用数字精确的定位出背景图片的位置。利用CSS Sprites能很好地减少网页的http请求，从而大大的提高页面的性能，这也是CSS Sprites最大 的优点，也是其被广泛传播和应用的主要原因；<br/>
CSS Sprites能减少图片的字节，曾经比较过多次3张图片合并成1张图片的字节总是小于这3张图片的字节总和。<br/>
解决了网页设计师在图片命名上的困扰，只需对一张集合的图片上命名就可以了，不需要对每一个小元素进行命名，从而提高了网页的制作效率。<br/>
更换风格方便，只需要在一张或少张图片上修改图片的颜色或样式，整个网页的风格就可以改变。维护起来更加方便。<br/>

