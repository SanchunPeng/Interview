1、JavaScript事件循环机制 
------
1)、JavaScript的一大特点就是单线程,这个线程中拥有唯一的一个事件循环。<br/>
2)、JavaScript代码的执行过程中，除了依靠函数调用栈来处理函数的执行顺序外，还依靠任务队列(task queue)来处理另外一些代码的执行。<br/>
3)、一个线程中，事件循环是唯一的，但是任务队列可以拥有多个。<br/>
4)、任务队列又分为macro-task（宏任务）与micro-task（微任务），在最新标准中，它们被分别称为task与jobs。<br/>
5)、macro-task大概包括：script(整体代码), setTimeout, setInterval, setImmediate, I/O, UI rendering。<br/>
6)、micro-task大概包括: process.nextTick, Promise, Object.observe(已废弃), MutationObserver(html5新特性)<br/>
7)、setTimeout/Promise等我们称之为任务源。而进入任务队列的是他们指定的具体执行任务。<br/>
8)、来自不同任务源的任务会进入到不同的任务队列。其中setTimeout与setInterval是同源的。<br/>
9)、事件循环的顺序，决定了JavaScript代码的执行顺序。它从script(整体代码)开始第一次循环。之后全局上下文进入函数调用栈。直到调用栈清空(只剩全局)，然后执行所有的micro-task。当所有可执行的micro-task执行完毕之后。循环再次从macro-task开始，找到其中一个任务队列执行完毕，然后再执行所有的micro-task，这样一直循环下去。<br/>
10)、其中每一个任务的执行，无论是macro-task还是micro-task，都是借助函数调用栈来完成。<br/>
var id = setTimeout（fn，延迟）；–初始化一个计时器，将延迟后调用指定的函数。该函数返回一个唯一的ID，可以在稍后的时间取消定时器。<br/>
var id = setInterval（FN，延迟）；–类似setTimeout但不断调用函数（用每延迟时间）直到它被取消。<br/>
clearInterval（id）；清除定时器（ID）；–接受一个定时器的ID（由上述函数返回）和停止计时器回调发生。<br/>
为了了解定时器如何在内部工作，有一个重要的概念，需要探讨：定时器延迟不能保证。由于浏览器中的所有JavaScript都执行在一个线程上的异步事件（如鼠标点击和定时器）只运行时，有一个开放的执行。<br/>
##### 实例：
```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
}
console.log(new Date, i);
```
约定，用箭头表示其前后的两次输出之间有 1 秒的时间间隔，而逗号表示其前后的两次输出之间的时间间隔可以忽略。<br/>
输出结果为：5 -> 5,5,5,5,5，即第1个5直接输出，1秒之后，输出5个5。<br/>
解释：执行到for循环时，遇到setTimeout，就将任务分发到对应的宏任务队列中。每循环一次就将任务放到任务队列中一次，for循环执行结束，执行到最后一句console.log(new Date, i);整个script代码执行结束，接下来执行任务队列，因为var定义的i的作用域在for上一层，所以此时i都是5。<br/>

###### 但是如果期望代码的输出变成：5 -> 0,1,2,3,4，该怎么改造代码
使用闭包：对循环体稍做改变，让负责输出的那段代码能拿到每次循环的i值即可。<br/>
```javascript
for (var i = 0; i < 5; i++) {
    (function(j) {  // j = i
        setTimeout(function() {
            console.log(new Date, j);
        }, 1000);
    })(i);
}
console.log(new Date, i);
```

利用 JS 中基本类型的参数传递是按值传递的特征，改造代码：<br/>
```javascript
var output = function (i) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
};
for (var i = 0; i < 5; i++) {
    output(i); 			 // 这里传过去的 i 值被复制了
}
console.log(new Date, i);
```
因为造成上述的原因是var不是块级作用域，var定义的i存在于上层作用域中，而不是for循环中，意味着在对于它的引用被全部解除之前，它都不会被垃圾收集器所标记并清除，已支保持着循环结束后的值。let定义的是块状作用域，让在循环体内所定义的变量或者常量能留在循环体内。<br/>
运用ES6的块级作用域，改造代码：<br/>
```javascript
for (let i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
}
console.log(new Date, i); 
```
###### 但是如果期望代码的输出变成：0 -> 1 -> 2 -> 3 -> 4 -> 5，并且要求原有的代码块中的循环和两处 console.log 不变该怎么改造代码
粗暴方案：<br/>
```javascript
for (var i = 0; i < 5; i++) {
    (function(j) {
        setTimeout(function() {
            console.log(new Date, j);
        }, 1000 * j);  // 这里修改 0~4 的定时器时间
    })(i);
}
setTimeout(function() { // 这里增加定时器，超时设置为 5 秒
    console.log(new Date, i);
}, 1000 * i);
```

###### 如果把需求抽象为：在系列异步操作完成（每次循环都产生了1个异步操作）之后，再做其他的事情，想到使用Promise。改造代码：
```javascript
const tasks = []; // 这里存放异步操作的 Promise
const output = (i) => new Promise((resolve) => {
    setTimeout(() => {
        console.log(new Date, i);
        resolve();
    }, 1000 * i);
});
// 生成全部的异步操作
for (var i = 0; i < 5; i++) {
    tasks.push(output(i));
}
// 异步操作完成之后，输出最后的 i
Promise.all(tasks).then(() => {
    setTimeout(() => {
        console.log(new Date, i);
    }, 1000);
});
```
使用 Promise 处理异步代码比回调机制让代码可读性更高，但是使用 Promise 的问题也很明显，即如果没有处理 Promise 的 reject，会导致错误被丢进黑洞，好在新版的 Chrome 和 Node 7.x 能对未处理的异常给出 Unhandled Rejection Warning，而排查这些错误还需要一些特别的技巧（浏览器、Node.js）。<br/>
接下来，使用ES7中的 async await 特性来让这段代码变的更简洁。<br/>
```javascript
// 模拟其他语言中的 sleep，实际上可以是任何异步操作
const sleep = (timeountMS) => new Promise((resolve) => {
    setTimeout(resolve, timeountMS);
});
(async () => {  // 声明即执行的 async 函数表达式
    for (var i = 0; i < 5; i++) {
        await sleep(1000);
        console.log(new Date, i);
    }
    await sleep(1000);
    console.log(new Date, i);
})();
```

2、js,html,css的加载顺序
----------
1）.js放在head中会立即执行，阻塞后续的资源下载与执行。因为js有可能会修改dom，如果不阻塞后续的资源下载，dom的操作顺序不可控。<br/>
2）.js的执行依赖前面的样式。即只有前面的样式全部下载完成后才会执行js，但是此时外链css和外链js是并行下载的。<br/>
3）.外链的js如果含有defer="true"属性，将会并行加载js，到页面全部加载完成后才会执行，会按顺序执行。defer属性的作用是，告诉浏览器，等到DOM加载完成后，再执行指定脚本。<br/>
4）.外链的js如果含有async="true"属性，将不会依赖于任何js和css的执行，此js下载完成后立刻执行，不保证按照书写的顺序执行。因为async="true"属性会告诉浏览器，js不会修改dom和样式，故不必依赖其它的js和css。<br/>　
async属性可以保证脚本下载的同时，浏览器继续渲染。需要注意的是，一旦采用这个属性，就无法保证脚本的执行顺序。哪个脚本先下载结束，就先执行那个脚本。另外，使用async属性的脚本文件中，不应该使用document.write方法。<br/>
一般来说，如果脚本之间没有依赖关系，就使用async属性，如果脚本之间有依赖关系，就使用defer属性。如果同时使用async和defer属性，后者不起作用，浏览器行为由async属性决定。<br/>


3、浏览器兼容问题
---------
浏览器间内核的差异是产生兼容性问题的根本原因。<br/>
##### 1)不同浏览器的标签默认的外补丁和内补丁不同
问题症状：随便写几个标签，不加样式控制的情况下，各个浏览器的margin 和padding差异较大。<br/>
解决方案：CSS里加\*{margin:0;padding:0;}<br/>
备注：几乎所有的项目开始都是有一个main.xml里面包含一些标签的初始设置a,div,ul,li,h1,p,....{margin:0;padding:0}。<br/>
##### 2)图片默认有间距
问题症状：几个img标签放在一起的时候，有些浏览器会有默认的间距，加了问题一中提到的通配符也不起作用。<br/>
解决方案：使用float属性为img布局。<br/>
备注：因为img标签是行内属性标签，所以只要不超出容器宽度，img标签都会排在一行里，但是部分浏览器的img标签之间会有个间距。使用float是正道。<br/>
##### 3)标签最低高度设置min-height不兼容
问题症状：因为min-height本身就是一个不兼容的CSS属性，所以设置min-height时不能很好的被各个浏览器兼容<br/>
解决方案：如果我们要设置一个标签的最小高度200px，需要进行的设置为：{min-height:200px; height:auto !important; height:200px; overflow:visible;}
IE6中的间距，双倍边距等bug。<br/>
备注：在B/S系统前端开时，有很多情况下我们又这种需求。当内容小于一个值（如300px）时。容器的高度为300px；当内容高度大于这个值时，容器高度被撑高，而不是出现滚动条。这时候我们就会面临这个兼容性问题。<br/>
##### 4）块属性标签float后，又有横行的margin情况下，在IE6显示margin比设置的大（即双倍边距bug）
问题症状：常见症状是IE6中后面的一块被顶到下一行。<br/>
解决方案：在float的标签样式控制中加入\_display:inline;将其转化为行内属性。<br/>
##### 5）设置较小高度标签（一般小于10px），在IE6，IE7，遨游中高度超出自己设置高度
问题症状：IE6、7和遨游里这个标签的高度不受控制，超出自己设置的高度。<br/>
解决方案：给超出高度的标签设置overflow:hidden;或者设置行高line-height小于你设置的高度。<br/>
备注：这种情况一般出现在我们设置小圆角背景的标签里。出现这个问题的原因是IE8之前的浏览器都会给标签一个最小默认的行高的高度。即使你的标签是空的，这个标签的高度还是会达到默认的行高。<br/>
##### 6）行内属性标签，设置display:block后采用float布局，又有横行的margin的情况，IE6间距bug
问题症状：IE6里的间距比超过设置的间距。<br/>
解决方案：在display:block;后面加入display:inline;display:table;
备注：行内属性标签，为了设置宽高，我们需要设置display:block;(除了input标签比较特殊)。在用float布局并有横向的margin后，在IE6下，他就具有了块属性float后的横向margin的bug。不过因为它本身就是行内属性标签，所以我们再加上display:inline的话，它的高宽就不可设了。这时候我们还需要在display:inline后面加入display:table。<br/>
##### 7）附：IE6中的常见BUG与相应的解决办法
###### 一、IE6双倍边距bug
例如“margin-left:10px” 在IE6中，该值就会被解析为20px。
解决方案：需要在该元素中加入display:inline 或 display:block 明确其元素类型
###### 二、IE6中3像素问题及解决办法
当元素使用float浮动后，元素与相邻的元素之间会产生3px的间隙。诡异的是如果右侧的容器没设置高度时3px的间隙在相邻容器的内部，当设定高度后又跑到容器的相反侧了。
解决方案：需要使布局在同一行的元素都加上float浮动。
###### 三、IE6中奇数宽高的BUG
IE6中奇数的高宽显示大小与偶数高宽显示大小存在一定的不同。其中要问题是出在奇数高宽上。
解决方案：需要尽量将外部定位的div高宽写成偶数即可。
###### 四、IE6中图片链接的下方有间隙
IE6中图片的下方会存在一定的间隙，尤其在图片垂直挨着图片的时候，即可看到这样的间隙。
解决方案：需要将img标签定义为display:block 或定义vertical-align对应的属性。也可以为img对应的样式写入font-size:0
###### 五、IE6下空元素的高度BUG
如果一个元素中没有任何内容，当在样式中为这个元素设置了0-19px之间的高度时。此元素的高度始终为19px。
解决的方法如下:<br/>
1）.在元素的css中加入：overflow:hidden。<br/>
2）.在元素中插入html注释：\<!– \><br/>
3）.在元素中插入html的空白符：&nbsp;<br/>
4）.在元素的css中加入：font-size:0。<br/>
###### 六、重复文字的BUG
在某些比较复杂的排版中，有时候浮动元素的最后一些字符会出现在clear清除元素的下面。<br/>
解决方法如下：<br/>
1）.确保元素都带有display:inline。<br/>
2）.在最后一个元素上使用“margin-right:-3px。<br/>
3）.为浮动元素的最后一个条目加上条件注释，\<!–[if !IE]\>xxx\<![endif]–\><br/>
4）.在容器的最后元素使用一个空白的div，为这个div指定不超过容器的宽度。<br/>
###### 七、IE6中 z-index失效
具体BUG为，元素的父级元素设置的z-index为1，那么其子级元素再设置z-index时会失效，其层级会继承父级元素的设置，造成某些层级调整上的BUG。<br/>
原因：z-index起作用有个小小前提，就是元素的position属性要是relative，absolute或是fixed。<br/>
解决方案：<br/>
1）.position:relative改为position:absolute；<br/>
2）.去除浮动；<br/>
3）.浮动元素添加position属性（如relative，absolute等）。<br/>
###### 八、 png24位的图片在iE6浏览器上出现背景，解决方案是做成PNG8.也可以引用一段脚本处理.
###### 九、渐进识别的方式，从总体中逐渐排除局部。 
  首先，巧妙的使用“\9”这一标记，将IE游览器从所有情况中分离出来。 <br/>
  接着，再次使用“+”将IE8和IE7、IE6分离开来，这样IE8已经独立识别。<br/>
 ```css
 css
      .bb{
       background-color:#f1ee18;/*所有识别*/
      .background-color:#00deff\9; /*IE6、7、8识别*/
      +background-color:#a200ff;/*IE6、7识别*/
      _background-color:#1e0bd1;/*IE6识别*/ 
      } 
 ```
###### 十、IE下,可以使用获取常规属性的方法来获取自定义属性,
   也可以使用getAttribute()获取自定义属性。<br/>
   Firefox下,只能使用getAttribute()获取自定义属性。<br/>
   解决方法:统一通过getAttribute()获取自定义属性。<br/>
##### 8）IE下,event对象有x,y属性,但是没有pageX,pageY属性; 
  Firefox下,event对象有pageX,pageY属性,但是没有x,y属性。<br/>
解决方法：（条件注释）缺点是在IE浏览器下可能会增加额外的HTTP请求数。<br/>
##### 9）Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示, 
  可通过加入 CSS 属性 -webkit-text-size-adjust: none; 解决。<br/>
##### 10）超链接访问过后hover样式就不出现了 被点击访问过的超链接样式不在具有hover和active了解决方法是改变CSS属性的排列顺序:
L-V-H-A :  a:link {} a:visited {} a:hover {} a:active {}<br/>
##### 11）ie和ff都存在，相邻的两个div的margin-left和margin-right不会重合，但是margin-top和margin-bottom却会发生重合。
解决方法，养成良好的代码编写习惯，同时采用margin-top或者同时采用margin-bottom。<br/>
#### IE6结语：
实际上中，很多BUG的解决方法都可以使用display:inline、font-size:0、float解决。因此我们在书写代码时要记住，一旦使用了float浮动，就为元素增加一个display:inline样式，可以有效的避免浮动造成的样式错乱问题。使用空DIV时，为了避免其高度影响布局美观，也可以为其加上font-size:0 这样就很容易避免一些兼容上的问题。<br/>

4、前端性能优化
---------
（1） 减少http请求次数：CSS Sprites, JS、CSS源码压缩、图片大小控制合适；网页Gzip，CDN托管，data缓存 ，图片服务器。<br/>
CSS Sprites其实就是把网页中一些背景图片整合到一张图片文件中，再利用CSS的“background-image”，“background- repeat”，“background-position”的组合进行背景定位，background-position可以用数字能精确的定位出背景图片的位置。这样可以减少很多图片请求的开销，因为请求耗时比较长；请求虽然可以并发，但是也有限制，一般浏览器都是6个。对于未来而言，就不需要这样做了，因为有了http2。<br/>
（2）、使用CDN：网站上静态资源即css、js全都使用cdn分发，图片亦然。<br/>
（3）、避免空的src和href：当link标签的href属性为空、script标签的src属性为空的时候，浏览器渲染的时候会把当前页面的URL作为它们的属性值，从而把页面的内容加载进来作为它们的值。所以要避免犯这样的疏忽。<br/>
（4）、使用GET来完成AJAX请求：当使用XMLHttpRequest时，浏览器中的POST方法是一个“两步走”的过程：首先发送文件头，然后才发送数据。因此使用GET获取数据时更加有意义。<br/>
（5） 前端模板JS+数据，减少由于HTML标签导致的带宽浪费，前端用变量保存AJAX请求结果，每次操作本地变量，不用请求，减少请求次数。<br/>
（6） 用innerHTML代替DOM操作，减少DOM操作次数，优化javascript性能。<br/>
（4） 当需要设置的样式很多时设置className而不是直接操作style。<br/>
（5） 少用全局变量、缓存DOM节点查找的结果。减少IO读取操作。<br/>
（6） 避免使用CSS Expression（css表达式)又称Dynamic properties(动态属性)。<br/>
（7） 图片预加载，将样式表放在顶部，将脚本放在底部，加上时间戳。<br/>
（8） 避免在页面的主体布局中使用table，table要等其中的内容完全下载之后才会显示出来，显示比div+css布局慢。<br/>
（10）、避免404：比如外链的css或者js文件出现问题返回404时，会破坏浏览器对文件的并行加载。并且浏览器会把试图在返回的404响应内容中找到可能有用的部分当作JavaScript代码来执行。<br/>
（11）、现代移动端web中由于webview缓存失效，用户刷新304，以及频繁业务迭代造成的代码更新造成了移动端的缓存是个很值得优化的点。对此美团,百度,滴滴等互联网公司采用了LocalStorage， 结合工程化手段替代webview本身文件缓存机制的优化。大概原理就是所有文件缓存利用js写入LocalStorage进行控制，这样即使用户刷新也不会有304请求。多页面间有模块复用的话，利用js控制可以将缓存细化到AMD模块（异步模块加载机制）级别，达到模块级别的共享复用，最极限的节省带宽和提高加载速度。<br/>
##### 使用LS注意点：
1）、非首屏渲染需要的css文件，可以做LS缓存。首屏渲染需要的css，需要按常规方式输出，因为SEO（搜索引擎优化）需要，不然爬虫爬取页面的时候，页面效果会很不好。而非首屏的css，则可以用LS缓存，减少资源下载时间。<br/>
2）、展示类、动画类等非业务主要逻辑的代码，可以做LS缓存。这样，可以一定程度上避免业务层的安全漏洞。当然，前端再怎么做防护都是一层薄纸。重要的，还是后台接口要做好安全保护。<br/>
3）、移动端可以做LS缓存。PC端做LS缓存，起到的优化作用不大。<br/>

  对普通的网站有一个统一的思路，就是尽量向前端优化、减少数据库操作、减少磁盘IO。向前端优化指的是，在不影响功能和体验的情况下，能在浏览器执行的不要在服务端执行，能在缓存服务器上直接返回的不要到应用服务器，程序能直接取得的结果不要到外部取得，本机内能取得的数据不要到远程取，内存能取到的不要到磁盘取，缓存中有的不要去数据库查询。减少数据库操作指减少更新次数、缓存结果减少查询次数、将数据库执行的操作尽可能的让你的程序完成（例如join查询），减少磁盘IO指尽量不使用文件系统作为缓存、减少读写文件次数等。程序优化永远要优化慢的部分，换语言是无法“优化”的。<br/>


5、js的优化
-------
1）、将脚本置底： 加载js时会对后续的资源造成阻塞，必须得等js加载完才去加载后续的文件 ，所以就把js放在页面底部最后加载。<br/>
2）、使用外部Javascirpt和CSS文件：目的是缓存文件， 但有时候为了减少请求，也会直接写到页面里，需根据PV和IP的比例权衡。<br/>
3）、精简Javascript和CSS将Javascript或CSS中的空格和注释全去掉，压缩一行。<br/>
4）、去除重复脚本。<br/>
5）、减少DOM访问： a.缓存已经访问过的元素；b.Offline更新节点然后再加回DOM Tree；c.避免通过Javascript修复layout。<br/>
6）、使用智能事件处理：比如一个div中10个按钮都需要事件句柄，那么我们可以将事件放在div上，在事件冒泡过程中捕获该事件然后判断事件来源。<br/>

6、最后一个问题之你还有什么想问我的？
---------
1）、我进去之后会做什么？<br/>
2）、团队是做什么东西的（业务是什么）？<br/>
3）、内部项目还是外部项目？<br/>
4）、就我之前的表现来看，你觉得我的优缺点在哪里？（这个问题可以侧面打探出他对你的评价，而且可以帮助你给自己查漏补缺）。<br/>
5）、偏基础还是偏业务（简单粗暴地说，做基础就是写给程序员用的东西，做业务就是写给用户用的东西）？<br/>
6）、技术氛围怎么样？主要用到什么技术？有什么开源产出吗？你们做 code review 吗？<br/>
这些问题是帮助你拿到 offer 之后决定要不要接的，如果你投的不止一家公司，而且到时候拿到的 offer 势均力敌，这个信息就十分有用了。<br/>

7、对WEB标准以及W3C的理解与认识
-------
1）、将页面的结构、表现和行为各自独立实现，结构与表现要分离。<br/>
2）、标签闭合、标签小写、不乱嵌套、提高搜索机器人搜索几率、使用外链css和js脚本、结构行为表现的分离、文件下载与页面速度更快、内容能被更多的用户所访问、内容能被更广泛的设备所访问、更少的代码和组件，容易维护、改版方便，不需要变动页面内容、提供打印版本而不需要复制内容、提高网站易用性；<br/>
3）、Web标准不是某一个标准，而是一系列的标准集合。网页主要由三部分组成：结构（structure）、表现（Presentation）、行为（Behavior）。则对应的标准也分为三方面：结构化标准语言主要包括XHTML和XML、表现标准语言主要包括CSS、行为标准主要包括：对象模型（如W3C DOM）、ECMAScriprt等。这些标准大部分是由W3C万维网联盟起草和发布，也有一些其他标准组织制定的标准，如ECMA的ECMAScript标准。<br/>

8、页面重构怎么操作？
--------
网站重构：在不改变外部行为的前提下，简化结构、添加可读性，而在网站前端保持一致的行为。也就是说是在不改变UI的情况下，对网站进行优化，在扩展的同时保持一致的UI。<br/>
##### 对于传统的网站来说重构通常是：
表格(table)布局改为DIV+CSS<br/>
使网站前端兼容于现代浏览器(针对于不合规范的CSS、如对IE6有效的)<br/>
对于移动平台的优化<br/>
针对于SEO进行优化<br/>

##### 深层次的网站重构应该考虑的方面
减少代码间的耦合<br/>
让代码保持弹性<br/>
严格按规范编写代码<br/>
设计可扩展的API<br/>
代替旧有的框架、语言(如VB)<br/>
增强用户体验<br/>

##### 通常来说对于速度的优化也包含在重构中
压缩JS、CSS、image等前端资源(通常是由服务器来解决)<br/>
程序的性能优化(如数据读写)<br/>
采用CDN来加速资源加载<br/>
对于JS DOM的优化<br/>
HTTP服务器的文件缓存<br/>

9、列举IE与其他浏览器不一样的特性？
------
获取字符代码、如果按键代表一个字符（shift、ctrl、alt除外），IE的keyCode会返回字符代码（Unicode），DOM中按键的代码和字符是分离的，要获取字符代码，需要使用charCode属性；<br/>
##### 事件不同之处：
a、触发事件的元素被认为是目标（target）。而在IE中，目标包含在event对象的srcElement属性；<br/>
b、阻止某个事件的默认行为，IE中阻止某个事件的默认行为，必须将returnValue属性设置为false，Mozilla中，需要调用preventDefault()方法；<br/>
c、停止事件冒泡，IE 中阻止事件进一步冒泡，需要设置cancelBubble为 true，Mozzilla 中，需要调用stopPropagation()；<br/>

10、是否了解公钥加密和私钥加密。
-------
一般情况下是指私钥用于对数据进行签名，公钥用于对签名进行验证;<br/>
HTTP网站在浏览器端用公钥加密敏感数据，然后在服务器端再用私钥解密。<br/>

11、一个页面从输入 URL 到页面加载显示完成，这个过程中都发生了什么？（流程说的越详细越好）
--------
注：这题胜在区分度高，知识点覆盖广，再不懂的人，也能答出几句，而高手可以根据自己擅长的领域自由发挥，从URL规范、HTTP协议、DNS、CDN、数据库查询、到浏览器流式解析、CSS规则构建、layout、paint、onload/domready、JS执行、JS API绑定等等；<br/>
##### 详细版：
1）、当发送一个URL请求时，浏览器会开启一个线程来处理这个请求，对 URL 分析判断如果是 http 协议就按照 Web 方式来处理;<br/>
2）、通过DNS解析获取网址的IP地址，通过这个IP地址找到客户端到服务器的路径。<br/>
3）、进行HTTP协议会话，客户端发送报头(请求报头);与远程Web服务器通过TCP三次握手协商来建立一个TCP/IP连接。<br/>
4）、一旦TCP/IP连接建立，浏览器会通过该连接向远程服务器发送HTTP的GET请求。远程服务器找到资源，处理，并使用HTTP响应返回该资源，如果浏览器访问过，缓存上有对应资源，会与服务器最后修改时间对比，一致则返回304。<br/>
5）、值为200的HTTP响应状态表示一个正确的响应。浏览器开始下载html文档，同时使用缓存;<br/>
6）、根据标记请求所需指定MIME类型的文件（比如css、js）,同时设置了cookie;<br/>
7）、生成Dom树、解析css样式开始渲染DOM，JS根据DOM API操作DOM,执行事件绑定等，页面显示完成。<br/>
##### 简洁版：
1）、浏览器根据请求的URL交给DNS域名解析，找到真实IP，向服务器发起请求；<br/>
2）、服务器交给后台处理完成后返回数据，浏览器接收文件（HTML、JS、CSS、图象等）；<br/>
3）、浏览器对加载到的资源（HTML、JS、CSS等）进行语法解析，建立相应的内部数据结构（如HTML的DOM）；<br/>
4）、载入解析到的资源文件，渲染页面，完成。<br/>

12、平时如何管理你的项目？
--------
先期团队必须确定好全局样式（globe.css），编码模式(utf-8) 等；<br/>
编写习惯必须一致（例如都是采用继承式的写法，单样式都写成一行）；<br/>
标注样式编写人，各模块都及时标注（标注关键样式调用的地方）；<br/>
页面进行标注（例如 页面 模块 开始和结束）；<br/>
CSS跟HTML 分文件夹并行存放，命名都得统一（例如style.css）；<br/>
JS 分文件夹存放 命名以该JS功能为准的英文翻译。<br/>
图片采用整合的 images.png png8 格式文件使用 尽量整合在一起使用方便将来的管理。<br/>

13、移动端（Android IOS）怎么做好用户体验?
--------
清晰的视觉纵线、信息的分组、极致的减法、利用选择代替输入、标签及文字的排布方式、依靠明文确认密码、合理的键盘利用

14、请说出三种减少页面加载时间的方法。
-------
1）优化图片；<br/>
2）图像格式的选择（GIF：提供的颜色较少，可用在一些对颜色要求不高的地方）；<br/>
3）优化CSS（压缩合并css，如margin-top,margin-left...)<br/>
4）网址后加斜杠（如www.campr.com/目录，会判断这个“目录是什么文件类型，或者是目录。） <br/>
5）标明高度和宽度（如果浏览器没有找到这两个参数，它需要一边下载图片一边计算大小，如果图片很多，浏览器需要不断地调整页面。这不但影响速度，也影响浏览体验。当浏览器知道了高度和宽度参数后，即使图片暂时无法显示，页面上也会腾出图片的空位，然后继续加载后面的内容。从而加载时间快了，浏览体验也更好了。）<br/> 
6）减少http请求（合并文件，合并图片）。<br/>

15、哪些操作会造成内存泄漏？
-------
内存泄漏指任何对象在您不再拥有或需要它之后仍然存在。<br/>
垃圾回收器定期扫描对象，并计算引用了每个对象的其他对象的数量。如果一个对象的引用数量为 0（没有其他对象引用过该对象），或对该对象的惟一引用是循环的，那么该对象的内存即可回收。<br/>
setTimeout 的第一个参数使用字符串而非函数的话，会引发内存泄漏。<br/>
闭包、控制台日志、循环（在两个对象彼此引用且彼此保留时，就会产生一个循环）<br/>

16、SEO:
##### 1）、避免站点有死链接
　  死链接指的就是失效的链接、错误链接，它原本是正常的，但是后来就变成无效的链接，使得网页中打开这个死链接地址，服务器回应的就是打不开的页面或友好的404错误页面。
##### 2）、善于使用锚文本优化
　  文本关键词加入链接代码，达到点击这个关键词可以链接到你设置的页面
##### 3）、善于选择精准目标关键词和长尾关键词
　  目标关键词需要与网站的产品内容相符合
##### 4）、网站地图不可缺少
　  地图可以让搜索引擎更加容易爬行你的站点，在你的站点畅通无阻
##### 5）、网站优化前请确定首选域的选择
##### 6）、能够快速走出沙盒效应
　  通俗的讲就是搜索引擎对我们新站的考核期，在这考核期内，我们的网站不到频繁的进行模板的更改，标题的更改。
##### 7）、理解301重定向和302重定向
　301重定向也是网址重定向，当网站的域名发生变更后，搜索引擎只对新网址进行索引，采用301重定向之后，就可以把旧地址下原有的外部链接如数转移到新地址下且网站权重也不会发生变化，那么只需要301网址重定向即可搞定。
##### 8）、了解用户视线金三角
　  好的用户体验也是需要遵循用户浏览视线金三角的，通过了解用户浏览视线金三角，可以让SEO在今后可以善于去利用站内的权重来合理分配进行优化。
##### 9）、了解外部链接、反向链接、站外链接概念
　  外部链接：就是我们经常说的“外链”，也可以理解为反向链接或者导入链接，外部链接就除了网站外的站点指向你网站的url，包括首页和内页，但不带NOFOLLOW标签的，能让搜索引擎抓取到的。外部链接是决定你的网站pr权重最为重要的因素。
　   反向链接:抽象的表示就是A网站上有一个链接指向B网站，那么A网站上的链接就是B网站的反向链接
　   站外链接:就是所有来自站外的链接指向我们的目标网站都属于站外链接。
##### 10）、了解seo行业中的pv、uv、ip概念
　　IP：指独立IP数。即IP地址，一个电脑可能一天换多个IP。
　　UV：指独立访客，即访问您网站的一台电脑客户端为一个访客;每天网站的独立访问，一个IP下可以有多个电脑，那么这多台电脑的独立访问就算是多次的uv。
　　PV：指页面浏览量，所有的页面被浏览的总次数，每个页面的点击都计算一次。
##### 11）、不要和链接工厂交换链接，交换链接尽量避免各种陷阱
　链接工厂，是整个网站或网站中的一部分页面没有实质性的内容，单纯的为了交换链接互相传递pr来提高这个网页的PR值而存在，站点本身是没有其他内容或者极少内容，它是一种典型的seo作弊行为的网站，与链接养殖场相互链接的网站比较容易被百度蜘蛛k掉并拒绝收录的危险。
　　例如pr劫持，垃圾网站、门页网站、站群、网页劫持站点、链接养殖场等站点的就属于友情链接陷阱的站点。
##### 12）、使用面包屑导航或者树状导航
　　面包屑导航(或称为面包屑路径)是一种显示用户在网站或网络应用中的位置的一层层指引的导航。在互联网中，面包屑为用户提供一种追踪返回最初访问页面的方式，可以清晰的为客户指引进入网站内部也和首页之间的路线。最简化的方式是，面包屑就是水平排列的被大于号">"隔开的文本链接;这个符号指示该页面相对于链接到它的页面的深度(级别)。
##### 13）、尽量使用绝对链接来做站内优化，而非相对链接
　　相对链接，相对路径指的是不包含网站首页域名的网址，而是使用返回某个文件的命令来让网站进入某个页面或者返回首页;而绝对链接是包含首页域名的完整的网址。
　　在设计网站的时候，栏目页面链接都是采用相对链接的，相对链接代码简洁、使用方便，可以减少多余代码，但相对链接也容易导致断链死链接的存在。所以最好的解决方法是使用绝对链接，使用绝对链接可以避免上面提到的所有的潜在问题，是让链接真正正常工作的最好的方法。
##### 14）、关键词密度应该设置为多少，如何提高关键词密度
　　目标关键词超过了2%即可，但是如果从网站运营的角度出发，更多的是应该围绕用户体验建站。
##### 15）、了解nofollow标签
　　nofollow标签常用语添加在超链接上的HTML标签，指示搜索引擎不要去爬行，那么搜索引擎看到这个标签就可能减少或完全取消该超链接的投票权重，那么搜索引擎就不会去收录该超链接的页面，nofollow标签最初是由google发明的，目的是尽量减少垃圾链接对搜索引擎的影响。
##### 16）、了解黑帽seo、白帽seo、灰帽seo
　　黑帽技术就是采用一种作弊的方法来进行seo的优化，黑帽可以在短期内提升网站的排名和流量;白帽技术是采用很正规的方式来进行网站的优化的，它可以很好的提高网站的用户体验，也可以很好与其他网站进行链接交换等互动性;灰帽技术是介于白帽和黑帽中间的一种优化手法。
##### 17）、了解交叉链接，方便很好进行链接交换
　　交叉链接还是需要通过做交换链接来实现，一般的友情链接只是2个站之间的链接交换，而交叉链接则是三个站或者更多站之间的链接交换.
##### 18）、了解轮链和站群
　　所谓“轮链”也可以称之为“链轮”，就是链接有目的性的轮着指向下一个站点，三个以上的链接单向轮流指向下一个链接，有A网、B网、C网、D网四个网站，做轮链就是A网页面链接指向B网，B网指向C网，C网指向D网，虽然D网只有C网一个链接，但是其实A网、B网都有间接指向了D网，获得更大的权重，而C网也不仅仅获得B网一个页面的链接。
##### 19）、了解各种权重标签
　　权重标签就是会影响页面权重或者相关性的html标签。权重标签常用于突出页面中相对重要的内容，从而提高页面相关性，增加页面权重。H1，strong,alt这些标签都是权重标签。
##### 20）、提高seo转化率是用户体验好的表现
　　seo转化率指的是用户通过搜索引擎进入我们的网站，在我们网站进行的相应网站用户行为的访问次数与总访问次数的比率，而seo转化率就是通过搜索引擎优化把进来网站的访客转化成网站常驻用户，也可以理解为访客到用户的转换。


17、BFC
--------
block-level box:display属性为 block, list-item, table 的元素，会生成 block-levelbox。并且参与 block fomatting context；
inline-level box:display属性为 inline, inline-block, inline-table 的元素，会生成inline-level box。并且参与 inline formatting context；
BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与，它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。在页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。
BFC布局规则：
内部的Box会在垂直方向，一个接一个地放置。
Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
BFC的区域不会与float box重叠。
BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
计算BFC的高度时，浮动元素也参与计算
哪些元素会生成BFC：
根元素
float属性不为none
position为absolute或fixed
display为inline-block, table-cell, table-caption, flex, inline-flex
overflow不为visible
应用：自适应的两栏布局，清除内部浮动，防止margin重叠


22.cookie，localStroage,sessionStroage
共同点：都是保存在浏览器端，且同源的。
区别：cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；
cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
作用域不同:sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。
sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。
因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。

23.prototype与__proto__
prototype是函数的内置属性，__proto__是对象的内置属性。
每一个函数都有一个prototype(原型)属性, 可以返回对象的原型对象的引用。prototype是通过调用构造函数来创建的那个对象的原型(属性)对象。
函数(对象)有prototype属性->对应了一个原型对象->每个原型对象都有一个constructor属性->包含一个指向prototype属性所在函数的指针
prototype:每一个函数对象都有一个显示的prototype属性,它代表了对象的原型，指向原型对象。
    __proto__:内部原型(IE6/7/8/9不支持),每个对象实例都有一个名为__proto__的内部隐藏属性，指向于它所对应的原型对象,
继承： 原型对象等于另一类型的实例。此时的原型对象包含一个指向另一原型的指针(__proto__)
 
24.src与href的区别
href 是指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，用于超链接。link a
src是指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置；在请求src资源时会将其指向的资源下载并应用到文档内，例如js脚本，img图片和frame等元素。

25.什么是CSS Hack?
一般来说是针对不同的浏览器写不同的CSS,就是 CSS Hack。
IE浏览器Hack一般又分为三种，条件Hack、属性级Hack、选择符Hack
// 1、条件Hack
   <!--[if IE]>
      <style>
           .test{color:red;}
      </style>
   <![endif]-->
   // 2、属性Hack
    .test{
    color:#090\9; /*For IE8+ */
    +color:#f00;/* For IE6,7,8*/
    *color:#f00;  /* For IE7 and earlier */
    _color:#ff0;  /* For IE6 and earlier */
    }
   // 3、选择符Hack
    * html .test{color:#090;}       /* For IE6 and earlier */
    * + html .test{color:#ff0;}     /* For IE7 */


实现一个函数clone，可以对JavaScript中的5种主要的数据类型（包括Number、String、Object、Array、Boolean）进行值复制。
Object.prototype.clone = function(){
   var o =this.constructor === Array ? [] : {};
   for(var e in this){
    o[e] = typeof this[e] === "object" ? this[e].clone() : this[e];
   }
   return o;
}
function clone(Obj){
  var buf;
  if(Obj instanceOf Array){
  buf=[];
 var i=Obj.length;
 while(i--){
  buf[i]=clone(obj[i]);
}
return buf;
}else if(ObjinstanceOf Object){
   buf={};
   for(var k in Obj){  buf[k]=clone(obj[k]);}
return buf;
} else { returnObj;//普通变量 }
}

29.一个页面上有大量的图片（大型电商网站），加载很慢，你有哪些方法优化这些图片的加载，给用户更好的体验。
图片懒加载：在页面上的未可视区域可以添加一个滚动条事件，判断图片位置与浏览器顶端的距离与页面的距离，如果前者小于后者，优先加载。
如果为幻灯片、相册等，可以使用图片预加载技术，将当前展示图片的前一张和后一张优先下载。
如果图片为css图片，可以使用CSSsprite，SVGsprite，Iconfont、Base64等技术。
如果图片过大，可以使用特殊编码的图片，加载时会先加载一张压缩的特别厉害的缩略图，以提高用户体验。
如果图片展示区域小于图片的真实大小，则因在服务器端根据业务需要先行进行图片压缩，图片压缩后大小与展示一致。
30.在Javascript中什么是伪数组？如何将伪数组转化为标准数组？
伪数组（类数组）：无法直接调用数组方法或期望length属性有什么特殊的行为，但仍可以对真正数组遍历方法来遍历它们。典型的是函数的argument参数，还有像调用getElementsByTagName,document.childNodes之类的,它们都返回NodeList对象都属于伪数组。可以使用Array.prototype.slice.call(fakeArray)将数组转化为真正的Array对象。
31.Javascript中callee和caller的作用？
caller是返回一个对函数的引用，该函数调用了当前函数；
callee是返回正在被执行的function函数，也就是所指定的function对象的正文。

32.统计字符串中字母个数或统计最多字母数。
var str = 'asdfssaaasasasasaa';
var json = {};
for (var i = 0; i < str.length; i++) {
       if(!json[str.charAt(i)]){
               json[str.charAt(i)] = 1;
        }else{
               json[str.charAt(i)]++;
        }
};
var iMax = 0;
var iIndex = '';
for(var i in json){
       if(json[i]>iMax){
                iMax =json[i];
                iIndex= i;
        }
}
alert('出现次数最多的是:'+iIndex+'出现'+iMax+'次');

33.字符串反转，如将 '12345678' 变成 '87654321'
/思路：先将字符串转换为数组 split()，利用数组的反序函数 reverse()颠倒数组，再利用 jion() 转换为字符串
var str = '12345678';
str =str.split('').reverse().join('');
34.将数字 12345678 转化成 RMB形式如： 12,345,678
//思路：先将数字转为字符， str= str + '' ;
//利用反转函数，每三位字符加一个 ','最后一位不加； re()是自定义的反转函数，最后再反转回去！
for(var i = 1; i <= re(str).length; i++){
    tmp += re(str)[i -1];
    if(i % 3 == 0&& i != re(str).length){
        tmp += ',';
    }
}
35.BOM对象有哪些，列举window对象？
   1、window对象 ，是JS的最顶层对象，其他的BOM对象都是window对象的属性；
 	2、document对象，文档对象；
   3、location对象，浏览器当前URL信息；
   4、navigator对象，浏览器本身信息；
   5、screen对象，客户端屏幕信息；
        6、history对象，浏览器访问历史信息；

36.简述readonly与disabled的区别
1、Readonly只针对input(text / password)和textarea有效，而disabled对于所有的表单元素都有效，
2、但是表单元素在使用了disabled后，当我们将表单以POST或GET的方式提交的话，这个元素的值不会被传递出去，而readonly会将该值传递出去（readonly接受值更改可以回传，disable接受改但不回传数据）。

37.css有个content属性吗？有什么作用？有什么应用？
css的content属性专门应用在 before/after 伪元素上，用来插入生成内容。最常见的应用是利用伪类清除浮动。
//一种常见利用伪类清除浮动的代码
.clearfix:after {
    content:".";//这里利用到了content属性
    display:block;
    height:0;
    visibility:hidden;
    clear:both; }
.clearfix {    *zoom:1;    }
after伪元素通过 content 在元素的后面生成了内容为一个点的块级素，再利用clear:both清除浮动。
　那么问题继续还有，知道css计数器（序列数字字符自动递增）吗？如何通过css content属性实现css计数器？
css计数器是通过设置counter-reset 、counter-increment 两个属性 、及counter()/counters()一个方法配合after / before 伪类实现。

38.一次完整的HTTP事务是怎样的一个过程？
基本流程：
a. 域名解析
b. 发起TCP的3次握手
c. 建立TCP连接后发起http请求
d. 服务器端响应http请求，浏览器得到html代码
e. 浏览器解析html代码，并请求html代码中的资源
f. 浏览器对页面进行渲染呈现给用户

41.写一个function，清除字符串前后的空格。
if (!String.prototype.trim) {
 String.prototype.trim= function() {
 returnthis.replace(/^\s+/, "").replace(/\s+$/,"");
 }
}

42.事件类型
UI事件：load，unload，select,resize,scroll
焦点事件:blur, focus
鼠标事件:click, dbclick, mousedown, mouseleave
滚轮事件:mousewheel
文本事件:textinput
键盘事件: keydown, keypress, keyup

43.js的浅拷贝和深拷贝
浅复制：浅复制是复制引用，复制后的引用都是指向同一个对象的实例，彼此之间的操作会互相影响
深复制：深复制不是简单的复制引用，而是在堆中重新分配内存，并且把源对象实例的所有属性都进行新建复制，以保证深复制的对象的引用图不包含任何原有对象或对象图上的任何对象，复制后的对象与原来的对象是完全隔离的
concat和slice常用来做深度拷贝
（1）数组的浅拷贝和深拷贝
1）var arr =["One","Two","Three"];     var arrto = arr;
如果只是将数组的名字赋值给其他变量，改变其中一个，也会改变另一个。这种方式就是浅拷贝。
2）js的slice方法：array对象的slice函数，返回一个数组的一段。（仍为数组）
var arr =["One","Two","Three"];
var arrtoo = arr.slice(0); //从0开始，默认的结束时到最后
arrtoo[1] = "set Map";
document.writeln(arr + "<br />");//Export:数组的原始值：One,Two,Three
document.writeln(arrtoo + "<br />");//Export:数组的新值：One,set Map,Three
3）concat() 方法用于连接两个或多个数组。该方法不会改变现有的数组，而仅仅会返回被连接数组的一个副本。
var arr =["One","Two","Three"];
var arrtooo = arr.concat();
arrtooo[1] = "set Map To";
document.writeln(arr); // One,Two,Three
document.writeln(arrtooo); // One,set Map To,Three
4）function deepCopy(arry1,arry2){
    for(var i = 0,l=arry1.length;i<l;i++){
        arry2[i] =arry1[i];
    }  }
（2）对象的浅拷贝和深拷贝
1）JSON对象parse方法可以将JSON字符串反序列化成JS对象，stringify方法可以将JS对象序列化成JSON字符串，借助这两个方法，也可以实现对象的深复制。
varsource = {
    name:"source",
    child:{
        name:"child"
    }
}
vartarget = JSON.parse(JSON.stringify(source));
target.name= "target";  //改变target的name属性
console.log(source.name);   //source
console.log(target.name);   //target
target.child.name= "target child";  //改变target的child
console.log(source.child.name);  //child
console.log(target.child.name);  //target child
2）把对象的属性遍历一遍，赋给一个新的对象。
vardeepCopy= function(source) {
var result={};
for (var key insource) {
      result[key] = typeof source[key]===’object’?deepCopy(source[key]): source[key];
   }   return result;   }


44.常用的布局，什么是响应式布局？
1.  固宽布局：各个模块是固定宽度
特点：设计简单，不会受到图片等固定宽度内容的影响。对比高分辨率的用户，固定宽度会留下很多空白，屏幕小出现滚动条。
2.  流动布局使用百分比的方式自适应不同的分辨率
特点：对用户友好，能够部分自适应用户的设置。网页周围的空白区域在所有分辨率和显示器下都是相同，视觉上美观。设计者需要进行不同的测试，准备不同的对应素材
3.  弹性布局使用em作为单位，em是相对单位，随用户字体大小变化而改变
特点：页面中所有元素可以随着用户的偏好缩放，需要花更多的事件测试，让布局适合所有的用户
4.  栅格化布局，也分为固定式栅格，流式栅格
在网页设计中，我们把宽度为“W”的页面分割成n个网格单元“a”，每个单元与单元之间的间隙设为“i”,此时我们把“a+i”定义“A”。他们之间的关系如下：W =（a*n）+（n-1）*I，由于a+i=A，因此可得：(A×n) – i = W。注：最左右边没有边距(margin-left,marign-right)。
       特点：可以提高网页的规范性和可用性。在栅格系统下，页面模块的尺寸标准化。整个网站的各个页面布局一致，增加页面的相似度。
5.  响应式布局
允许页面宽度自动调整，利用媒体查询根据不同的宽度设置不同的样式，液态布局，自适应媒体（图片，视频）。
（1）   运行页面宽度自动调整：在网页头部加入一张viewport元标签
<metaname=”viewport” content=”width=device-width,initial-scale=1.0”>
网页的宽度默认等于屏幕宽度，原始缩放比例是1，即网页初始大小占屏幕面积的100%
（2）   利用媒体查询设置不同的样式
<linkrel=”stylesheet” type=”text/css” href=”site.css” media=”screen”/>
<linkrel=”stylesheet” type=”text/css”href=”print.css” media=”print”>
screen:使用于计算机彩色屏幕
print：使用与打印预览模式下查看的内容或打印机打印的内容

45.Ajax工作原理，同步和异步
Ajax就是通过JavaScript创建XMLHttpRequest对象，再由JavaScript调用XMLHttpRequest对象的方法完成异步通信；然后，再由JavaScript通过DOM的属性和方法，完成页面的不完全刷新。
   AJAX全称为“Asynchronous JavaScript and XML”（异步JavaScript和XML）
由事件触发，创建一个XMLHttpRequest对象，把HTTP方法（Get/Post）和目标URL以及请求返回后的回调函数设置到XMLHttpRequest对象，通过XMLHttpRequest向服务器发送请求，请求发送后继续响应用户的界面交互，只有等到请求真正从服务器返回的时候才调用callback()函数，对响应数据进行处理。
同步：脚本会停留并等待服务器发送回复然后再继续
异步：脚本允许页面继续其进程并处理可能的回复
（1）XMLHttpRequest简介
XMLHttpRequest，是我们得以实现异步通讯的的根本。
用XMLHttpRequest进行异步通讯，首先必须用JavaScript创建一个XMLHttpRequest对象实例。创建XMLHttpRequest对象实例的代码清单如下所示：
var xmlHttp;
functioncreateXMLHttpRequest(){
   if(window.ActiveXObject){
 xmlHttp = newActiveXObject("Microsoft.XMLHTTP");
} elseif(window.XMLHttpRequest){
 xmlHttp = new XMLHttpRequest();
}
}
（2）利用XMLHttpRequest对象发送简单请求
1）  创建XMLHttpRequest对象实例。
2）  设定XMLHttpRequest对象的回调函数，利用onreadystatechange属性。
3）  设定请求属性：设定HTTP方法（GET或POST）；设定目标URL。利用open()方法。
4）  将请求发送给服务器。利用send()方法。
（3）利用DOM对服务器响应进行处理
前面已经设置了回调函数，回调函数正是用来处理服务器响应信息的。在服务器对我们的请求信息作出响应后，我们就得实现页面的无缝更新（就是无闪烁的更新信息）。通过DOM，我们可以把页面上的数据和结构抽象成一个树型表示，进而可以通过DOM中定义的属性和方法对文档进行操作，如遍历、编辑等。这样，服务器相应信息就可以通过DOM的方法和属性，动态的更新到页面的相应节点。从而使用户感觉不到刷新过程的存在，提高了交互性。
（4）实例
实例包含两个文件：Request.htm和Response.xml。通过Request.htm向服务器发送请求，而Response.xml模仿了从服务器返回的响应。两个文件清单如下：
<!--Request.htm----------------------------------------------------------->
<html>
    <head>
        <title>Ajax应用实例</title>
        <scripttype="text/javaScript">
            var xmlHttp;
varrequestType="";
functioncreateXMLHttpRequest(){
    if(window.ActiveXObject){
        xmlHttp = newActiveXObject("Microsoft.XMLHTTP");
    }
    else if(window.XMLHttpRequest){
        xmlHttp = new XMLHttpRequest();
    }
}
functionstartRequest(theRequestType){
requestType= theRequestType;
    createXMLHttpRequest();
    xmlHttp.onreadystatechange =handleStateChange;
   xmlHttp.open("GET","Response.xml",true);
    xmlHttp.send(null);
}
functionmyCallback(){
    if(xmlHttp.readyState==4){
        if(xmlHttp.status==200){
            if(requestType=="all")
                listAll();
            elseif(requestType=="north")
                listNorth();
        }
    }
}
 (1).AJAX的优点
<1>.无刷新更新数据。
AJAX最大优点就是能在不刷新整个页面的前提下与服务器通信维护数据。这使得Web应用程序更为迅捷地响应用户交互，并避免了在网络上发送那些没有改变的信息，减少用户等待时间，带来非常好的用户体验。
<2>.异步与服务器通信。
AJAX使用异步方式与服务器通信，不需要打断用户的操作，具有更加迅速的响应能力。优化了Browser和Server之间的沟通，减少不必要的数据传输、时间及降低网络上数据流量。
<3>.前端和后端负载平衡。
AJAX可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，AJAX的原则是“按需取数据”，可以最大程度的减少冗余请求和响应对服务器造成的负担，提升站点性能。
<4>.基于标准被广泛支持。
<5>.界面与应用分离。
Ajax使WEB中的界面与应用分离（也可以说是数据与呈现分离），有利于分工合作、减少非技术人员对页面的修改造成的WEB应用程序错误、提高效率、也更加适用于现在的发布系统。
(2).AJAX的缺点
<1>.AJAX干掉了Back和History功能，即对浏览器机制的破坏。
在动态更新页面的情况下，用户无法回到前一个页面状态，因为浏览器仅能记忆历史记录中的静态页面。一个被完整读入的页面与一个已经被动态修改过的页面之间的差别非常微妙；用户通常会希望单击后退按钮能够取消他们的前一次操作，但是在Ajax应用程序中，这将无法实现。
<2>.AJAX的安全问题。
Ajax技术就如同对企业数据建立了一个直接通道。这使得开发者在不经意间会暴露比以前更多的数据和服务器逻辑。Ajax的逻辑可以对客户端的安全扫描技术隐藏起来，允许黑客从远端服务器上建立新的攻击。还有Ajax也难以避免一些已知的安全弱点，诸如跨站点脚步攻击、SQL注入攻击和基于Credentials的安全漏洞等等。
<3>.对搜索引擎支持较弱。
对搜索引擎的支持比较弱。如果使用不当，AJAX会增大网络数据的流量，从而降低整个系统的性能。
<4>.破坏程序的异常处理机制。
<5>.违背URL和资源定位的初衷。
<6>.AJAX不能很好支持移动设备。
<7>.客户端过肥，太多客户端代码造成开发上的成本。编写复杂、容易出错；冗余代码比较多，破坏了Web的原有标准。
5.AJAX注意点及适用和不适用场景
(1).注意点
Ajax开发时，网络延迟——即用户发出请求到服务器发出响应之间的间隔——需要慎重考虑。不给予用户明确的回应，没有恰当的预读数据，或者对XMLHttpRequest的不恰当处理，都会使用户感到延迟，这是用户不希望看到的，也是他们无法理解的。通常的解决方案是，使用一个可视化的组件来告诉用户系统正在进行后台操作并且正在读取数据和内容。
(2).Ajax适用场景
<1>.表单驱动的交互<2>.深层次的树的导航<3>.快速的用户与用户间的交流响应
<4>.类似投票、yes/no等无关痛痒的场景<5>.对数据进行过滤和操纵相关数据的场景
<6>.普通的文本输入提示和自动完成的场景
(3).Ajax不适用场景
<1>.部分简单的表单<2>.搜索<3>.基本的导航<4>.替换大量的文本<5>.对呈现的操纵

46.Javascript异步编程的4种方法
当线程中没有执行任何同步代码的前提下才会执行异步代码，setTimeout是异步代码，所以setTimeout只能等js空闲才会执行。
一、 回调函数
function f1(callback){  //假设f1是一个很耗时的函数，把f2写成f1的回调函数
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　callback();
　　　　}, 1000);   　　}
执行代码就变成下面这样：　　f1(f2);
采用这种方式，我们把同步操作变成了异步操作，f1不会堵塞程序运行，相当于先执行程序的主要逻辑，将耗时的操作推迟执行。回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度耦合（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。
二、 事件监听
采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。
首先，为f1绑定一个事件：　　f1.on('done', f2);
当f1发生done事件，就执行f2。然后，对f1进行改写：
　　function f1(){
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　f1.trigger('done');
　　　　}, 1000);  　　}
f1.trigger('done')表示，执行完成后，立即触发done事件，从而开始执行f2。
这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"（Decoupling），有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。
三、 发布/订阅
我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。
首先，f2向"信号中心"jQuery订阅"done"信号：　jQuery.subscribe("done",f2);
然后，f1进行如下改写：
　　function f1(){
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　jQuery.publish("done");
　　　　}, 1000);   　　}
jQuery.publish("done")的意思是，f1执行完成后，"信号中心"jQuery发布"done"信号，从而引发f2的执行。
此外，f2完成执行后，也可以取消订阅（unsubscribe）。
　　jQuery.unsubscribe("done", f2);
这种方法的性质与"事件监听"类似，但是明显优于后者。因为我们可以通过查看"消息中心"，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。
四、Promises对象
每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。比如，f1的回调函数f2,可以写成：　　f1().then(f2);
f1要进行如下改写（这里使用的是jQuery的实现）：
　　function f1(){
　　　　var dfd = $.Deferred();
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　dfd.resolve();
　　　　}, 500);
　　　　return dfd.promise;
　　}
这样写的优点在于，回调函数变成了链式写法，程序的流程可以看得很清楚，而且有一整套的配套方法，可以实现许多强大的功能。
比如，指定多个回调函数：　　f1().then(f2).then(f3);
再比如，指定发生错误时的回调函数：　　f1().then(f2).fail(f3);
而且，它还有一个前面三种方法都没有的好处：如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。这种方法的缺点就是编写和理解，都相对比较难。

47.图片轮播
<script>
            window.onload=function() {
                var list =document.getElementById("list");
                var liList =document.getElementsByTagName("li"); //所有图片
                var len = liList.length; //个数
                var liwidth =liList[0].clientWidth; //每张图片的宽度
                var totalWidth = (len - 1) * liwidth* (-1); //图片总宽度
                var varyLeft = list.offsetLeft;//ul初始left值
                var speed = 3; //每次移动距离                
                function move() {
                   if (varyLeft < totalWidth){//左移完最后一张后，瞬间切换到第二张a，第二张a和最后一张a'相同
                       list.style.left ="-300px";
                       varyLeft = -300;
                   }                 
                   varyLeft -= speed;//每次移动
                   list.style.left = varyLeft +"px";
                }
                var timer = setInterval(move,30);//每个40毫秒左移一次      
            }
        </script>

 JavaScript实现DOM树的遍历
function traversal(node){  
   if(node && node.nodeType ===1){  //对node的处理
     console.log(node.tagName);
   }
   var i = 0, childNodes =node.childNodes,item;
   for(; i < childNodes.length ; i++){
     item = childNodes[i];
     if(item.nodeType === 1){      
       traversal(item);  //递归先序遍历子节点
     }
   }   }
functiontraverseNodes(node){   
         if(node.nodeType == 1){  //判断是否是元素节点 
           display(node);                
           for(var i=0;i<node.attributes.length;i++){  //判断是否有属性节点                
                var attr =node.attributes.item(i);  //得到属性节点                 
                  if(attr.specified){  //判断该节点是否存在
                       display(attr); //如果存在显示输出
                } 
           }                
              if(node.hasChildNodes){  //判断该元素节点是否有子节点
                 var sonnodes = node.childNodes;  //得到所有的子节点
                  for (var i = 0; i < sonnodes.length; i++){  //遍历所有的子节点
                      var sonnode = sonnodes.item(i);  //得到具体的某个子节点
                       traverseNodes(sonnode);  //递归遍历
                } 
           } 
       }else{ 
           display(node); 
       } 
} 
DOM的广度和深度遍历
Tree.prototype.BFSearch =  function(node,callback){ 
   var queue=[]; 
   while(node!=null){         
      callback(node); 
      if(node.children.length!=0){ 
       for (var i=0;i<node.children.length;i++){ 
           queue.push(node.children[i]);//借助于队列,暂存当前节点的所有子节点 
       }  
       } 
           node=queue.shift();//先入先出，借助于数据结构：队列 
   }        
}; 
Tree.prototype.DFSearch =  function(node,callback){ 
       var stack=[];        
       while(node!=null){ 
       callback(node); 
       if(node.children.length!=0){ 
       for (var i=node.children.length-1;i>=0;i--){//按照相反的子节点顺序压入栈 
           stack.push(node.children[i]);//将该节点的所有子节点压入栈 
       } 
       } 
     node = stack.pop();//弹出栈的子节点顺序就是原来的正确顺序(因为栈是先入后出的)       
   }    
}; 

2.    javascript实现链表的操作

//Node类和LList类
function Node(element){
    this.element=element;
    this.next=null;
}
function LList(){
    this.head=new Node("head");
    this.find=find;
    this.insert=insert;
    this.display=display;
    this.findPrevious=findPrevious;
    this.remove=remove;
}
function display(){  //显示
    var currNode=this.head;
    while(!(currNode.next==null)){
        print(currNode.next.element);
        currNode=currNode.next;
}
function find(item){   //查找
    var currNode=this.head;
    while(currNode.element!=item){
        currNode=currNode.next;
    }
    return currNode;
}
functioninsert(newElement,item){  //插入
var newNode=new Node(newElement);
varcurrent=this.find(item);
newNode.next=current.next;
current.next=newNode;
}
function remove(item){  //移除
var preNode=this.findPrevious(item);
    if(!(preNode.next==null)){
     preNode.next=preNode.next.next;
    }
}  

3.  javascript实现import动态导入节点：
1）
var $import =function(){
    return function(rId, res, callback){
        if(res && 'string' == typeofres){
            if(rId){
                if($($('#' + rId),$('head')).length>0){
                    return;
                }
            }            
            var sType =res.substring(res.lastIndexOf('.') + 1); //加载资源文件
             if(sType && ('js' == sType || 'css'== sType)){ // 支持js/css
                var isScript = (sType == 'js');
                var tag = isScript ? 'script' :'link';
                var head =document.getElementsByTagName('head')[0];              
                var linkScript =document.createElement(tag); // 创建节点
                linkScript.type = isScript ?'text/javascript' : 'text/css';
                linkScript.charset = 'UTF-8';
                if(!isScript){
                    linkScript.rel = 'stylesheet';
                }
                isScript ? linkScript.src = res: linkScript.href = res;
                if(callback &&'function' == typeof callback){
                    if(linkScript.addEventListener){
                        linkScript.addEventListener('load',function(){
                            callback.call();
                        }, false);
                    } else if(linkScript.attachEvent) {
                       linkScript.attachEvent('onreadystatechange', function(){
                            var target =window.event.srcElement;
                            if(target.readyState == 'complete') {
                               callback.call();
                            }
                        });    }   }
                head.appendChild(linkScript);
            } }  };  }();
2）var JCore ={//构造核心对象
version:1.0,
$import:function(importFile){
var file =importFile.toString();
varIsRelativePath = (file.indexOf("$")==0 ||file.indexOf("/")==-1);//相对路径(相对于JCore)
var path=file;
if(IsRelativePath){//计算路径,$开头表示使用当前脚本路径，/开头则是完整路径
if(file.indexOf("$")==0)
file =file.substr(1);
path =JCore.$dir+file;
}
varnewElement=null,i=0;
var ext =path.substr(path.lastIndexOf(".")+1);
if(ext.toLowerCase()=="js"){
var scriptTags =document.getElementsByTagName_r("script");
for(var i=0;ilength;i++) {
if(scriptTags[i].src && scriptTags[i].src.indexOf(path)!=-1)
return;
}
newElement=document.createElement_x("script");
newElement.type="text/javascript";
newElement.src=path;
}
elseif(ext.toLowerCase()=="css"){
var linkTags = document.getElementsByTagName_r("link");
for(var i=0;ilength;i++) {
if(linkTags[i].href && linkTags[i].href.indexOf(path)!=-1)
return;
}
newElement=document.createElement_x("link");
newElement.type="text/css";
newElement.rel="Stylesheet";
newElement.href=path;
}
else
return;
var head=document.getElementsByTagName_r("head")[0];
head.appendChild(newElement);
},
$dir :function(){
var scriptTags = document.getElementsByTagName_r("script");
for(var i=0;ilength;i++) {
if(scriptTags[i].src &&scriptTags[i].src.match(/JCore/.js$/)) {
path = scriptTags[i].src.replace(/JCore/.js$/,"");
return path;
}  }
return "";
}()
}

4.    javascript实现分页函数：
function goPage(pno,psize){
    var itable =document.getElementById("idData");
    var num = itable.rows.length;//表格所有行数(所有记录数)   
    var totalPage = 0;//总页数
    var pageSize = psize;//每页显示行数   
    if(num/pageSize >parseInt(num/pageSize)){  //总共分几页  
            totalPage=parseInt(num/pageSize)+1;  
       }else{  
          totalPage=parseInt(num/pageSize);  
       }  
    var currentPage = pno;//当前页数
    var startRow = (currentPage - 1) *pageSize+1;//开始显示的行  31
    var endRow = currentPage * pageSize;//结束显示的行   40
    endRow = (endRow > num)? num :endRow;    40     
    for(var i=1;i<(num+1);i++){   //遍历显示数据实现分页 
        var irow = itable.rows[i-1];
        if(i>=startRow &&i<=endRow){
            irow.style.display ="block";   
        }else{
            irow.style.display = "none";
        }
    }
     varpageEnd = document.getElementById("pageEnd");
    var tempStr = "共"+num+"条记录 分"+totalPage+"页 当前第"+currentPage+"页";
    if(currentPage>1){
        tempStr += "<ahref=\"#\" onClick=\"goPage("+(1)+","+psize+")\">首页</a>";
tempStr += "<a href=\"#\"onClick=\"goPage("+(currentPage-1)+","+psize+")\"><上一页</a>"
    }else{
        tempStr += "首页";
        tempStr += "<上一页";   
    }
 if(currentPage<totalPage){
tempStr += "<a href=\"#\"onClick=\"goPage("+(currentPage+1)+","+psize+")\">下一页></a>";
tempStr += "<a href=\"#\"onClick=\"goPage("+(totalPage)+","+psize+")\">尾页</a>";
    }else{
        tempStr += "下一页>";
        tempStr += "尾页";   
    }
5.    String()与toString()的区别：
     (1)null和undefined有String()转换成字符串，而toString()不能；
     (2)toString()能设定数值数据转换的进制数，而String()不能；
     (3)其他情况下：toString(val) === String(val)
6.    下面这个ul，如何点击每一列的时候alert其index?（闭包）
<ul id=”test”>
<li>这是第一条</li>
<li>这是第二条</li>
<li>这是第三条</li>
</ul>
// 方法一：
var lis=document.getElementById('test').getElementsByTagName('li');
for(var i=0;i<3;i++)
{   lis[i].index=i;
   lis[i].onclick=function(){
       alert(this.index);
   };
}
//方法二：
var lis=document.getElementById('test').getElementsByTagName('li');
for(var i=0;i<3;i++){
   lis[i].index=i;
   lis[i].onclick=(function(a){
       return function() {
           alert(a);
       }
   })(i);
}

7.    javascript实现快排
选择中间的元素作为基准，其他的元素和基准比较，形成两个子集，不断重复，直到所有子集剩下一个元素为止；
   var quickSort = function(arr) {
    　　if(arr.length <= 1) { return arr; }
    　　varpivotIndex = Math.floor(arr.length / 2);
    　　var pivot =arr.splice(pivotIndex, 1)[0];
    　　var left =[];
    　　var right =[];
    　　for (var i= 0; i < arr.length; i++){
    　　　　if(arr[i] < pivot) {
    　　　　　　left.push(arr[i]);
    　　　　} else {
    　　　　　　right.push(arr[i]);
    　　　　}
    　　}
    　　returnquickSort(left).concat([pivot], quickSort(right));
   };

8.    javascript实现冒泡排序
bubbleSort: function(array) {
   var i = 0,
   len = array.length,
   j, d;
   for (; i < len; i++) {
       for (j = 0; j < len; j++) {
           if (array[i] < array[j]) {
                d = array[j];
                array[j] = array[i];
                array[i] = d;
           }
       }
    }
   return array;
},

9.    插入排序：
function insertSort(arr){
   for(var i =1,j;i<arr.lenght;i++){
       j=i;
       v=arr[j];
       while(arr[j-1]>v){
          arr[j] = arr[j-1];
          j--;
          if(j == 0){
          break;
         }
       }
       arr[j]=v;
    }
   return arr;
}

10. 选择排序:
function selectSort(arr){
   var len=arr.length;
   var temp;
   for(var i=0;i<len-1;i++){
      for(var j=i+1;j<len;j++){
         if(arr[j]<arr[i]){
            temp=arr[j];
            arr[j]=arr[i];
            arr[i]=temp;
         }
       }
      i++;
     }
    returnarr;
  }

11. 希尔排序：
function shallSort(array) {
 varincrement = array.length;
 var i
 var temp; //暂存
 var count = 0;
 do {
   //设置增量
  increment = Math.floor(increment / 3) + 1;
  for (i = increment ; i < array.length; i++) {
   console.log(increment);
    if (array[i] < array[i - increment]) {
      temp = array[i];
      for (var j = i - increment; j >= 0 && temp < array[j]; j-= increment) {
          array[j + increment] = array[j];
      }
      array[j + increment] = temp;
    }
   }
 }
 while (increment > 1)
 return array;
}

12. 堆排序：
Array.method('createHeap', function(low,high){
   var i=low, j=2*i, tmp=this[i];
   while(j<=high){
       if(j< high && this[j]<this[j+1]) j++; //从左右子节点中选出较大的节点
       if(tmp < this[j]){    //根节点(tmp)<较大的节点
           this[i] = this[j];
           i = j;
           j = 2*i;
       }else break;
    }
   this[i] = tmp;    //被筛选的元素放在最终的位置上
   return this;
});
Array.method('heapSort', function(){
   var i, tmp, len=this.length-1;
   for(i=parseInt(len/2); i>=1; i--) this.createHeap(i, len);
   for(i=len; i>=2; i--){
       tmp = this[1];
       this[1] = this[i];
       this[i] = tmp;
       this.createHeap(1, i-1);
    }
   return this;
});

13. 归并排序：
function mergeSort(items) {
   if (items.length < 2) {
       return items;
    }
   var middle = Math.floor(items.length / 2),
       left = items.slice(0, middle),
       right = items.slice(middle),
       params = merge(mergeSort(left), mergeSort(right));
   params.unshift(0, items.length);
   items.splice.apply(items, params);
   return items;
   function merge(left, right) {
       var result = [], il = 0, ir = 0;
       while (il < left.length && ir < right.length) {
            if (left[il] < right[ir]) {
                result.push(left[il++]);
           } else {
                result.push(right[ir++]);
           }
       }
       return result.concat(left.slice(il)) .concat(right.slice(ir));
    }
}

14. javascript实现二分查找
function binarySearch(data, dest){ 
   var h = data.length - 1, 
       l = 0; 
   while(l <= h){ 
       var m = Math.floor((h + l) / 2); 
       if(data[m] == dest){ 
           return m; 
       } 
       if(dest > data[m]){ 
            l = m + 1; 
       }else{ 
           h = m - 1; 
       } 
   }     
   return false; 
} 

15. XML 和 JSON 有过了解吧？能说一下分别介绍一下他们吗？ JSON 有什么优势？
XML：扩展标记语言 (ExtensibleMarkup Language, XML) ，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 XML使用DTD(document type definition)文档类型定义来组织数据;格式统一，跨平台和语言，早已成为业界公认的标准。
XML是标准通用标记语言 (SGML) 的子集，非常适合 Web 传输。XML 提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。
1）应用广泛，可扩展性强，被广泛应用各种场合
2）读取、解析没有JSON快
3）可读性强，可描述复杂结构
4）XML文件庞大，文件格式复杂，传输占带宽
JSON：(JavaScript ObjectNotation) 是一种轻量级的数据交换格式。易于人阅读和编写。同时也易于机器解析和生成。 JSON采用完全独立于语言的文本格式。
JSON建构于两种结构：“名称/值”对
1）结构简单，都是键值对。
2）读取、解析速度快，很多语言支持
3）数据格式比较简单，易于读写，格式都是压缩的，占用带宽小；
4）描述复杂结构能力较弱
XML: <Student>张三</Student>  转为JSON:  { "Student": "张三" }
16. 斐波那契数
function fib(n){ 
   if(n==1||n==2){ 
       return 1; 
   } 
   return arguments.callee (n-1)+ arguments.callee (n-2); 
} 

17. String(), toString()的区别
数值，布尔值，对象和字符串值都有toString()方法，但是null和undefined没有这个方法。
一般情况下没有参数，但是可以传递一个参数，输出数值的基数。
String()可以将任何类型的值转化为字符串，如果值有toString()的方法，则调用该方法并返回相应的结果。如果值是null，则返回null。如果值是undefined，则返回undefined。


18. 左边固定，右边自适应

1）#left{
 float:left;
 width:200px;
 background-color:blue;
  }
 #right{
 overflow:hidden;
 background-color:gray;
  }
2）#left{
 float:left;
 width:200px;
 background-color:blue;
  }
 #right{
 margin-left:200px;
 background-color:red;
  }
3）#left{
 position:absolute;
 top:0px;
 left:0px;
 width:200px;
 background-color:blue;
  }
 #right{
 margin-left:200px;
 background-color:red;
  }
4）#left{
 position:absolute;
  top:0px;
 left:0px;
 width:200px;
 background-color:blue;
  }
 #right{
 position:absolute;
 top:0px;
 left:200px;
 width:100%;
 background-color:gray;
  }
右边固定，左边自适应：
1）#left{
 float:left;
 width:100%;
 background-color:blue;
 margin-left:-200px;
 }
 #right{
 float:right;
 width:200px;
 background-color:gray;
}

 希望获取到页面中所有的checkbox怎么做？
var domList = document.getElementsByTagName(‘input’)
var checkBoxList = [];
var len = domList.length;　　//缓存到局部变量
while (len--) {　　//使用while的效率会比for循环更高
　　if (domList[len].type == ‘checkbox’) {
    　　checkBoxList.push(domList[len]);
　　}   } 

跨浏览器的事件绑定和解绑程序
addHandler:function(element,type,handler){
 if(element.addEventListener){   //removeEventListener
    element.addEventListener(type,handler,false);
}elseif(element.attachEvent){    //detachEvent
    element.attachEvent(“on”+type,handler);
}else{
  element[“on”+type]=handler;      //element[“on”+type]=null;
}
}


一、一个页面上两个div左右铺满整个浏览器，要保证左边的div一直为100px，右边的div跟随浏览器大小变化（比如浏览器为500，右边div为400，浏览器为900，右边div为800），请写出大概的css代码。

1.使用flex

//html
<div class='box'><div class='left'></div> <div class='right'></div></div>

//css
.box {
    width: 400px;
    height: 100px;
    display: flex;
    flex-direction: row;
    align-items: center;
    border: 1px solid #c3c3c3;
}
.left {
    flex-basis：100px;
    -webkit-flex-basis: 100px;
    /* Safari 6.1+ */
    background-color: red;
    height: 100%;
}
.right {
    background-color: blue;
    flex-grow: 1;
}

2.浮动布局

<div id="left">Left sidebar</div>
<div id="content">Main Content</div>

<style type="text/css">
* {
    margin: 0;
    padding: 0;
}
#left {
    float: left;
    width: 220px;
    background-color: green;
}
#content {
    background-color: orange;
    margin-left: 220px;
    /*==等于左边栏宽度==*/
}
</style>

二、请写出一些前端性能优化的方式，越多越好

1.减少dom操作
2.部署前，图片压缩，代码压缩
3.优化js代码结构，减少冗余代码
4.减少http请求，合理设置 HTTP缓存
5.使用内容分发cdn加速
6.静态资源缓存
7.图片延迟加载

三、一个页面从输入 URL 到页面加载显示完成，这个过程中都发生了什么？（流程说的越详细越好）

输入地址
1.浏览器查找域名的 IP 地址
2.这一步包括 DNS 具体的查找过程，包括：浏览器缓存->系统缓存->路由器缓存…
3.浏览器向 web 服务器发送一个 HTTP 请求
4.服务器的永久重定向响应（从 http://example.com 到 http://www.example.com）
5.浏览器跟踪重定向地址
6.服务器处理请求
7.服务器返回一个 HTTP 响应
8.浏览器显示 HTML
9.浏览器发送请求获取嵌入在 HTML 中的资源（如图片、音频、视频、CSS、JS等等）
10.浏览器发送异步请求

四、请大概描述下页面访问cookie的限制条件

  1. 跨域问题
  2. 设置了HttpOnly

五、描述浏览器重绘和回流，哪些方法能够改善由于dom操作产生的回流

1.直接改变className，如果动态改变样式，则使用cssText

// 不好的写法
var left = 1;
var top = 1;
el.style.left = left + "px";
el.style.top = top + "px"; // 比较好的写法
el.className += " className1";
// 比较好的写法
el.style.cssText += ";
left: " + left + "px;
top: " + top + "px;";

2.让要操作的元素进行”离线处理”，处理完后一起更新

a) 使用DocumentFragment进行缓存操作,引发一次回流和重绘；
b) 使用display:none技术，只引发两次回流和重绘；
c) 使用cloneNode(true or false) 和 replaceChild 技术，引发一次回流和重绘

六、vue生命周期钩子

1.beforcreate
2.created
3.beformount
4.mounted
5.beforeUpdate
6.updated
7.actived
8.deatived
9.beforeDestroy
10.destroyed

七、js跨域请求的方式，能写几种是几种

1、通过jsonp跨域
2、通过修改document.domain来跨子域
3、使用window.name来进行跨域
4、使用HTML5中新引进的window.postMessage方法来跨域传送数据（ie 67 不支持）
5、CORS 需要服务器设置header ：Access-Control-Allow-Origin。
6、nginx反向代理 这个方法一般很少有人提及，但是他可以不用目标服务器配合，不过需要你搭建一个中转nginx服务器，用于转发请求

八、对前端工程化的理解

  ● 开发规范
  ● 模块化开发
  ● 组件化开发
  ● 组件仓库
  ● 性能优化
  ● 项目部署
  ● 开发流程
  ● 开发工具

九, js深度复制的方式

1.使用jq的$.extend(true, target, obj)
2.newobj = Object.create(sourceObj)，// 但是这个是有个问题就是 newobj的更改不会影响到 sourceobj但是 sourceobj的更改会影响到newObj
3.newobj = JSON.parse(JSON.stringify(sourceObj))

十、js设计模式

总体来说设计模式分为三大类：

  ● 创建型模式，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。
  ● 结构型模式，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。
  ● 行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模

十一、图片预览

<input type="file" name="file" onchange="showPreview(this)" />
<img id="portrait" src="" width="70" height="75">

function showPreview(source) {
  var file = source.files[0];
  if(window.FileReader) {
      var fr = new FileReader();
      fr.onloadend = function(e) {
        document.getElementById("portrait").src = e.target.result;
      };
      fr.readAsDataURL(file);
  }
}

十二、扁平化多维数组

1、老方法

var result = []
function unfold(arr){
     for(var i=0;i< arr.length;i++){
      if(typeof arr[i]=="object" && arr[i].length>1) {
       unfold(arr[i]);
     } else {        
       result.push(arr[i]);
     }
  }
}
var arr = [1,3,4,5,[6,[0,1,5],9],[2,5,[1,5]],[5]];
unfold(arr)

2、使用tostring

var c=[1,3,4,5,[6,[0,1,5],9],[2,5,[1,5]],[5]];
var b = c.toString().split(',')

3、使用es6的reduce函数

var arr=[1,3,4,5,[6,[0,1,5],9],[2,5,[1,5]],[5]];
const flatten = arr => arr.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b) : b), []);
var result = flatten(arr)

十三、iframe有那些缺点？

  ● iframe会阻塞主页面的Onload事件；
  ● 搜索引擎的检索程序无法解读这种页面，不利于SEO;
  ● iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。
  ● 使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题。


4.简述几个css hack?
（1）图片间隙
在div 中插入图片，图片会将div 下方撑大3px。hack1：将<div>与<img>写在同一行。hack2：
给<img>添加display：block；
dt li 中的图片间隙。hack：给<img>添加display：block；
（2）默认高度，IE6 以下版本中，部分块元素，拥有默认高度（低于18px）
hack1：给元素添加：font-size：0；
hack2：声明：overflow：hidden；
表单行高不一致
hack1：给表单添加声明：float：left；height： ；border： 0；
鼠标指针
hack：若统一某一元素鼠标指针为手型：cursor：pointer；
当li 内的a 转化块元素时，给a 设置float，IE 里面会出现阶梯状
hack1：给a 加display：inline-block；
hack2：给li 加float：left；
8.transform？animation？区别?animation-duration
Transform:它和width、left 一样，定义了元素很多静态样式实现变形、旋转、缩放、
移位及透视等功能，通过一系列功能的组合我们可以实现很炫酷的静态效果（非动画)。
Animation:作用于元素本身而不是样式属性,属于关键帧动画的范畴，它本身被用来替代
一些纯粹表现的javascript 代码而实现动画,可以通过keyframe 显式控制当前帧的属性值.
animation-duration：规定完成动画所花费的时间，以秒或毫秒计。

13.弹性盒子模型?flex|box 区别?
答：（1）引入弹性盒布局模型的目的是提供一种更加有效的方式来对一个容器中的条目进
行排列、对齐和分配空白空间。即便容器中条目的尺寸未知或是动态变化的，弹性盒布局模
型也能正常的工作。在该布局模型中，容器会根据布局的需要，调整其中包含的条目的尺寸
和顺序来最好地填充所有可用的空间。当容器的尺寸由于屏幕大小或窗口尺寸发生变化时，
其中包含的条目也会被动态地调整。比如当容器尺寸变大时，其中包含的条目会被拉伸以占
满多余的空白空间；当容器尺寸变小时，条目会被缩小以防止超出容器的范围。弹性盒布局
是与方向无关的。在传统的布局方式中，block 布局是把块在垂直方向从上到下依次排列的；
而inline 布局则是在水平方向来排列。弹性盒布局并没有这样内在的方向限制，可以由开
发人员自由操作。
（2）flex 和box 的区别:
display：box 是老规范，要兼顾古董机子就加上它；
父级元素有display:box;属性之后。他的子元素里面加上box-flex 属性。可以让子元素按照
父元素的宽度进行一定比例的分占空间。
flex 是最新的，董机老机子不支持的；
父元素设置display:flex 后，子元素宽度会随父元素宽度的改变而改变，而display:box 不会。
Android UC 浏览器只支持display: box 语法；而iOS UC 浏览器则支持两种方式。
14.viewport 所有属性？
答： (1)width :设置layout viewport 的宽度，为一个正整数，或字符串'device-width';
(2)initial-scale:设置页面的初始缩放值，为一个数字，可以带小数。
(3)minimum-scale:允许用户的最小缩放值，为一个数字，可以带小数。
(4)maximum-scale:允许用户的最大缩放值，为一个数字，可以带小数。
(5)height:设置layout viewport 的高度，这个属性对我们并不重要，很少使用
(6)user-scalable:是否允许用户进行缩放，值为‘no’或者‘yes’。
安卓中还支持：target-densitydpi，表示目标设备的密度等级，作用是决定css 中的1px
代表多少物理像素
(7)target-densitydpi:值可以为一个数值或者high-dpi 、medium-dpi、low-dpi、
device-dpi 这几个字符串中的一个
15. 如何理解HTML 结构的语义化？
所谓标签语义化，就是指标签的含义。语义化的主要目的就是让大家直观的认识标签
(markup)和属性(attribute)的用途和作用，对搜索引擎友好，有了良好的结构和语义我们的
网页内容便自然容易被搜索引擎抓取，这种符合搜索引擎收索规则的做法，网站的推广便可
以省下不少的功夫，而且可维护性更高，因为结构清晰,十分易于阅读。这也是搜索引擎优
化SEO 重要的一步。
16. 伪类选择器和伪元素？c3 中引入的伪类选择器有？c3 中伪元素有?
伪类用一个冒号来表示，而伪元素则用两个冒号来表示。
伪类选择器：
由于状态是动态变化的，所以一个元素达到一个特定状态时，它可能得到一个伪类的样式；
当状态改变时，它又会失去这个样式。
伪元素选择器：
并不是针对真正的元素使用的选择器，而是针对CSS 中已经定义好的伪元素使用的选择器；
c3 中引入的伪类选择器：
:root()选择器，根选择器，匹配元素E 所在文档的根元素。在HTML 文档中，根元素始终
是<html>。:root 选择器等同于<html>元素。
:not()选择器称为否定选择器，和jQuery 中的:not 选择器一模一样，可以选择除某个元素之
外的所有元素。
:empty()选择器表示的就是空。用来选择没有任何内容的元素，这里没有内容指的是一点内
容都没有，哪怕是一个空格。
:target()选择器来对页面某个target 元素(该元素的id 被当做页面中的超链接来使用)指定样
式，该样式只在用户点击了页面中的超链接，并且跳转到target 元素后起作用。
:first-child()选择器表示的是选择父元素的第一个子元素的元素E。简单点理解就是选择元素
中的第一个子元素，记住是子元素，而不是后代元素。
:nth-child()选择某个元素的一个或多个特定的子元素。
:nth-last-child()从某父元素的最后一个子元素开始计算，来选择特定的元素
:nth-of-type(n)选择器和:nth-child(n)选择器非常类似，不同的是它只计算父元素中指定的某
种类型的子元素。
:only-child 表示的是一个元素是它的父元素的唯一一个子元素。
:first-line 为某个元素的第一行文字使用样式。
:first-letter 为某个元素中的文字的首字母或第一个字使用样式。
:before 在某个元素之前插入一些内容。
:after 在某个元素之后插入一些内容。
c3 中伪元素：
::first-line 选择元素的第一行，比如说改变每个段落的第一行文本的样式
::before 和::after 这两个主要用来给元素的前面或后面插入内容，这两个常用"content"配合
使用，见过最多的就是清除浮动
::selection 用来改变浏览网页选中文的默认效果

23.media 属性？screen？All？max-width?min-width?
答：media 属性规定被链接文档将显示在什么设备上。media 属性用于为不同的媒介类型
规定不同的样式。Screen 计算机默认屏幕，all 适用于所有设备，max-width 超过最大宽度
就不执行，min-width 必须大于最小宽度才执行。
24.meta 标签的name 属性值？
答：name 属性主要用于描述网页，与之对应的属性值为content，content 中的内容主要是
便于搜索引擎机器人查找信息和分类信息用的。meta 标签的name
属性语法格式是：＜meta name="参数" content="
具体的参数值"＞。其中name 属性主要有以下几种参数：A 、Keywords(关键字)说明：
keywords 用来告诉搜索引擎你网页的关键字是什么。B 、description(网站内容描述) 说明：
description 用来告诉搜索引擎你的网站主要内容.
C robots(机器人向导)说明：
robots 用来告诉搜索机器人哪些页面需要索引，哪些页面不需要索引。content 的参数有
all,none,index,noindex,follow,nofollow
。默认是all。举例：＜meta name="robots" content="none"＞D 、author(作者)
25.一般做手机页面切图有几种方式?
答：三种。响应式布局，弹性布局display：flex，利用js 编写设定比例，给根元素设定像
素，使用rem 为单位。
px/em/rem 有什么区别？ 为什么通常给font-size 设置的字体为62.5%
答：px 像素是相对长度单位。像素px 是相对于显示器屏幕分辨率而言的。em 是相对长度
单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则
相对于浏览器的默认字体尺寸。1、em 的值并不是固定的；2、em 会继承父级元素的字体
大小。使用rem 为元素设定字体大小时，仍然是相对大小，但相对的只是HTML 根元素。
这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比
例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。
rem 是相对于浏览器进行缩放的。1rem 默认是16px，在响应式布局中，一个个除来转换成
rem，太麻烦，所以重置rem
body{font-size=62.5% } 此时1rem = 10px;若是12px 则是1.2rem.
26.sass 和scss 有什么区别?sass 一般怎么样编译的
答：文件扩展名不同，Sass 是以“.sass”后缀为扩展名，而SCSS 是以“.scss”后缀为扩展
名；语法书写方式不同，Sass 是以严格的缩进式语法规则来书写，不带大括号({})和分号(;)，
而SCSS 的语法书写和我们的CSS 语法书写方式非常类似。
27.如果对css 进行优化如何处理？
答：压缩打包，图片整合，避免使用Hack，解决兼容问题，使用简写，让CSS 能保证日
后的维护。

35.手机端上图片长时间点击会选中图片，如何处理?
onselect=function() {
return false
}
36.video 标签的几个个属性方法
src：视频的URL poster：视频封面，没有播放时显示的图片preload：预加载autoplay：
自动播放loop：循环播放controls：浏览器自带的控制条width：视频宽度height：
视频高度
37.常见的视频编码格式有几种?视频格式有几种?
视频格式：MPEG-1、MPEG-2 和MPEG4 、AVI 、RM、ASF 和WMV 格式
视频编码格式：H.264、MPEG-4、MPEG-2、WMA-HD 以及VC-1
38.canvas 在标签上设置宽高和在style 中设置宽高有什么区别？
canvas 标签的width 和height 是画布实际宽度和高度，绘制的图形都是在这个上面。而style
的width 和height 是canvas 在浏览器中被渲染的高度和宽度。如果canvas 的width 和height
没指定或值不正确，就被设置成默认值、

42.animation 对应的属性
写法：animation: name duration timing-function delay iteration-count direction;
下面是对应的属性的介绍
animation-name 规定需要绑定到选择器的keyframe 名称。。
animation-duration 规定完成动画所花费的时间，以秒或毫秒计。
animation-timing-function 规定动画的速度曲线。
animation-delay 规定在动画开始之前的延迟。
animation-iteration-count 规定动画应该播放的次数。
animation-direction 规定是否应该轮流反向播放动画。
43.transition?
css 的transition 允许css 的属性值在一定的时间区间内平滑地过渡。这种效果可以在鼠
标单击、获得焦点、被点击或对元素任何改变中触发，并圆滑地以动画效果改变CSS 的属
性值
注意区别transform，transform 是进行2D 转换的如移动，比例化，反过来，旋转，和拉
伸元素。

2.声明函数作用提升?声明变量和声明函数的提升有什么区别?
（1）变量声明提升：变量申明在进入执行上下文就完成了。
只要变量在代码中进行了声明，无论它在哪个位置上进行声明， js 引擎都会将它的声
明放在范围作用域的顶部；
（2）函数声明提升：执行代码之前会先读取函数声明，意味着可以把函数申明放在调用它
的语句后面。
只要函数在代码中进行了声明，无论它在哪个位置上进行声明， js 引擎都会将它的声明
放在范围作用域的顶部；
（3）变量or 函数声明：函数声明会覆盖变量声明，但不会覆盖变量赋值。
同一个名称标识a，即有变量声明var a，又有函数声明function a() {}，不管二者声明
的顺序，函数声明会覆盖变量声明，也就是说，此时a 的值是声明的函数function a() {}。
注意：如果在变量声明的同时初始化a，或是之后对a 进行赋值，此时a 的值变量的值。eg: var
a; var c = 1; a = 1; function a() { return true; } console.log(a);

9.回调函数?
回调函数就是一个通过函数指针调用的函数。如果你把函数的指针（地址）作为参数传
递给另一个函数，当这个指针被用来调用其所指向的函数时，我们就说这是回调函数。回调
函数不是由该函数的实现方直接调用，而是在特定的事件或条件发生时由另外的一方调用
的，用于对该事件或条件进行响应。

10、合并连个数组
arr1.concat(arr2)
Array.prototype.push.aplly(arr1,arr2)
循环

22. var a = true;
typeof a++ //number
typeof true++ //错误！
typeof a+1 //number1
typeof (true)+1 //boolean1

25.如何通过原生js 判断一个元素当前是显示还是隐藏状态?
if( document.getElementById("div").css("display")==='none')
if( document.getElementById("div").css("display")==='block')
$("#div").is(":hidden"); // 判断是否隐藏
$("#div").is(":visible")

29.jq 中如何将一个jq 对象转化为dom 对象？
方法一：
jQuery 对象是一个数据对象，可以通过[index]的方法，来得到相应的DOM 对象。
如：var $v =$("#v") ; //jQuery 对象
var v=$v[0]; //DOM 对象
alert(v.checked) //检测这个checkbox 是否被选中
方法二：
jQuery 本身提供，通过.get(index)方法，得到相应的DOM 对象
如：var $v=$("#v"); //jQuery 对象
var v=$v.get(0); //DOM 对象
alert(v.checked) //检测这个checkbox 是否被选中

31.jq 中怎么样编写插件?
第一种是类级别的插件开发：
使用$.extend
jQuery.extend({
foo: function() {
alert('This is a test. This is only a test.');
},
bar: function(param) {
alert('This function takes a parameter, which is "' + param +'".');
}
}
也可以使用命名空间：
jQuery.myPlugin = {
foo:function() {
alert('This is a test. This is only a test.');
},
bar:function(param) {
alert('This function takes a parameter, which is "' + param + '".');
}
};
采用命名空间的函数仍然是全局函数，调用时采用的方法：
$.myPlugin.foo();
$.myPlugin.bar('baz');

第二种是对象级别的插件开发：
形式1：
(function($){
$.fn.extend({
pluginName:function(opt,callback){
// Our plugin implementation code goes here.
}
})
})(jQuery);
形式2：
(function($) {
$.fn.pluginName = function() {
// Our plugin implementation code goes here.
};
})(jQuery);
形参是$，函数定义完成之后,把jQuery 这个实参传递进去.立即调用执行。这样的好处是,
我们在写jQuery 插件时,也可以使用$这个别名,而不会与prototype 引起冲突。

33.$.map 和$.each 有什么区别
map()方法主要用来遍历操作数组和对象，会返回一个新的数组。$.map()方法适用于将数
组或对象每个项目新阵列映射到一个新数组的函数；
each()主要用于遍历jquery 对象，返回的是原来的数组，并不会新创建一个数组。

34、JSON和JS对象的对比
对比内容JSONJS对象键名必须是加双引号可允许不加、加单引号、加双引号属性值只能是数值（10进制）、字符串（双引号）、布尔值和null，
也可以是数组或者符合JSON要求的对象，
不能是函数、NaN, Infinity, -Infinity和undefined爱啥啥逗号问题最后一个属性后面不能有逗号可以数值前导0不能用，小数点后必须有数字没限制
var obj1 = {}; // 这只是 JS 对象
// 可把这个称做：JSON 格式的 JavaScript 对象 
var obj2 = {"width":100,"height":200,"name":"rose"};
// 可把这个称做：JSON 格式的字符串
var str1 = '{"width":100,"height":200,"name":"rose"}';
// 这个可叫 JSON 格式的数组，是 JSON 的稍复杂一点的形式
var arr = [
    {"width":100,"height":200,"name":"rose"},
    {"width":100,"height":200,"name":"rose"},
];      
// 这个可叫稍复杂一点的 JSON 格式的字符串     
var str2='['+
    '{"width":100,"height":200,"name":"rose"},'+
    '{"width":100,"height":200,"name":"rose"},'+
']';
另外，除了常见的“正常的”JSON格式，要么表现为一个对象形式{...}，要么表现为一个数组形式[...]，任何单独的一个10进制数值、双引号字符串、布尔值和null都是有效符合JSON格式的。
JSON不是JS的子集：
var code = '"\u2028\u2029"';
JSON.parse(code); // works fine
eval(code); // fails
JSON.parse可以正常解析，但是当做js解析时会报错。
将JS数据结构转化为JSON字符串 —— JSON.stringify
JSON.stringify(value[, replacer [, space]])
基本使用 —— 仅需一个参数
这个大家都会使用，传入一个JSON格式的JS对象或者数组，JSON.stringify({"name":"Good Man","age":18})返回一个字符串"{"name":"Good Man","age":18}"。
第二个参数可以是函数，也可以是一个数组
  ● 如果第二个参数是一个函数，那么序列化过程中的每个属性都会被这个函数转化和处理
  ● 如果第二个参数是一个数组，那么只有包含在这个数组中的属性才会被序列化到最终的JSON字符串中
  ● 如果第二个参数是null，那作用上和空着没啥区别，但是不想设置第二个参数，只是想设置第三个参数的时候，就可以设置第二个参数为null
这第二个参数若是函数
var friend={
    "firstName": "Good",
    "lastName": "Man",
    "phone":"1234567",
    "age":18
};
var friendAfter=JSON.stringify(friend,function(key,value){
    if(key==="phone")
        return "(000)"+value;
    else if(typeof value === "number")
        return value + 10;
    else
        return value; //如果你把这个else分句删除，那么结果会是undefined
});
console.log(friendAfter);
//输出：{"firstName":"Good","lastName":"Man","phone":"(000)1234567","age":28}
如果制定了第二个参数是函数，那么这个函数必须对每一项都有返回，这个函数接受两个参数，一个键名，一个是属性值，函数必须针对每一个原来的属性值都要有新属性值的返回。
那么问题来了，如果传入的不是键值对的对象形式，而是方括号的数组形式呢？，比如上面的friend变成这样：friend=["Jack","Rose"]，那么这个逐属性处理的函数接收到的key和value又是什么？如果是数组形式，那么key是索引，而value是这个数组项，你可以在控制台在这个函数内部打印出来这个key和value验证，记得要返回value，不然会出错。
这第二个参数若是数组
var friend={
    "firstName": "Good",
    "lastName": "Man",
    "phone":"1234567",
    "age":18
};
//注意下面的数组有一个值并不是上面对象的任何一个属性名
var friendAfter=JSON.stringify(friend,["firstName","address","phone"]);
console.log(friendAfter);
//{"firstName":"Good","phone":"1234567"}
//指定的“address”由于没有在原来的对象中找到而被忽略
如果第二个参数是一个数组，那么只有在数组中出现的属性才会被序列化进结果字符串，只要在这个提供的数组中找不到的属性就不会被包含进去，而这个数组中存在但是源JS对象中不存在的属性会被忽略，不会报错。
第三个参数用于美化输出 —— 不建议用

注意：这个函数的“小聪明”（重要）
1）键名不是双引号的（包括没有引号或者是单引号），会自动变成双引号；字符串是单引号的，会自动变成双引号
2）最后一个属性后面有逗号的，会被自动去掉
3）非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中，也就是对非数组对象在最终字符串中不保证属性顺序和原来一致
4）布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值，也就是你的什么new String("bala")会变成"bala"，new Number(2017)会变成2017
5）undefined、任意的函数（其实有个函数会发生神奇的事，后面会说）以及 symbol 值（symbol详见ES6对symbol的介绍）
  ● 出现在非数组对象的属性值中：在序列化过程中会被忽略
    JSON.stringify({x: undefined, y: function(){return 1;}, z: Symbol("")});
	 /出现在非数组对象的属性值中被忽略："{}"
  ● 出现在数组中时：被转换成 null
    JSON.stringify([undefined, Object, Symbol("")]);
    //出现在数组对象的属性值中，变成null："[null,null,null]"
6）NaN、Infinity和-Infinity，不论在数组还是非数组的对象中，都被转化为null
7）所有以 symbol 为属性键的属性都会被完全忽略掉，即便 replacer 参数中强制指定包含了它们
8）不可枚举的属性会被忽略
将JSON字符串解析为JS数据结构 —— JSON.parse
JSON.parse(text[, reviver])
如果第一个参数，即JSON字符串不是合法的字符串的话，那么这个函数会抛出错误，所以如果你在写一个后端返回JSON字符串的脚本，最好调用语言本身的JSON字符串相关序列化函数，而如果是自己去拼接实现的序列化字符串，那么就尤其要注意序列化后的字符串是否是合法的，合法指这个JSON字符串完全符合JSON要求的严格格式。
值得注意的是这里有一个可选的第二个参数，这个参数必须是一个函数，这个函数作用在属性已经被解析但是还没返回前，将属性处理后再返回。
var friend={
    "firstName": "Good",
    "phone":{"home":"1234567","work":["7654321","999000"]}
};
//我们先将其序列化
var friendAfter=JSON.stringify(friend);
//'{"firstName":"Good","phone":{"home":"1234567","work":["7654321","999000"]}}'
//再将其解析出来，在第二个参数的函数中打印出key和value
JSON.parse(friendAfter,function(k,v){
    console.log(k);
    console.log(v);
    console.log("----");
});
/*
firstName
Good
----
home
1234567
----
0
7654321
----
1
999000
----
work
[]
----
phone
Object
----

Object
----
*/
仔细看一下这些输出，可以发现这个遍历是由内而外的，可能由内而外这个词大家会误解，最里层是内部数组里的两个值啊，但是输出是从第一个属性开始的，怎么就是由内而外的呢？
这个由内而外指的是对于复合属性来说的，通俗地讲，遍历的时候，从头到尾进行遍历，如果是简单属性值（数值、字符串、布尔值和null），那么直接遍历完成，如果是遇到属性值是对象或者数组形式的，那么暂停，先遍历这个子JSON，而遍历的原则也是一样的，等这个复合属性遍历完成，那么再完成对这个属性的遍历返回。
本质上，这就是一个深度优先的遍历。
有两点需要注意：
1）如果 reviver 返回 undefined，则当前属性会从所属对象中删除，如果返回了其他值，则返回的值会成为当前属性新的属性值。
2）你可以注意到上面例子最后一组输出看上去没有key，其实这个key是一个空字符串，而最后的object是最后解析完成对象，因为到了最上层，已经没有真正的属性了。
影响 JSON.stringify 的神奇函数 —— object.toJSON
如果你在一个JS对象上实现了toJSON方法，那么调用JSON.stringify去序列化这个JS对象时，JSON.stringify会把这个对象的toJSON方法返回的值作为参数去进行序列化。
var info={
    "msg":"I Love You",
    "toJSON":function(){
        var replaceMsg=new Object();
        replaceMsg["msg"]="Go Die";
        return replaceMsg;
    }
};
JSON.stringify(info);
//出si了，返回的是：'"{"msg":"Go Die"}"',说好的忽略函数呢
这个函数就是这样子的：其实Date类型可以直接传给JSON.stringify做参数，其中的道理就是，Date类型内置了toJSON方法。
小结以及关于兼容性的问题
JSON是一种语法上衍生于JS语言的一种轻量级的数据交换格式。不过遗憾的是，以上所用的3个函数，不兼容IE7以及IE7之前的浏览器。

问题1:JavaScript 中 undefined 和 not defined 的区别
JavaScript 未声明变量直接使用会抛出异常：var name is not defined，如果没有处理异常，代码就停止运行了。
但是，使用typeof undeclared_variable并不会产生异常，会直接返回 undefined。
var x; // 声明 x
console.log(x); //output: undefined 
console.log(typeof y); //output: undefined
console.log(z); // 抛出异常: ReferenceError: z is not defined

问题2:下面的代码输出什么？
var y = 1;
if (function f(){}) {
    y += typeof f;
}
console.log(y);
正确的答案应该是 1undefined。
JavaScript中if语句求值其实使用eval函数，eval(function f(){}) 返回 function f(){} 也就是 true。
下面我们可以把代码改造下，变成其等效代码。
var k = 1;
if (1) {
    eval(function foo(){});
    k += typeof foo;
}
console.log(k); 
上面的代码输出其实就是 1undefined。为什么那？我们查看下 eval() 说明文档即可获得答案。该方法只接受原始字符串作为参数，如果 string 参数不是原始字符串，那么该方法将不作任何改变地返回。恰恰function f(){} 语句的返回值是 undefined，所以一切都说通了。
注意上面代码和以下代码不同。
var k = 1;
if (1) {
    function foo(){};
    k += typeof foo;
}
console.log(k); // output 1function

问题3:在JavaScript中创建一个真正的private方法有什么缺点？
每一个对象都会创建一个private方法的方法，这样很耗费内存
观察下面代码
var Employee = function (name, company, salary) {
    this.name = name || "";       
    this.company = company || ""; 
    this.salary = salary || 5000; 
    // Private method
    var increaseSalary = function () {
        this.salary = this.salary + 1000;
    };
    // Public method
    this.dispalyIncreasedSalary = function() {
        increaseSlary();
        console.log(this.salary);
    };
};
// Create Employee class object
var emp1 = new Employee("John","Pluto",3000);
// Create Employee class object
var emp2 = new Employee("Merry","Pluto",2000);
// Create Employee class object
var emp3 = new Employee("Ren","Pluto",2500);
在这里 emp1,emp2,emp3都有一个increaseSalary私有方法的副本。
所以我们除非必要，非常不推荐使用私有方法。


问题5:写一个mul函数，使用方法如下。
console.log(mul(2)(3)(4)); // output : 24 
console.log(mul(4)(3)(4)); // output : 48
答案直接给出：
function mul (x) {
    return function (y) { // anonymous function 
        return function (z) { // anonymous function 
            return x * y * z; 
        };
    };
}
简单说明下： mul 返回一个匿名函数，运行这个匿名函数又返回一个匿名函数，最里面的匿名函数可以访问 x,y,z 进而算出乘积返回即可。
对于JavaScript中的函数一般可以考察如下知识点：
  ● 函数是一等公民
  ● 函数可以有属性，并且能连接到它的构造方法
  ● 函数可以像一个变量一样存在内存中
  ● 函数可以当做参数传给其他函数
  ● 函数可以返回其他函数

问题6:JavaScript怎么清空数组？
如var arrayList = ['a','b','c','d','e','f'];
怎么清空 arrayList
方法1：arrayList = [];直接改变arrayList所指向的对象，原对象并不改变。
方法2：arrayList.length = 0;这种方法通过设置length=0 使原数组清除元素。
方法3：arrayList.splice(0, arrayList.length)和方法2相似

问题7:怎么判断一个object是否是数组(array)？
方法1：使用 Object.prototype.toString 来判断是否是数组
function isArray(obj){
    return Object.prototype.toString.call( obj ) ==="[object Array]";
}
这里使用call来使 toString 中 this 指向 obj。进而完成判断
方法2：使用 原型链 来完成判断
function isArray(obj){
    return obj.__proto__ === Array.prototype;
}
基本思想是利用 实例如果是某个构造函数构造出来的那么 它的 __proto__是指向构造函数的 prototype属性。
方法3：利用JQuery
function isArray(obj){
    return $.isArray(obj)
}
JQuery isArray 的实现其实就是方法1

问题8:下面代码输出什么？
var output = (function(x){
    delete x;
    return x;
})(0);
console.log(output);
输出是 0。 delete 操作符是将object的属性删去的操作。但是这里的 x 是并不是对象的属性， delete 操作符并不能作用。

问题9:下面代码输出什么？
var x = 1;
var output = (function(){
    delete x;
    return x;
})();
console.log(output);
输出是 1。delete 操作符是将object的属性删去的操作。但是这里的 x 是并不是对象的属性， delete 操作符并不能作用。

问题10:下面代码输出什么？
var x = { foo : 1};
var output = (function(){
    delete x.foo;
    return x.foo;
})();
console.log(output);
输出是 undefined。x虽然是全局变量，但是它是一个object。delete作用在x.foo上，成功的将x.foo删去。所以返回undefined

问题11:下面代码输出什么？
var Employee = {
    company: 'xyz'
}
var emp1 = Object.create(Employee);
delete emp1.company
console.log(emp1.company);
输出是 xyz，这里的 emp1 通过 prototype 继承了 Employee的 company。emp1自己并没有company属性。所以delete操作符的作用是无效的。

问题12:什么是 undefined x 1 ？
在chrome下执行如下代码，我们就可以看到undefined x 1的身影。
var trees = ["redwood","bay","cedar","oak","maple"];
delete trees[3];
console.log(trees);
当我们使用 delete 操作符删除一个数组中的元素，这个元素的位置就会变成一个占位符。打印出来就是undefined x 1。
注意如果我们使用trees[3] === 'undefined × 1'返回的是 false。因为它仅仅是一种打印表示，并不是值变为undefined x 1。

问题13:下面代码输出什么？
var trees = ["xyz","xxxx","test","ryan","apple"];
delete trees[3];
console.log(trees.length);
输出是5。因为delete操作符并不是影响数组的长度。

问题14:下面代码输出什么？
var bar = true;
console.log(bar + 0);   
console.log(bar + "xyz");  
console.log(bar + true);  
console.log(bar + false);   
输出是
1
truexyz
2
1
下面给出一个加法操作表
  ● Number + Number -> 加法
  ● Boolean + Number -> 加法
  ● Boolean + Boolean -> 加法
  ● Number + String -> 连接
  ● String + Boolean -> 连接
  ● String + String -> 连接


问题15:下面代码输出什么？
var z = 1, y = z = typeof y;
console.log(y);  
输出是 undefined。js中赋值操作结合律是右至左的 ，即从最右边开始计算值赋值给左边的变量。
上面代码等价于
var z = 1
z = typeof y;
var y = z;
console.log(y);  

问题16:下面代码输出什么？
var foo = function bar(){ return 12; };
typeof bar();  
输出是抛出异常，bar is not defined。如果想让代码正常运行，需要这样修改代码：
var bar = function(){ return 12; };
typeof bar();  
或者是
function bar(){ return 12; };
typeof bar();  
明确说明这个下问题
var foo = function bar(){ 
    // foo is visible here 
    // bar is visible here
    console.log(typeof bar()); // Work here :)
};
// foo is visible here
// bar is undefined here

问题17:两种函数声明有什么区别
变量定义和函数定义都会提升，函数赋值只会在调用的时候才会执行。
var foo = function(){ 
    // Some code
}; 
function bar(){ 
    // Some code
}; 
foo的定义是在运行时。想系统说明这个问题，我们要引入变量提升的这一概念。
我们可以运行下如下代码看看结果。
console.log(foo)
console.log(bar)
var foo = function(){ 
    // Some code
}; 
function bar(){ 
    // Some code
}; 

输出为
undefined
function bar(){ 
    // Some code
}; 
为什么那？为什么 foo 打印出来是 undefined，而 bar打印出来却是函数？
JavaScript在执行时，会将变量提升。
所以上面代码JavaScript 引擎在实际执行时按这个顺序执行。
// foo bar的定义位置被提升
function bar(){ 
    // Some code
}; 
var foo;
console.log(foo)
console.log(bar)
foo = function(){ 
    // Some code
}; 



问题18:下面代码输出什么？
var salary = "1000$";
(function () {
    console.log("Original salary was " + salary);
    var salary = "5000$";
    console.log("My New Salary " + salary);
})();
输出是
Original salary was undefined
My New Salary 5000$
这题同样考察的是变量提升。等价于以下代码
var salary = "1000$";
 (function () {
     var salary ;
     console.log("Original salary was " + salary);
     salary = "5000$";
     console.log("My New Salary " + salary);
 })();

问题19:什么是 instanceof 操作符？下面代码输出什么？
function foo(){ 
  return foo; 
}
console.log(new foo() instanceof foo);
instanceof操作符用来判断是否当前对象是特定类的对象。
如
function Animal(){
    //或者不写return语句
    return this;
}
var dog = new Animal();
dog instanceof Animal // Output : true
但是，这里的foo定义为
function foo(){ 
  return foo; 
}
所以
// here bar is pointer to function foo(){return foo}.
var bar = new foo();

所以 new foo() instanceof foo 返回 false

问题20: 如果我们使用JavaScript的"关联数组"，我们怎么计算"关联数组"的长度？
var counterArray = {
    A : 3,
    B : 4
};
counterArray["C"] = 1;
其实答案很简单，直接计算key的数量就可以了。
Object.keys(counterArray).length // Output 3

