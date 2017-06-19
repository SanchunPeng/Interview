1、什么是Web Workers？为什么我们需要他们？
-------
                
循环代码在HTML按钮点击以后执行，这个方法执行是同步的，换句话说这个浏览器必须等到循环完成才能操作，这个会进一步导致浏览器冻结并且没有响应。     
            
如果你能移动这些繁重的循环到Javascript文件中，采用异步的方式运行，这意味着浏览器不需要等到循环接触，我们可以有更敏感的浏览器，这就是web worker的作用。Web worker帮助我们用异步执行Javascript文件。在 HTML5 的新规范中，实现了 Web Worker 来引入 JavaScript 的 “多线程” 技术，他的能力让我们可以在页面主运行的 JavaScript 线程中加载运行另外单独的一个或者多个 JavaScript 线程；当然 Web Worker 提供不像其他的多线程语言(Java、C++ 等)，主程序线程和 Worker 线程之间，Worker 线程之间，不会共享任何作用域或资源，他们间唯一的通信方式就是一个基于事件监听机制的 message；同时，这并不意味着     JavaScript 语言本身就支持了多线程，对于 JavaScript 语言本身它仍是运行在单线程上的， Web Worker 只是浏览器（宿主环境）提供的一个能力API。     

我们如何在JavaScript中创建一个worker线程？
------------
```javascript
var worker = new Worker("MyHeavyProcess.js");//文件名   
worker.postMessage();//使用“PostMessage”发送信息给worker对象     
worker.onmessage = function (e) {   
document.getElementById("txt1").value = e.data;   
};//当worker线程发送数据的时候，我们在调用结束的时候，通过”onMessage”事件获取
```

现规定：main.js 为页面运行的主要脚本文件，workder.js 为 Web Worker 脚本的文件                    
          
将繁重的循环放在“MyHeavyProcess.js”的文件中，当Javascript文件想发送信息，可以使用”postmessage”方法来给 Worker 线程传递信息，同时任何来自worker的信息都在“onmessage”事件中接收到。在main.js中，new Worker() 之后会返回一个实例对象，它包含一个 postMessage 方法，可以通过调用这个。    
```javascript
// main.js     
var worker = new Worker('./worker.js');     
worker.addEventListener('message', function (e) {// 监听消息事件   
console.log('MAIN: ', 'RECEIVE', e.data);     
});    
worker.onmessage = function () {// 或者可以使用 onMessage 来监听事件    
console.log('MAIN: ', 'RECEIVE', e.data);    
};    
worker.addEventListener('error', function (e) {// 监听 error 事件     
console.log('MAIN: ', 'ERROR', e);     
console.log('MAIN: ', 'ERROR', 'filename:' + e.filename + '---message:' + e.message + '---lineno:' + e.lineno); });    
worker.onerror = function () {// 或者可以使用 onMessage 来监听事件   
console.log('MAIN: ', 'ERROR', e);    
};     
worker.postMessage({ m: 'Hello Worker, I am main.js' });// 触发事件，传递信息给 Worker
worker.terminate();//中止Web Worker
```
在 Worker 的脚本中，我们可以调用全局函数 postMessage 和给全局的 onmessage 赋值来发送和监听数据和事件
```javascript
// worker.js    
console.log('WORKER TASK: ', 'running');     
onmessage = function (e) {//监听数据进行处理   
console.log('WORKER TASK: ', 'RECEIVE', e.data); 
postMessage('Hello, I am Worker');// 发送数据事件    
} 
addEventListener('message', function (e) {// 或者使用 addEventListener 来监听事件    
console.log('WORKER TASK: ', 'RECEIVE', e.data);   
postMessage('Hello, I am Worker');    
});   
```
event 这个事件对象中有几个比较重要的参数需要我们注意：      
  ● event.filename: 导致错误的 Worker 脚本的名称；   
  ● event.message: 错误的信息；   
  ● event.lineno: 出现错误的行号；   

Worker 的环境与作用域
--------
         
如前文所述，在 Worker 线程的运行环境中没有 window 全局对象，也无法访问 DOM 对象，所以一般来说他只能来执行纯 JavaScript 的计算操作。    
但是，他还是可以获取到部分浏览器提供的 API 的：       
  ● setTimeout()， clearTimeout()， setInterval()， clearInterval()：有了设计个函数，就可以在 Worker 线程中执行定时操作了；    
  ● XMLHttpRequest 对象：意味着我们可以在 Worker 线程中执行 ajax 请求； 
  ● navigator 对象：可以获取到 ppName，appVersion，platform，userAgent 等信息；
  ● location 对象（只读）：可以获取到有关当前 URL 的信息；

如何在 Worker 中加载外部脚本
--------
         
可以通过 Worker 环境中的全局函数 importScripts() 加载外部 js 脚本到当前 Worker 脚本中，它接收多个参数，参数都为加载脚本的链接字符串，比如：
importScripts('worker2.js', 'worker3.js'); importScripts('worker2.js'); importScripts('worker3.js');       

应用:
-----
      
Web Worker 的实现为前端程序带来了后台计算的能力，可以实现主 UI 线程与复杂计运算线程的分离，从而极大减轻了因计算量大而造成 UI 阻塞而出现的界面渲染卡、掉帧的情况，并且更大程度地利用了终端硬件的性能；   
同时把程序之间的任务更清晰、条理化；  
其主要应用有几个场景：   
  ● 对于图像、视频、音频的解析处理；  
  ● canvas 中的图像计算处理；  
  ● 大量的 ajax 请求或者网络服务轮询；   
  ● 大量数据的计算处理（排序、检索、过滤、分析…）；   

Web Worker线程的限制是什么？
-------
       
Web worker线程不能修改HTML元素，全局变量和Window.Location一类的窗口属性。你可以自由使用Javascript数据类型，XMLHttpRequest调用等。  

2、什么是WebSQL？
--------
WebSQL是一个在浏览器客户端的结构关系数据库，这是浏览器内的本地RDBMS(关系型数据库系统)，你可以使用SQL查询      
WebSql不是HTML5的一个规范吗，许多人把它标记为HTML5，但是他不是HTML5的规范的一部分，这个规范是基于SQLite的    
我们如何使用WebSQL：     
第一步我们需要做的是使用如下所示的“OpenDatabase”方法打开数据库，第一个参数是数据库的名字，接下来是版本，然后是简单原文标题，最后是数据库大小；     
```javascript
var db=openDatabase('dbCustomer','1.0','Customer app', 2 * 1024 * 1024);
```
为了执行SQL，我们需要使用“transaction”方法，并调用”executeSql”方法来使用SQL
```javascript
db.transaction(function (tx) 
{
tx.executeSql('CREATE TABLE IF NOT EXISTS tblCust(id unique, customername)');
tx.executeSql('INSERT INTO tblcust (id, customername) VALUES(1, "shiv")');
tx.executeSql('INSERT INTO tblcust (id, customername) VALUES (2, "raju")');
}
```
万一你使用“select”查你会得到数据”result”集合，我们可以通过循环展示到HTML的用户界面
```javascript
db.transaction(function (tx) {
   tx.executeSql('SELECT * FROM tblcust', [], function (tx, results) {
   for (i = 0; i < len; i++){
     msg = "<p><b>" + results.rows.item(i).log + "</b></p>";
     document.querySelector('#customer').innerHTML +=  msg;
}
}, null);
});
```

3、如何实现浏览器内多个标签页之间的通信? (阿里)
--------
1)WebSocket、SharedWorker；   
2)也可以调用localstorge、cookies等本地存储方式；   
localstorge另一个浏览上下文里被添加、修改或删除时，它都会触发storage事件，我们通过监听事件，控制它的值来进行页面信息通信；    
注意quirks：Safari 在无痕模式下设置localstorge值时会抛出 QuotaExceededError 的异常；     

4、WebSocket
-----
WebSocket ：WebSocket protocol 是HTML5一种新的协议。     
目标：在一个单独的持久连接上提供全双工，双向通信。webSocket是一种与服务器进行全双工、双向通信的信道。需要支持这种协议的专门服务器，HTTP服务器无法实现WebSocket，未加密的协议是ws://，加密的协议是wss://,使用自定义协议的好处是传递的数据包很小，适合移动应用。缺点是制定协议的时间比js API时间还要长，必须给websocket构造函数传入绝对URL，同源策略对于Web Sockets完全不适用，因此可以通过它打开到任何站点的连接。     

webSocket如何兼容低浏览器？(阿里)    
Adobe Flash Socket 、   
ActiveX HTMLFile (IE) 、   
基于 multipart 编码发送 XHR 、   
基于长轮询的 XHR（组合SSE和XHR也可以实现双向通信）    
引用WebSocket.js这个文件来兼容低版本浏览器    

5、客户端存储
--------
请你谈谈Cookie的弊端    
cookie虽然在持久保存客户端数据提供了方便，分担了服务器存储的负担，但还是有很多局限性的。 第一：每个特定的域名下最多生成20个cookie，在客户端使用document.cookie访问cookie    
1）IE6或更低版本最多20个cookie   
2）IE7和之后的版本最后可以有50个cookie    
3）Firefox最多50个cookie     
4）Opera限制每个域最多30个cookie   
5）chrome和Safari没有做硬性限制    
IE和Opera 会清理近期最少使用的cookie，Firefox会随机清理cookie。cookie的最大大约为4096字节4KB，为了兼容性，一般不能超过4095字节。IE 提供了一种存储可以持久化用户数据，叫做userData，从IE5.0就开始支持。每个数据最多128K，每个域名下最多1M。这个持久化数据放在缓存中，如果缓存没有清理，那么会一直存在。     

##### 优点：极高的扩展性和可用性      
1）通过良好的编程，控制保存在cookie中的session对象的大小。    
2）通过加密和安全传输技术（SSL），减少cookie被破解的可能性。   
3）只在cookie中存放不敏感数据，即使被盗也不会有重大损失。    
4）控制cookie的生命期，使之不会永远有效。偷盗者很可能拿到一个过期的cookie。    

##### 缺点：    
1）Cookie数量和长度的限制。每个domain最多只能有20条cookie，每个cookie长度不能超过4KB，否则会被截掉。      
2）安全性问题。如果cookie被人拦截了，那人就可以取得所有的session信息。即使加密也与事无补，因为拦截者并不需要知道cookie的意义，他只要原样转发cookie就可以达到目的了。    
3）有些状态不可能保存在客户端。例如，为了防止重复提交表单，我们需要在服务器端保存一个计数器。如果我们把这个计数器保存在客户端，那么它起不到任何作用。
所以为了绕开cookie个数限制，一些开发人员使用了子cookie，使用cookie存储多个名值对，用&连接。   

##### cookie的构造：       
cookie的名称不区分大小写，可以设置cookie域，路径，失效时间（没定义的话是会话结束失效），安全标志（SSL）e.g.Set-Cookie:name-value;domain=.wrox.com;path=/;secure，指定在.erox.com域或子域下的所有路径发送https请求时都会带上cookie。都会进行URL编码。    

##### web storage和cookie的区别：    
Web Storage的概念和cookie相似，区别是它是为了更大容量存储，在cookie之外存储会话数据的途径设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。除此之外，Web Storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。      

Web Storage定义了两种用于存储数据的对象，sessionStorage和localStorage，前者严格用于在一个浏览器会话中存储数据，浏览器关闭后立即删除，后者用户跨会话持久化数据并遵循跨域安全策略。要访问同一个localStorage对象，页面必须来自同一个域名（子域名无效），同一种协议，同一端口。    

##### 但是Cookie也是不可以或缺的：      
Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。浏览器的支持除了IE７及以下不支持外，其他标准浏览器都完全支持(ie及FF需在web服务器里运行)，值得一提的是IE总是办好事，例如IE7、IE6中的UserData（可以应用在页面的某个元素上，每个文档最多128KB，每个域名最多1M，在使用userData之前需要再DOM元素上使用style的behavior属性，通过setAttribute()保存数据，最后必须使用save(数据空间名称)指定数据空间名称，最后就可以使用load(数据空间名称)方法指定同样的数据空间名称来获取数据，每次添加或者删除后都要使用save进行提交更改，用户数据会跨越会话永久存在，所以需要使用removeAttribute删除释放，也不安全）其实就是javascript本地存储的解决方案。所以通过简单的代码封装可以统一到所有的浏览器都支持web storage。很容就能够兼容。    

在较高版本的浏览器（IE8+）中，js提供了sessionStorage和globalStorage。在HTML5中提供了localStorage来取代globalStorage。html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。    
localStorage和sessionStorage都具有相同的操作方法，例如（setItem、getItem）（也可以用点的方式存取）和removeItem,key(index)：获得index位置处的值的名字等，也可以通过Storage对象间接调用，因为localStorage，sessionStorage都是Storage对象的实例。     

##### sessionStorage：       
可以跨页面刷新存在，浏览器崩溃后重启依然可用（IE不行）只能由最初给对象存储数据的页面可以访问，所以对多页应用有限制。IE8可以通过这个对象将数据写入磁盘，设置数据之前使用sessionStorage.begin()。成功之后使用sessionStorage.commit()，主要用于仅针对会话的小段数据存储，如果需要跨越会话存储数据，用localStorage或globalStorage更合适。

##### globalStorage：      
跨越会话存储数据，globalStorage不是Storage实例，使用globalStorage["wrox.com"]确定针对特定域名的存储空间，globalStorage["wrox.com"]是Storage实例，也就是该域可以访问存储在上面的数据。同时也要遵从类似同源策略的规则设置和访问。没有使用removeItem()或者delete删除，或者用户没有清除浏览器缓存，globalStorage中存储的数据会一直保存在磁盘上，适合存储文档或用户偏好设置。     

##### localStorage：     
是Storage实例，该对象在HTML5规范中作为持久保存客户端的数据的方案取代了globalStorage。访问规则事先也设定，相当于globalStorage[location.host]。    
对localStorage，globalStorage，sessionStorage进行操作都会触发storage事件。    

##### 本地存储和事务存储之间的区别:    
本地存储数据持续永久，但是会话在浏览器打开时有效知道浏览器关闭时会话变量重置    

cookie，localStorage和sessionStorage有什么区别？
------
##### 共同点：都是保存在浏览器端，且同源的。<br/>
##### 区别：<br/>
1）cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。<br/>
2）cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。<br/>
3）存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。<br/>
4）数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。<br/>
5）作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。<br/>

6、HTML5的离线储存怎么使用，工作原理能不能解释一下？
--------
##### 离线检测：       
HTML5定义了一个navigator.onLine属性，ture表示能上网，由于浏览器兼容问题，配合使用online和offline事件，当网络从在线转为离线触发offline，转为在线触发online，都在window对象上触发。      

##### 离线储存：    
所谓的离线存储就是将一些资源文件保存在本地，这样后续的页面加载将使用本地的资源文件，在 离线的情况下可以继续访问web应用。在用户与因特网连接时，更新用户机器上的缓存文件。   
##### 原理：
HTML5的离线存储是基于一个新建的.appcache文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像cookie一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。    
```javascript
localStorage 长期存储数据，浏览器关闭后数据不丢失；   
localStorage.setItem("key","value");//数据添加到本地存储采用键值对,value也可以是一个js对象。     localStorage.setItem(“I001”,JSON.stringify(country));//也可以存储json格式的数据   
var item = localStorage.getItem("key");//从本地存储中检索数据  
```
本地存储没有生命周期，它将保留直到用户从浏览器清除或者使用Javascript代码移除。
sessionStorage 数据在浏览器关闭后自动删除。    
##### 如何使用：       
1）、页面头部像下面一样加入一个manifest的属性；将描述文件与页面关联起来\<html manifest="/offine.manifest"\>           
2）、在cache.manifest文件的编写离线存储的资源;现在推荐描述文件扩展名为appcache.           
```javscript
    CACHE MANIFEST
    #v0.11
    CACHE:
    js/app.js
    css/style.css
    NETWORK:
    resourse/logo.png
    FALLBACK:
   //offline.html
```
3）、在离线状态时，操作window.applicationCache进行需求实现。    

##### HTML5中的应用缓存
一个最需要的事最终是用户的离线浏览，换句话说，如果网络连接不可用时，页面应该来自浏览器缓存，离线应用缓存可以帮助你达到这个目的，应用缓存可以帮助你指定哪些文件需要缓存，哪些不需要。     
1）如何实现应用缓存：首先我们需要指定”manifest”文件，“manifest”文件帮助你定义你的缓存如何工作
```javascript
CACHE MANIFEST          //所有manifest文件都以“CACHE MANIFEST”语句开始.
# version 1.0     //#（散列标签）有助于提供缓存文件的版本.
CACHE :           //CACHE 命令指出哪些文件需要被缓存.Mainfest文件的内容类型应是“text/cache-manifest”.
Login.aspx
```
2）创建一个缓存manifest文件以后，接下来的事情实在HTML页面中提供mainfest连接，如下所示：       
\<html manifest="cache.aspx"\>         
当以上文件第一次运行，他会添加到浏览器应用缓存中，在服务器宕机时，页面从应用缓存中获取      
3）应用缓存通过变更“#”标签后的版本版本号而被移除       
4）应用缓存中的回退帮助你指定在服务器不可访问的时候，将会显示某文件。例如在下面的manifest文件中，我们说如果谁敲击了”/home”同时服务器不可到达的时候，”homeoffline.html”文件应送达.     
FALLBACK:       
/home/homeoffline.html     
5）NETWORK://不需要缓存的文件      
home.aspx      

##### 浏览器是怎么对HTML5的离线储存资源进行管理和加载    
在线的情况下，浏览器发现html头部有manifest属性，它会请求manifest文件，如果是第一次访问app，那么浏览器就会根据manifest文件的内容下载相应的资源并且进行离线存储。如果已经访问过app并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的manifest文件与旧的manifest文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。    
离线的情况下，浏览器就直接使用离线存储的资源。      
步骤：     
1）html5是使用一个manifest文件来标明那些文件是需要被存储，对于manifest文件，文件的mime-type必须是text/cache-manifest类型。       
2）cache manifest下直接写需要缓存的文件，这里指明文件被缓存到浏览器本地；在network下指明的文件，强制必须通过网络资源获取的；在failback下指明是一种失败的回调方案，比如无法访问，就发出404.htm请求。           

6、iframe有那些优缺点？ 
----------
##### 优点：     
1）解决加载缓慢的第三方内容如图标和广告等的加载问题    
2）Security sandbox    
3）并行加载脚本    

##### 缺点：       
1）在网页中使用框架结构最大的弊病是搜索引擎的检索程序无法解读这种页面，不利于SEO;     
2）iframe会阻塞主页面的Onload事件；     
3）iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。即使内容为空，加载也需要时间。     
4）框架结构有时会让人感到迷惑，页面很混乱，没有语义。
使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题。   

7、什么是闭包？闭包有什么好处？为什么要用它？使用闭包要注意什么？
----------
##### 闭包：
闭包是指有权访问另一个函数作用域中变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，通过另一个函数访问这个函数的局部变量,利用闭包可以突破作用链域，将函数内部的变量和参数传递到外部。该变量和参数不会被垃圾回收机制所回收<br/>
##### 好处 ：<br/>
1）希望一个变量长期驻扎在内存之中<br/>
2）避免全局变量的污染<br/>
3）私有成员的存在<br/>
注意：参数和变量不会被垃圾回收机制回收，使用闭包后变量就在内存中，所以也不宜使用太多的闭包。可能会造成内存泄漏<br/>

//li节点的onclick事件都能正确的弹出当前被点击的li索引
```html
 <ul id="testUL">
    <li> index = 0</li>
    <li> index = 1</li>
    <li> index = 2</li>
    <li> index = 3</li>
</ul>
```
```javascript
<script type="text/javascript">
    var nodes = document.getElementsByTagName("li");
    for(i = 0;i<nodes.length;i++){
        nodes[i].onclick = function(){
            console.log(i+1);//不用闭包的话，值每次都是4
        }(i);
    }
</script>
```
执行say667()后,say667()闭包内部变量会存在,而闭包内部函数的内部变量不会存在，使得Javascript的垃圾回收机制GC不会收回say667()所占用的资源，因为say667()的内部函数的执行需要依赖say667()中的变量<br/>
```javascript
function say667() {
    // Local variable that ends up within closure
    var num = 666;
    var sayAlert = function() {
        alert(num);
    }
    num++;
    return sayAlert;
}
var sayAlert = say667();
sayAlert()//执行结果应该弹出的667
```

##### 闭包的原理和应用
闭包就是能够读取其他函数内部变量的函数。<br/>
当回调发生时，闭包能记住它原来所在的执行上下文<br/>
它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。<br/>
1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。<br/>
2）闭包会在父函数外部，改变父函数内部变量的值。<br/>

##### 闭包的用途：
(1)匿名自执行函数<br/>
如果变量不加上var关键字，则会默认添加到全局对象属性上去，这样可能造成别的函数误用这些变量，造成全局对象过于庞大，影响访问速度。此外，也会有的函数只需执行一次，内部的变量无需维护
```javascript
var datamodel={
  table:[],
  tree:{}
};
(function(dm){    
})(datamodel);
```
创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。<br/>
(2)有一个很耗时的函数对象，每次调用都会花费很长时间，就需要把计算的值存储起来，当调用的时候，首先在缓存中查找，找不到，则进行计算。（闭包不会释放外部的引用，从而使函数内部的值可以保留）<br/>
(3)实现封装，person之外的地方无法访问其内部变量的值，通过闭包的形式访问<br/>
```javascript
var person=function(){
  var name=”default”;
  return{
      getName:function(){
          return name;
},
setName:function (newName){
  	name=newName;
     }
  }
}();
print(person.name);  // 直接访问，结果为undefined
print(person.getName());  //default
person.setName(“MIKE”);
print(person.getName());   //MIKE
```
(4)实现面向对象中的对象<br/>
```javascript
function Person(){   
    var name = "default";          
    return {   
       getName : function(){   
           return name;   
       },   
       setName : function(newName){   
           name = newName;   
       }   
    }   
};      
var john =new Person();   
print(john.getName());   //default
john.setName("john");   
print(john.getName());    //john    
varjack =new Person();   
print(jack.getName());   //default
jack.setName("jack");   
print(jack.getName());    //jack
```
//john和jack都可以称为是Person这个类的实例，因为这两个实例对name这个成员的访问是独立的，互不影响的。<br/>
```javascript
function lazy_sum(arr) {
    var sum = function () {
        return arr.reduce(function (x, y) {
            return x + y;   //在函数lazy_sum中又定义了函数sum，
        }); //并且，内部函数sum可以引用外部函数lazy_sum的参数和局部变量，
    } //当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中
    return sum;   //注意sum是一个函数
}
varf = lazy_sum([1, 2, 3, 4, 5]); // 调用lazy_sum返回的不是求和结果，而是求和函数
f();  //15  调用函数f时，才是真正的计算结果
```
函数在其定义内部引用了局部变量arr，所以，当一个函数返回了一个函数后，其内部的局部变量还被新函数引用。<br/>
```javascript
function count() {
    var arr = [];
    for (var i=1; i<=3; i++) {
        arr.push(function () {
            return i * i;
        });     
  }
return arr; 
}
var results = count();
var f1 = results[0];   //16
var f2 = results[1];   //16
var f3 = results[2];  //16
```
返回的函数引用了变量i，但它并非立刻执行。等到3个函数都返回时，它们所引用的变量i已经变成了4，因此最终结果为16。<br/>
##返回闭包时牢记的一点就是：返回函数不要引用任何循环变量，或者后续会发生变化的变量。<br/>
如果一定要引用循环变量怎么办？方法是再创建一个函数，用该函数的参数绑定循环变量当前的值，然后立即调用该循环变量，无论该循环变量后续如何更改，已绑定到函数参数的值不变：<br/>
```javascript
function count() {
    var arr = [];
    for (var i=1; i<=3; i++) {
        arr.push(
(function (n) {
            return function () {
                return n * n;
            }
          })(i)     //这是一个匿名函数
      );       
    }
    return arr;
}
varresults = count();
var f1 = results[0];
var f2 = results[1];
var f3 = results[2];
f1();// 1
f2();// 4
f3();// 9
```
闭包的可以封装一个私有变量：
```javascript
function  create_counter(initial) {
    var x = initial || 0;
    return {
        inc: function () {
            x += 1;
            return x;
        }
    }
}
varc1 = create_counter();
c1.inc();// 1
c1.inc();// 2
c1.inc();// 3
varc2 = create_counter(10);
c2.inc();// 11
c2.inc();// 12
c2.inc();// 13
```
在返回的对象中，实现了一个闭包，该闭包携带了局部变量x，并且，从外部代码根本无法访问到变量x。<br/>
使用闭包写一个斐波那契数列求值<br/>
```javascript
function fibonacii(n){
    var sum=0,pre=0,next=1;
    return function(n){
        if(n==0){
            return 0;
        }else if(n==1){
            return 1;
        }
        for(var i=2;i<=n;i++){
            sum=pre+next;
            pre=next;
            next=sum;
        }
        return sum;
    }
}
for(var i=0;i<10;i++){
    console.log(fibonacii(i)(i));
}
```
### 使用生成器实现
```javascript
function* fibonacci(){
   let a=0;
   let b=1;
   yield a;
   yield b;
   while(true){
	 let next=a+b;
	 a=b;
	 b=next;
	 yield next;
   }
}
let gen=fibonacci();
for(var i=0;i<n;i++){
    console.log(gen.next().value);
}
```
8、JavaScript原型，原型链 ? 有什么特点？
------
每个对象都会在其内部初始化一个属性，就是prototype(原型)，当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么他就会去prototype里找这个属性，这个prototype又会有自己的prototype，于是就这样一直找下去，也就是我们平时所说的原型链的概念。<br/>
关系：instance.constructor.prototype = instance.__proto__（对象的__proto__属性和创建这个对象的构造函数的prototype属性是一个东西）<br/>

特点：<br/>
JavaScript对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变。而且创建子类型的实例时不能向超类型的构造函数中传递参数。<br/>
当我们需要一个属性的时，Javascript引擎会先看当前对象中是否有这个属性， 如果没有的话，就会查找他的Prototype对象是否有这个属性，如此递推下去，一直检索到 Object 内建对象。<br/>
```javascript
function Func(){}
Func.prototype.name = "Sean";
Func.prototype.getInfo = function() {
    return this.name;
}
var person = new Func();//现在可以参考var person = Object.create(oldObject);
console.log(person.getInfo());//它拥有了Func的属性和方法
//"Sean"
console.log(Func.prototype);
// Func { name="Sean", getInfo=function()}
```
Javascript作用链域?
------
全局函数无法查看局部函数的内部细节，但局部函数可以查看其上层的函数细节，直至全局细节。<br/>
当需要从局部函数查找某一属性或方法时，如果当前作用域没有找到，就会上溯到上层作用域查找，直至全局函数，这种组织形式就是作用域链。<br/>

9、javascript是面向对象的，怎么体现javascript的继承关系？
------
实现继承主要是依靠原型链来实现的<br/>
1）属性
```javascript
 父级构造函数.apply(this,arguments);       
 SuperType.apply(this,arguments);   实现父级构造函数的操作
```
2）方法
```javascript
person1.constructor == Person
Person.prototype.constructor == Person
子级构造函数.prototype=new 父级构造();       SubType.prototype=new SuperType();
子级构造函数.prototype.constructor=子级构造;   SubType.prototype.constructor=SubType;
```


## javascript继承的6种方法
原型链继承<br/>
借用构造函数继承<br/>
组合继承(原型+借用构造)<br/>
原型式继承<br/>
寄生式继承<br/>
寄生组合式继承<br/>
原型prototype机制或apply和call方法去实现较简单，建议使用构造函数与原型混合方式。<br/>
```javascript
function Parent(){
   this.name = 'wang';
}
function Child(){
   this.age = 28;
}
Child.prototype = new Parent();//继承了Parent，通过原型
var demo = new Child();
alert(demo.age);
alert(demo.name);//得到被继承的属性
}
```

10、javascript创建对象的几种方式？
------
工厂模式<br/>
构造函数模式<br/>
原型模式<br/>
混合构造函数和原型模式<br/>
动态原型模式<br/>
寄生构造函数模式<br/>
稳妥构造函数模式<br/>
javascript创建对象简单的说,无非就是使用内置对象或各种自定义对象，当然还可以用JSON；但写法有很多种，也能混合使用。<br/>
1）、对象字面量的方式<br/>
```javascript
person={
  firstname:"Mark",
  lastname:"Yun",
  age:25,
  eyecolor:"black"
};
```
2）、用function来模拟无参的构造函数
```javascript
function Person(){}
var person=new Person();//定义一个function，如果使用new"实例化",该function可以看作是一个Class
person.name="Mark";
person.age="25";
person.work=function(){
    alert(person.name+" hello...");
}
person.work();
```
3）、用function来模拟参构造函数来实现（用this关键字定义构造的上下文属性）
```javascript
function Pet(name,age,hobby){
   this.name=name;//this作用域：当前对象
   this.age=age;
   this.hobby=hobby;
   this.eat=function(){
      alert("我叫"+this.name+",我喜欢"+this.hobby+",是个程序员");
   }
}
var maidou =new Pet("麦兜",25,"coding");//实例化、创建对象
maidou.eat();//调用eat方法
```
4）、用工厂方式来创建（内置对象）
```javscript
var wcDog =new Object();
wcDog.name="旺财";
wcDog.age=3;
wcDog.work=function(){
   alert("我是"+wcDog.name+",汪汪汪......");
}
wcDog.work();
```
5）、用原型方式来创建
```javascript
function Dog(){
}
Dog.prototype.name="旺财";
Dog.prototype.eat=function(){
   alert(this.name+"是个吃货");
}
var wangcai =new Dog();
wangcai.eat();
```
6）、用混合方式来创建
```javascript
function Car(name,price){
   this.name=name;
   this.price=price; 
}
Car.prototype.sell=function(){
   alert("我是"+this.name+"，我现在卖"+this.price+"万元");
}
var camry =new Car("凯美瑞",27);
camry.sell(); 
```

11、call方法，apply方法
------
语法：call(thisObj，Object)<br/>
定义：调用一个对象的一个方法，以另一个对象替换当前对象。<br/>
说明：call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。<br/>
apply方法：<br/>
语法：apply(thisObj，[argArray])<br/>
定义：应用某一对象的一个方法，用另一个对象替换当前对象。<br/>
说明：如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。
```javascript
function add(a,b)
    {
        alert(a+b);
    }
    function sub(a,b)
    {
        alert(a-b);
    }
    add.call(sub,3,1);
```
例子中用 add 来替换 sub，add.call(sub,3,1) == add(3,1) ，所以运行结果为：alert(4);
注意：js 中的函数其实是对象，函数名是对 Function 对象的引用。
  
12、说几条写JavaScript的基本规范？
-----
1）.不要在同一行声明多个变量。<br/>
2）.请使用 ===/!==来比较true/false或者数值<br/>
3）.使用对象字面量替代new Array这种形式<br/>
4）.不要使用全局函数。<br/>
5）.Switch语句必须带有default分支<br/>
6）.函数不应该有时候有返回值，有时候没有返回值。<br/>
7）.For循环必须使用大括号<br/>
8）.If语句必须使用大括号<br/>
9）.for-in循环中的变量应该使用var关键字明确限定作用域，从而避免作用域污染。<br/>

13，在js中通过typeof能弹出的数据类型有哪些
-------
number，string，boolean，function，object，undefined记住typeof null是'object'<br/>

js数据类型和区分:<br/>
基本数据类型：String,boolean,Number,Undefined, Null<br/>
引用数据类型：Object(Array,Date,RegExp,Function)<br/>
Object 是 JavaScript 中所有对象的父对象
数据封装类对象：Object、Array、Boolean、Number 和 String
其他对象：Function、Arguments、Math、Date、RegExp、Error
区分基本数据类型：typeof;Undefined只能用typeof检测 typeof a=='undefined' typeof null是'object'<br/>
typeof 返回的类型都是字符串形式，可以判断function 的类型<br/>
区分引用数据类型：instanceof<br/>
区分各数据类型： Object.prototype.toString.call()<br/>

js 数组中提供了以下几个方法可以让我们很方便实现堆栈：<br/>
shift:从数组中把第一个元素删除，并返回这个元素的值。<br/>
unshift: 在数组的开头添加一个或更多元素，并返回新的长度<br/>
push:在数组的中末尾添加元素，并返回新的长度<br/>
pop:从数组中把最后一个元素删除，并返回这个元素的值。<br/>

JavaScript有几种类型的值？你能画一下他们的内存图吗？
-------
栈：原始数据类型（Undefined，Null，Boolean，Number、String）<br/>
堆：引用数据类型（对象、数组和函数）<br/>
两种类型的区别是：存储位置不同；<br/>
原始数据类型直接存储在栈(stack)中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；<br/>
引用数据类型存储在堆(heap)中的对象,占据空间大、大小不固定,如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。<br/>

14、谈谈This对象的理解。
------
this总是指向函数的直接调用者（而非间接调用者）；<br/>
如果有new关键字，this指向new出来的那个对象；<br/>
在事件中，this指向触发这个事件的对象，特殊的是，IE中的attachEvent中的this总是指向全局对象Window；<br/>

15、eval是做什么的？
-----
它的功能是把对应的字符串解析成JS代码并运行；<br/>
应该避免使用eval，不安全，非常耗性能（2次，一次解析成js语句，一次执行）。<br/>
由JSON字符串转换为JSON对象的时候可以用eval，var obj =eval('('+ str +')');<br/>

16、什么是window对象? 什么是document对象?
------
window对象代表浏览器中打开的一个窗口。document对象代表整个html文档。实际上，document对象是window对象的对象属性。

17、null，undefined 的区别？
------
null：示一个对象被定义了，值为“空值”；<br/>
undefined：表示不存在这个值。<br/>
typeof undefine//"undefined"<br/>
undefined :是一个表示"无"的原始值或者说表示"缺少值"，就是此处应该有一个值，但是还没有定义。当尝试读取时会返回 undefined；例如变量被声明了，但没有赋值时，就等于undefined。<br/>
typeof null//"object"<br/>
null : 是一个对象(空对象, 没有任何属性和方法)；<br/>
例如作为函数的参数，表示该函数的参数不是对象；<br/>
注意：<br/>
在验证null时，一定要使用　=== ，因为 == 无法区别 null 和　undefined<br/>

18、写一个通用的事件侦听器函数。
--------
```javascript
     markyun.Event = {
        // 页面加载完成后
        readyEvent : function(fn) {
            if (fn==null) {
                fn=document;
            }
            var oldonload = window.onload;
            if (typeof window.onload != 'function') {
                window.onload = fn;
            } else {
                window.onload = function() {
                    oldonload();
                    fn();
                };
            }
        },
        // 视能力分别使用dom0||dom2||IE方式 来绑定事件
        // 参数： 操作的元素,事件名称 ,事件处理程序
        addEvent : function(element, type, handler) {
            if (element.addEventListener) {
                //事件类型、需要执行的函数、是否捕捉
                element.addEventListener(type, handler, false);
            } else if (element.attachEvent) {
                element.attachEvent('on' + type, function() {
                    handler.call(element);
                });
            } else {
                element['on' + type] = handler;
            }
        },
        // 移除事件
        removeEvent : function(element, type, handler) {
            if (element.removeEventListener) {
                element.removeEventListener(type, handler, false);
            } else if (element.datachEvent) {
                element.detachEvent('on' + type, handler);
            } else {
                element['on' + type] = null;
            }
        },
        // 阻止事件 (主要是事件冒泡，因为IE不支持事件捕获)
        stopPropagation : function(ev) {
            if (ev.stopPropagation) {
                ev.stopPropagation();
            } else {
                ev.cancelBubble = true;
            }
        },
        // 取消事件的默认行为
        preventDefault : function(event) {
            if (event.preventDefault) {
                event.preventDefault();
            } else {
                event.returnValue = false;
            }
        },
        // 获取事件目标
        getTarget : function(event) {
            return event.target || event.srcElement;
        },
        // 获取event对象的引用，取到事件的所有信息，确保随时能使用event；
        getEvent : function(e) {
            var ev = e || window.event;
            if (!ev) {
                var c = this.getEvent.caller;
                while (c) {
                    ev = c.arguments[0];
                    if (ev && Event == ev.constructor) {
                        break;
                    }
                    c = c.caller;
                }
            }
            return ev;
        }
    };
```

19、["1", "2", "3"].map(parseInt) 答案是多少？
-------
[1, NaN, NaN] <br/>
parseInt() 函数能解析一个字符串，并返回一个整数，需要两个参数 (val, radix),其中 radix 表示要解析的数字的基数。【该值介于 2 ~ 36 之间，并且字符串中的数字不能大于radix才能正确返回数字结果值;但此处 map 传了 3 个 (element, index, array),我们重写parseInt函数测试一下是否符合上面的规则。<br/>
```javascript
function parseInt(str, radix) {   
    return str+'-'+radix;   
};  
var a=["1", "2", "3"];  
a.map(parseInt);  // ["1-0", "2-1", "3-2"] 不能大于radix
```
因为二进制里面，没有数字3,导致出现超范围的radix赋值和不合法的进制解析，才会返回NaN。所以["1", "2", "3"].map(parseInt) 答案也就是：[1, NaN, NaN]<br/>

20、事件是？IE与火狐的事件机制有什么区别？ 如何阻止冒泡？
-----
 1）. 我们在网页中的某个操作（有的操作对应多个事件）。例如：当我们点击一个按钮就会产生一个事件。是可以被 JavaScript 侦测到的行为。
 2）. 事件处理机制：IE是事件冒泡、Firefox同时支持两种事件模型，也就是：捕获型事件和冒泡型事件；
 3）. ev.stopPropagation();（旧ie的方法 ev.cancelBubble = true;）

21、javascript 代码中的"use strict";是什么意思 ? 使用它区别是什么？
-----
use strict是一种ECMAscript 5 添加的（严格）运行模式,这种模式使得 Javascript 在更严格的条件下运行,使JS编码更加规范化的模式,消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为。<br/>
默认支持的糟糕特性都会被禁用，比如不能用with，也不能在意外的情况下给全局变量赋值;<br/>
全局变量的显示声明,函数必须声明在顶层，不允许在非函数代码块内声明函数,arguments.callee也不允许使用；<br/>
消除代码运行的一些不安全之处，保证代码运行的安全,限制函数中的arguments修改，严格模式下的eval函数的行为和非严格模式的也不相同;<br/>
提高编译器效率，增加运行速度；<br/>
为未来新版本的Javascript标准化做铺垫。<br/>

21、如何判断一个对象是否属于某个类？
------
使用instanceof （待完善）
```javascript
if(a instanceof Person){
    alert('yes');
}
```

22、new操作符具体干了什么呢?
------
 1）、声明一个中间对象；<br/>
 2）、将该中间对象的原型指向构造函数的原型；<br/>
 3）、调用该构造函数，将构造函数的上下文对象this，指向该中间对象；<br/>
 4）、返回该中间对象，即返回实例对象。<br/>
```javascript
var obj  = {};
obj.__proto__ = fn.prototype;
fn.call(obj);//如果构造函数明确指定了返回对象时，那么new的执行结果就是该返回对象，否则就是返回的实例对象
```

23、Javascript中，有一个函数，执行时对象查找时，永远不会去查找原型，这个函数是？
-------
hasOwnProperty<br/>
javaScript中hasOwnProperty函数方法是返回一个布尔值，指出一个对象是否具有指定名称的属性。此方法无法检查该对象的原型链中是否具有该属性；该属性必须是对象本身的一个成员。<br/>
使用方法：<br/>
object.hasOwnProperty(proName),其中参数object是必选项。一个对象的实例。proName是必选项。一个属性名称的字符串值。如果 object 具有指定名称的属性，那么JavaScript中hasOwnProperty函数方法返回 true，反之则返回 false。

24、JSON 的了解？
------
JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。是键值对，是一个对象。<br/>
它是基于JavaScript的一个子集。数据格式简单, 易于读写, 占用带宽小<br/>
如：{"age":"12", "name":"back"}<br/>
JSON字符串转换为JSON对象:<br/>
var obj =eval('('+ str +')');<br/>
var obj = str.parseJSON();<br/>
var obj = JSON.parse(str);<br/>

JSON对象转换为JSON字符串：<br/>
var last=obj.toJSONString();<br/>
var last=JSON.stringify(obj);<br/>

25、js延迟加载的方式有哪些？
--------
defer和async、动态创建DOM方式（用得最多）、按需异步载入js

26、Ajax 是什么?ajax原理是什么？ajax 的交互模型?同步和异步的区别?如何解决跨域问题?
------
ajax的全称：Asynchronous Javascript And XML。<br/>
异步传输+js+xml。<br/>
所谓异步，在这里简单地解释就是：向服务器发送请求的时候，我们不必等待结果，而是可以同时做其他的事情，等到有了结果它自己会根据设定进行后续操作，与此同时，页面是不会发生整页刷新的，提高了用户体验。<br/>
Ajax就是通过JavaScript创建XMLHttpRequest对象，再由JavaScript调用XMLHttpRequest对象的方法完成异步通信；然后，再由JavaScript通过DOM的属性和方法，完成页面的不完全刷新。<br/>
由事件触发，创建一个XMLHttpRequest对象，把HTTP方法（Get/Post）和目标URL以及请求返回后的回调函数设置到XMLHttpRequest对象，通过XMLHttpRequest向服务器发送请求，请求发送后继续响应用户的界面交互，只有等到请求真正从服务器返回的时候才调用callback()函数，对响应数据进行处理。<br/>

## 原理：<br/>
AJAX技术的核心是XMLHttpRequest对象(XHR)，AJAX与数据格式无关，这种即使是无需刷新技术就可以从服务器取得数据，不一定是XML数据。<br/>
要完整实现一个AJAX异步调用和局部刷新,通常需要以下几个步骤:<br/>
(1)创建XMLHttpRequest对象,也就是创建一个异步调用对象。<br/>
```javascript
var xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");//IE
var xmlHttpRequest = new XMLHttpRequest();
```
(2)创建一个新的HTTP请求,并指定该HTTP请求的方法、URL及验证信息。说明XMLHttpRequest对象要从哪里获取数据。<br/>
```javascript
XMLHttpRequest.open(method,URL,flag,name,password)//method：get、post、head、put、delete
```
 (3)设置响应HTTP请求状态变化的函数。<br/>
XMLHttpRequest对象可以响应readystatechange事件，该事件在XMLHttpRequest对象状态改变时（readyState属性只要发生改变就会触发该函数）激发。因此，可以通过该事件调用一个函数，并在该函数中判断XMLHttpRequest对象的readyState属性值。<br/>
```javascript
//设置当XMLHttpRequest对象状态改变时调用的函数，注意函数名后面不要添加小括号
xmlHttpRequest.onreadystatechange = getData;
//定义函数
function getData(){
     //判断XMLHttpRequest对象的readyState属性值是否为4，如果为4表示异步调用完成
     if(xmlHttpRequest.readyState == 4){
          //设置获取数据的语句
          if(xmlHttpRequest.status >= 200 && xmlHttpRequest.status <300|| xmlHttpRequest.status == 304){
              //使用以下语句将返回结果以字符串形式输出
              document.write(xmlHttpRequest.responseText);
              //或者使用以下语句将返回结果以XML形式输出
              //docunment.write(xmlHttpRequest.responseXML);
          }
     }
}
```    
(4)发送HTTP请求。<br/>
```javascript
MLHttpRequest.send(data)
```
其中data是作为请求主体发送的数据，如果请求的数据不需要参数，则必须传入null。<br/>
(5)获取异步调用返回的数据。<br/>
如果XMLHttpRequest对象的readyState属性值等于4，表示异步调用过程完毕，使用responseText属性（作为响应主体）或responseXml属性来获取数据。但是，异步调用过程完毕，并不代表异步调用成功了，如果要判断异步调用是否成功，还要判断XMLHttpRequest对象的status属性值，只有该属性值为200，才表示异步调用成功。<br/>
(6)使用JavaScript和DOM实现局部刷新。<br/>
注意：在调用open()函数之前制定onreadystatechange事件处理程序才能确保跨浏览器兼容性。没有使用event对象直接使用XHR对象确定下一步该怎么做是较为可靠的一种方式。<br/>

### 整个XMLHttpRequest对象的生命周期应该包含如下阶段：
创建－初始化请求－发送请求－接收数据－解析数据－完成<br/>
readyState共有五个状态，分别为01234，但一般我们只关注4这个状态就好。但对于其各个状态的含义可以了解下，具体如下：<br/>
　　0 － （未初始化）还没有调用open()方法<br/>
　　1 － （启动）已调用open()方法，尚未调用send()方法<br/>
　　2 － （发送）已经调用send()，但尚未接收到响应<br/>
　　3 － （接收）已经接收到部分相应数据(已经接收到HTTP响应头部信息，但是消息体部分还没有完全接收到)<br/>
　　4 － （完成）已经接收到全部响应数据，可以在客户端调用了<br/>
(0)未初始化<br/>
此阶段确认XMLHttpRequest对象是否创建，并为调用open()方法进行未初始化作好准备。值为0表示对象已经存在，否则浏览器会报错－－对象不存在。<br/>
(1)启动<br/>
此阶段对XMLHttpRequest对象进行初始化，即调用open()方法，根据参数(method,url,true)完成对象状态的设置。并且XMLHttpRequest对象已经准备好将一个请求发送到服务器端。<br/>
(2)发送<br/>
已经通过send方法把一个请求发送到服务器端，但是还没有收到一个响应<br/>
(3)接收<br/>
此阶段解析接收到的服务器端响应数据。即根据服务器端响应头部返回的MIME类型把数据转换成能通过responseBody、responseText或responseXML属性存取的格式，为在客户端调用作好准备。状态3表示正在解析数据。<br/>
(4)完成<br/>
此阶段确认全部数据都已经解析为客户端可用的格式，解析已经完成。值为4表示数据解析完毕，可以通过XMLHttpRequest对象的相应属性取得数据。<br/>

请求返回前可调用abort()方法终止请求。<br/>
Comet是Ajax的进一步发展，让服务器几乎能够实时的向客户端发送数据，实现Comet主要有长轮询和HTTP流，所有浏览器都支持长轮询，而只有部分浏览器原生支持HTTP流，SSE是一种实现Comet交互的浏览器API，既支持长轮询，也支持HTTP流。<br/>

#### 优势:<br/>
<1>.无刷新更新数据。<br/>
AJAX最大优点就是能在不刷新整个页面的前提下与服务器通信维护数据。这使得Web应用程序更为迅捷地响应用户交互，并避免了在网络上发送那些没有改变的信息，减少用户等待时间，带来非常好的用户体验。<br/>
<2>.异步与服务器通信。<br/>
AJAX使用异步方式与服务器通信，不需要打断用户的操作，具有更加迅速的响应能力。优化了Browser和Server之间的沟通，减少不必要的数据传输、时间及降低网络上数据流量。<br/>
<3>.前端和后端负载平衡。<br/>
AJAX可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，AJAX的原则是“按需取数据”，可以最大程度的减少冗余请求和响应对服务器造成的负担，提升站点性能。<br/>
<4>.基于标准被广泛支持。<br/>
<5>.界面与应用分离。<br/>
Ajax使WEB中的界面与应用分离（也可以说是数据与呈现分离），有利于分工合作、减少非技术人员对页面的修改造成的WEB应用程序错误、提高效率、也更加适用于现在的发布系统。<br/>

#### Ajax的最大的特点是什么:<br/>
Ajax可以实现动态不刷新（局部刷新）<br/>
readyState属性 状态 有5个可取值： 0=未初始化 ，1=启动 2=发送，3=接收，4=完成<br/>

#### Ajax 同步和异步的区别:<br/>
1）同步：提交请求 -> 等待服务器处理 -> 处理完毕返回，这个期间客户端浏览器不能干任何事<br/>
2）异步：请求通过事件触发 -> 服务器处理（这是浏览器仍然可以作其他事情）-> 处理完毕<br/>
ajax.open方法中，第3个参数是设同步或者异步<br/>

#### ajax的缺点:<br/>
1）ajax不支持浏览器back按钮。<br/>
back和history存在的根本就是url的改变，ajax并没有改变url，用户单击后退按钮访问历史记录时，通过创建或使用一个隐藏的IFRAME来重现页面上的变更。（例如，当用户在Google Maps中单击后退时，它在一个隐藏的IFRAME中进行搜索，然后将搜索结果反映到Ajax元素上，以便将应用程序状态恢复到当时的状态。）但是它所带来的开发成本是非常高的，和ajax框架所要求的快速开发是相背离的。这是ajax所带来的一个非常严重的问题。<br/>
2）安全问题 AJAX暴露了与服务器交互的细节。<br/>
ajax技术就如同对企业数据建立了一个直接通道。这使得开发者在不经意间会暴露比以前更多的数据和服务器逻辑。ajax的逻辑可以对客户端的安全扫描技术隐藏起来，允许黑客从远端服务器上建立新的攻击。还有ajax也难以避免一些已知的安全弱点，诸如跨站点脚本攻击、SQL注入攻击和基于credentials的安全漏洞等。<br/>
3）对搜索引擎的支持比较弱。<br/>
搜索引擎在抓取页面的时候会屏蔽所有的JavaScript代码，而基于ajax技术的web站点所用的到的很重要的技术就是js代码，这样一来ajax载入的内容相对于搜索引擎就是透明的不利于各大搜索引擎的搜索。<br/>
4）破坏了程序的异常机制。<br/>
5）不容易调试。<br/>
6）违背URL和资源定位的初衷。<br/>
7）AJAX不能很好支持移动设备。<br/>
8）客户端过肥，太多客户端代码造成开发上的成本。编写复杂、容易出错；冗余代码比较多，破坏了Web的原有标准。<br/>
跨域： jsonp、 iframe、window.name、window.postMessage、服务器上设置代理页面<br/>


#### AJAX注意点及适用和不适用场景
##### (1).注意点
Ajax开发时，网络延迟——即用户发出请求到服务器发出响应之间的间隔——需要慎重考虑。不给予用户明确的回应，没有恰当的预读数据，或者对XMLHttpRequest的不恰当处理，都会使用户感到延迟，这是用户不希望看到的，也是他们无法理解的。通常的解决方案是，使用一个可视化的组件来告诉用户系统正在进行后台操作并且正在读取数据和内容。<br/>
##### (2).Ajax适用场景
<1>.表单驱动的交互<br/>
<2>.深层次的树的导航<br/>
<3>.快速的用户与用户间的交流响应<br/>
<4>.类似投票yes/no等无关痛痒的场景<br/>
<5>.对数据进行过滤和操纵相关数据的场景<br/>
<6>.普通的文本输入提示和自动完成的场景。<br/>
##### (3).Ajax不适用场景
<1>.部分简单的表单<br/>
<2>.搜索<br/>
<3>.基本的导航<br/>
<4>.替换大量的文本<br/>
<5>.对呈现的操纵。<br/>

27、Ajax 解决浏览器缓存问题？
------
确保ajax 或连接不走缓存路径：<br/>
1）、在ajax发送请求前加上 anyAjaxObj.setRequestHeader("If-Modified-Since","0")。<br/>
2）、在ajax发送请求前加上 anyAjaxObj.setRequestHeader("Cache-Control","no-cache")。<br/>
3）、在URL后面加上一个随机数： "fresh=" + Math.random();。<br/>
4）、在URL后面加上时间搓："nowtime=" + new Date().getTime();。<br/>
5）、如果是使用jQuery，直接这样就可以了 $.ajaxSetup({cache:false})。这样页面的所有ajax都会执行这条语句就是不需要保存缓存记录。<br/>

# Flash、Ajax各自的优缺点，在使用中如何取舍？
Flash适合处理多媒体、矢量图形、访问机器；对CSS、处理文本上不足，不容易被搜索。<br/>
Ajax对CSS、文本支持很好，支持搜索；多媒体、矢量图形、机器访问不足。<br/>
共同点：与服务器的无刷新传递消息、用户离线和在线状态、操作DOM。<br/>


28.简述同步和异步的区别
-------
同步是阻塞模式，异步是非阻塞模式。<br/>
同步就是指一个进程在执行某个请求的时候，若该请求需要一段时间才能返回信息，那么这个进程将会一直等待下去，直到收到返回信息才继续执行下去；<br/>
异步是指进程不需要一直等下去，而是继续执行下面的操作，不管其他进程的状态。当有消息返回时系统会通知进程进行处理，这样可以提高执行的效率<br/>
同步的概念应该是来自于OS中关于同步的概念:不同进程为协同完成某项工作而在先后次序上调整(通过阻塞,唤醒等方式).同步强调的是顺序性.谁先谁后.异步则不存在这种顺序性.<br/>
同步：浏览器访问服务器请求，用户看得到页面刷新，重新发请求,等请求完，页面刷新，新内容出现，用户看到新内容,进行下一步操作。<br/>
异步：浏览器访问服务器请求，用户正常操作，浏览器后端进行请求。等请求完，页面不刷新，新内容也会出现，用户看到新内容。<br/>
（待完善）

29、如何解决跨域问题?
-------
jsonp、document.domain + iframe(只有在主域相同的时候才能使用该方法) iframe、window.name、web-cocket、postMessage（HTML5中的XMLHttpRequest Level 2中的API）、服务器上设置代理页面，动态创建script，CORS<br/>

30、模块化开发怎么做？
------
模块化开发指的是在解决某一个复杂问题或者一系列问题时，依照一种分类的思维把问题进行系统性的分解以之解决。模块化是一种处理复杂系统分解为代码结构更合理，可维护性更高的可管理的模方式。对于软件行业：系统被分解为一组高内聚，低耦合的模块。<br/>
（1）定义封装的模块<br/>
（2）定义新模块对其他模块的依赖<br/>
（3）可对其他模块的引入支持<br/>
在JavaScript中出现了一些非传统模块开发方式的规范 CommonJS的模块规范，AMD（Asynchronous Module Definition），CMD（Common Module Definition）等
AMD是异步模块定义，所有的模块将被异步加载，模块加载不影响后边语句运行。<br/>
立即执行函数,不暴露私有成员<br/>
```javascript
var module1 = (function(){
   var _count = 0;
   var m1 = function(){
    　//...
   };
   var m2 = function(){
    　//...
   };
   return {
    　m1 : m1,
    　m2 : m2
   };
})();
```

31、AMD（Modules/Asynchronous-Definition）、CMD（Common Module Definition）规范区别？
------
Asynchronous Module Definition，异步模块定义，所有的模块将被异步加载，模块加载不影响后面语句运行。所有依赖某些模块的语句均放置在回调函数中。<br/>

区别：
1.对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible.<br/>
2.CMD 推崇依赖就近（在语句中使用到这个模块的内容时再书写），AMD 推崇依赖前置（在编写代码开始就写好依赖）。看代码：<br/>
```javascript
define(function(require, exports, module) {
    var a = require('./a')
    a.doSomething()
    // 此处略去 100 行
    var b = require('./b') // 依赖可以就近书写
    b.doSomething()
    // ...
})
// AMD 默认推荐
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
    a.doSomething()
    // 此处略去 100 行
    b.doSomething()
    // ...
})
```

32 requireJS的核心原理是什么？（如何动态加载的？如何避免多次加载的？如何缓存的？）
---------
（1）实现js文件的异步加载，避免网页失去响应；<br/>
（2）管理模块之间的依赖性，便于代码的编写和维护。<br/>
Requirejs基于AMD规范，每个模块用define定义，如果这个模块还依赖其他模块，那么define()函数的第一个参数，必须是一个数组，指明该模块的依赖性。主模块依次加载require的模块，使用require.config()方法，我们可以对模块的加载行为进行自定义。require.config()就写在主模块（main.js）的头部。参数就是一个对象，这个对象的paths属性指定各个模块的加载路径。shim属性，专门用来配置不兼容的模块。具体来说，每个模块要定义（1）exports值（输出的变量名），表明这个模块外部调用时的名称；（2）deps数组，表明该模块的依赖性。然后监测script的onload事件，判断所有模块加载成功，执行require的callback， 如果只带一个参数且不是数组，就是加载成功后return 模块，会记录加载成功的个数以及各个模块的顺序。<br/>

对CommonJs和AMD，CMD的理解：<br/>
都是为了使js代码模块化的规范，以前的时候如果一个js模块调用另一个模块，需要在html中进行link，而且必须有严格的引入顺序，但是这样又有可能造成阻塞，使页面失去响应。<br/>
CommonJS规定一个文件是一个模块，每个模块内部，module变量代表当前模块。这个变量是一个对象，它的exports属性（即module.exports）是对外的接口。加载某个模块，其实是加载该模块的module.exports属性。内置的require（路径以/开头是绝对路径，以./开头是相对本文件所在位置的路径,不以“./“或”/“开头，则表示加载的是一个默认提供的核心模块（位于Node的系统安装目录中），或者一个位于各级node_modules目录的已安装模块（全局安装或局部安装））命令用于加载模块文件。当使用require调用该模块时，就获得了exports对象。<br/>

每个模块内部，都有一个module对象，代表当前模块。它有以下属性。<br/>
  ● module.id 模块的识别符，通常是带有绝对路径的模块文件名。<br/>
  ● module.filename 模块的文件名，带有绝对路径。<br/>
  ● module.loaded 返回一个布尔值，表示模块是否已经完成加载。<br/>
  ● module.parent 返回一个对象，表示调用该模块的模块。<br/>
  ● module.children 返回一个数组，表示该模块要用到的其他模块。<br/>
  ● module.exports 表示模块对外输出的值。<br/>

CommonJs是同步加载 JS 脚本，Node.js 使用了这一规范。这一规范和我们之前的做法比较类似，是同步加载 JS 脚本。这么做在服务端毫无问题，因为文件都存在磁盘上，然而浏览器的特性决定了 JS 脚本需要异步加载，否则就会失去响应，因此 CommonJS 规范无法直接在浏览器中使用。<br/>

CommonJS模块的特点如下:<br/>
  ● 所有代码都运行在模块作用域，不会污染全局作用域。<br/>
  ● 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。<br/>
  ● 模块加载的顺序，按照其在代码中出现的顺序。<br/>
  
在Node中使用exports=module.exports，可以对exports进行返回值的设定，但是不能对exports和module.exports进行赋值，而且也不能使用exports输出，只能使用module.exports输出module.exports = function (x){ console.log(x);};。当使用require调用自身模块时就会执行自身的module.exports<br/>
CommonJS模块的加载机制是，输入的是被输出的值的拷贝。<br/>
AMD规范：前置加载，所有前置模块异步加载结束后，才进行调用callback。require.js实现了这个规范。<br/>
缓存：所有缓存的模块保存在require.cache之中，如果想删除模块的缓存，可以像下面这样写。<br/>
// 删除指定模块的缓存<br/>
delete require.cache[moduleName];<br/>
// 删除所有模块的缓存<br/>
Object.keys(require.cache).forEach(function(key) {<br/>
  delete require.cache[key];<br/>
})<br/>

注意，缓存是根据绝对路径识别模块的，如果同样的模块名，但是保存在不同的路径，require命令还是会重新加载该模块。<br/>
require.main：<br/>
require方法有一个main属性，可以用来判断模块是直接执行，还是被调用执行。直接执行的时候（node module.js），require.main属性指向模块本身。require.main === module// true。调用执行的时候（通过require加载该脚本执行），上面的表达式返回false。<br/>

require的内部处理流程：<br/>
require命令是CommonJS规范之中，用来加载其他模块的命令。它其实不是一个全局命令，而是指向当前模块的module.require命令，而后者又调用Node的内部命令Module._load。<br/>
```javascript
Module._load = function(request, parent, isMain) {
  // 1. 检查 Module._cache，是否缓存之中有指定模块
  // 2. 如果缓存之中没有，就创建一个新的Module实例
  // 3. 将它保存到缓存
  // 4. 使用 module.load() 加载指定的模块文件，
  //    读取文件内容之后，使用 module.compile() 执行文件代码
  // 5. 如果加载/解析过程报错，就从缓存删除该模块
  // 6. 返回该模块的 module.exports
};
```

上面的第4步，采用module.compile()执行指定模块的脚本，逻辑如下。<br/>
```javascript
Module.prototype._compile = function(content, filename) {
  // 1. 生成一个require函数，指向module.require
  // 2. 加载其他辅助方法到require
  // 3. 将文件内容放到一个函数之中，该函数可调用 require
  // 4. 执行该函数
};
```
上面的第1步和第2步，require函数及其辅助方法主要如下。<br/>
  ● require(): 加载外部模块<br/>
  ● require.resolve()：将模块名解析到一个绝对路径<br/>
  ● require.main：指向主模块<br/>
  ● require.cache：指向所有缓存的模块<br/>
  ● require.extensions：根据文件的后缀名，调用不同的执行函数<br/>
一旦require函数准备完毕，整个所要加载的脚本内容，就被放到一个新的函数之中，这样可以避免污染全局环境。该函数的参数包括require、module、exports，以及其他一些参数。<br/>
```javascript
(function (exports, require, module, __filename, __dirname) {
  // YOUR CODE INJECTED HERE!
});
```
Module._compile方法是同步执行的，所以Module._load要等它执行完成，才会向用户返回module.exports的值。<br/>

CMD规范：就近加载，在需要用到依赖的时候才申明，可同步可异步，Sea.js实现了这个规范，Sea.js 遇到依赖后只会去下载 JS 文件，并不会执行，而是等到所有被依赖的 JS 脚本都下载完以后，才从头开始执行主逻辑。因此被依赖模块的执行顺序和书写顺序完全一致。<br/>

33、异步加载JS的方式有哪些？
------
(1) defer，只支持IE<br/>
(2) async：<br/>
(3) 动态创建script，插入到DOM中，加载完毕后callBack<br/>


34、DOM操作——怎样添加、移除、移动、复制、创建和查找节点?
-------
（1）创建新节点<br/>
  createDocumentFragment()//创建一个DOM片段<br/>
  createElement()   //创建一个具体的元素<br/>
  createTextNode()   //创建一个文本节点<br/>
（2）添加、移除、替换、插入<br/>
  appendChild()<br/>
  removeChild()<br/>
  replaceChild()<br/>
  insertBefore() //在已有的子节点前插入一个新的子节点<br/>
（3）查找<br/>
  getElementsByTagName()    //通过标签名称<br/>
  getElementsByName()    //通过元素的Name属性的值(IE容错能力较强，会得到一个数组，其中包括id等于name值的)<br/>
  getElementById()    //通过元素Id，唯一性<br/>


35、JS 怎么实现一个类。怎么实例化这个类
------
可以使用命名空间，js中的一个类就是一个对象，也可以说就是一个模块，

36、Jquery与jQuery UI 有啥区别？
-----
jQuery是一个js库，主要提供的功能是选择器，属性修改和事件绑定等等。<br/>
jQuery UI则是在jQuery的基础上，利用jQuery的扩展性，设计的插件。<br/>
提供了一些常用的界面元素，诸如对话框、拖动行为、改变大小行为等等<br/>

37、针对 jQuery 的优化方法？
-----
1）基于Class的选择性的性能相对于Id选择器开销很大，因为需遍历所有DOM元素。<br/>
2）频繁操作的DOM，先缓存起来再操作。用Jquery的链式调用更好。<br/>
 比如：var str=$("a").attr("href");<br/>
3）for (var i = size; i < arr.length; i++) {}<br/>
for 循环每一次循环都查找了数组 (arr) 的.length 属性，在开始循环的时候设置一个变量来存储这个数字，可以让循环跑得更快：for (var i = size, length = arr.length; i < length; i++) {}<br/>

38、如何判断当前脚本运行在浏览器还是node环境中？（阿里）
-------
通过判断 Global 对象是否为window，如果不为window，当前脚本没有运行在浏览器中。即在node中的全局变量是global ,浏览器的全局变量是window。 可以通过该全局变量是否定义来判断宿主环境<br/>

39、那些操作会造成内存泄漏？
-------
内存泄漏指任何对象在您不再拥有或需要它之后仍然存在。<br/>
垃圾回收器定期扫描对象，并计算引用了每个对象的其他对象的数量。如果一个对象的引用数量为 0（没有其他对象引用过该对象），或对该对象的惟一引用是循环的，那么该对象的内存即可回收。<br/>
setTimeout 的第一个参数使用字符串而非函数的话，会引发内存泄漏。<br/>
闭包、控制台日志、循环（在两个对象彼此引用且彼此保留时，就会产生一个循环）<br/>
防止内存泄露：<br/>
1)、不要动态绑定事件；<br/>
2)、不要在动态添加，或者会被动态移除的dom 上绑事件，用事件冒泡在父容器监听事件；<br/>
3)、如果要违反上面的原则，必须提供destroy 方法，保证移除dom 后事件也被移除，这点可以参考Backbone 的源代码，做的比较好；<br/>
4)、单例化，少创建dom，少绑事件。<br/>

40、JQuery一个对象可以同时绑定多个事件，这是如何实现的？
-------
在1.4.0之前（不包含1.4.0）无法使用多个绑定的，单个事件绑定代码为：<br/>
```javascript
$('.clickme').live('click', function() {
  alert("click");
 });
 ```
1.4.0-1.4.2版本开始支持绑定多个事件，如下代码：<br/>
```javasccript
$('.hoverme').live('mouseover mouseout', function(event) {
  if (event.type == 'mouseover') {
     alert("mouseover");
  } else {
    alert("mouseout");
  }
});
```
1.4.3之后的版本又开始支持另外一种更新的方法：<br/>
```javascript
$('a').live({
  click: function() {
    // do something on click
  },
  mouseover: function() {
    // do something on mouseover
  }
});
```

41、什么是“前端路由”?什么时候适合使用“前端路由”? “前端路由”有哪些优点和缺点?
-----
### 什么是前端路由？
路由是根据不同的url地址展示不同的内容或页面。前端路由就是把不同路由对应不同的内容或页面的任务交给前端来做，之前是通过服务端根据 url 的不同返回不同的页面实现的。

### 什么时候使用前端路由？
在单页面应用，大部分页面结构不变，只改变部分内容的使用

### 前端路由有什么优点和缺点？
优点：<br/>
用户体验好，不需要每次都从服务器全部获取，快速展现给用户。<br/>
缺点：<br/>
使用浏览器的前进，后退键的时候会重新发送请求，没有合理地利用缓存。<br/>
单页面无法记住之前滚动的位置，无法在前进，后退的时候记住滚动的位置<br/>

42、用js实现千位分隔符?(提示：正则+replace)
-------
```javascript
function commafy(num) {
     num = num + '';
     var reg = /(-?d+)(d{3})/;

    if(reg.test(num)){
     num = num.replace(reg, '$1,$2');
    }
    return num;
}
```

43、检测浏览器版本版本有哪些方式？
------
功能检测、userAgent特征检测<br/>
比如：navigator.userAgent<br/>
```javascript
//"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36
  (KHTML, like Gecko) Chrome/41.0.2272.101 Safari/537.36"
```

44、What is a Polyfill?
------
polyfill 是“在旧版浏览器上复制标准 API 的 JavaScript 补充”,可以动态地加载 JavaScript 代码或库，在不支持这些标准 API 的浏览器中模拟它们。例如，geolocation（地理位置）polyfill 可以在 navigator 对象上添加全局的 geolocation 对象，还能添加 getCurrentPosition 函数以及“坐标”回调对象，所有这些都是 W3C 地理位置 API 定义的对象和函数。因为 polyfill 模拟标准 API，所以能够以一种面向所有浏览器未来的方式针对这些 API 进行开发，一旦对这些 API 的支持变成绝对大多数，则可以方便地去掉 polyfill，无需做任何额外工作。<br/>

做的项目中，有没有用过或自己实现一些 polyfill 方案（兼容性处理方案）？<br/>
比如： html5shiv、Geolocation、Placeholder <br/>

45、我们给一个dom同时绑定两个点击事件，一个用捕获，一个用冒泡。会执行几次事件，会先执行冒泡还是捕获？
-------
从上往下，如有捕获事件，则执行；一直向下到目标元素后，从目标元素开始向上执行冒泡元素，即第三个参数为true表示捕获阶段调用事件处理程序，如果是false则是冒泡阶段调用事件处理程序。(在向上执行过程中，已经执行过的捕获事件不再执行，只执行冒泡事件。)<br/>
点击的某一个元素的时候，其祖先元素的事件是遵循先捕获再冒泡，但是在本元素上的事件是按照代码顺序执行的，与冒泡和捕获无关，写在前面的先执行，所以结论是：绑定在被点击元素的事件是按照代码顺序发生，其他元素通过冒泡或者捕获“感知”的事件，按照W3C的标准，先发生捕获事件，后发生冒泡事件。所有事件的顺序是：其他元素捕获阶段事件 -> 本元素代码顺序事件 -> 其他元素冒泡阶段事件 。<br/>

所以本题是会执行两次，但是执行顺序按照代码的书写顺序执行。<br/>

46、使用JS实现获取文件扩展名？
```javascript
function getFileExtension(filename) {
  return filename.slice((filename.lastIndexOf(".") - 1 >>> 0) + 2);
}
```
String.lastIndexOf() 方法返回指定值（本例中的'.'）在调用该方法的字符串中最后出现的位置，如果没找到则返回 -1。对于'filename'和'.hiddenfile'，lastIndexOf的返回值分别为0和-1无符号右移操作符(>>>) 将-1转换为4294967295，将-2转换为4294967294，这个方法可以保证边缘情况时文件名不变。String.prototype.slice() 从上面计算的索引处提取文件的扩展名。如果索引比文件名的长度大，结果为""。<br/>

47、跨域访问
------
在同源策略影响下，一个域名A的网页可以获取域名B下的脚本,css,图片等，但是不能发送Ajax请求，也不能操作Cookie、LocalStorage等数据。AJAX通信默认情况下，XHR对象只能访问与包含它的页面位于同一个域(协议，域名，端口相同)中的资源(使用 XMLHttpRequest 对象发起 HTTP 请求就必须遵守同源策略。)，这种安全策略可以防止某些恶意行为。<br/>
## 1）—$.ajax()支持get方式跨域：jsonp(JSON with Padding)
JSONP是包含在函数调用中的json。e.g.callback({'name':'nini'})。<br/>
dataType: 'jsonp',原先的beforeSend和error方法都不再触发，原因可能是dataType如果指定为jsonp的话,就已经不是ajax事件了(即JSONP始终是无状态连接，不能获悉连接状态和错误事件)，只能使用计时器在规定时间内是否接收到了响应。<br/>
原理：<br/>
动态添加一个\<script\>标签，而script标签的src属性是没有跨域的限制的。这样说来,这种跨域方式其实与ajax XmlHttpRequest协议无关了。取而代之的则是JSONP协议，主要是利用script标签不受同源策略(同源策略，即JavaScript或Cookie只能访问同域下的内容)限制的特性，向跨域的服务器请求并返回一段JSON数据。
JSONP是一个非官方的协议，它允许在服务器端集成Script tags返回至客户端，通过javascript callback的形式实现跨域访问JSONP即JSON with Padding。由于同源策略的限制，XmlHttpRequest只允许请求当前源（域名、协议、端口）的资源。如果要进行跨域请求，我们可以通过使用html的script标记来进行跨域请求，并在响应中返回要执行的script代码，其中可以直接使用JSON传递javascript对象。这种跨域的通讯方式称为JSONP。onCallback 函数 是浏览器客户端注册的，获取跨域服务器上的json数据后，回调的函数。
```javascript
$.ajax({
   async:false,
   url: http://跨域的dns/document!searchJSONResult.action,
   type: "GET",
   dataType: 'jsonp',
   jsonpCallback: 'jsonp1236827957501',// //callback名称
   data: qsData,
   timeout: 5000,
   success: function (json) {//客户端jquery预先定义好的callback函数,成功获取跨域服务器上的json数据后,会动态执行这个callback函数
    if(json.actionErrors.length!=0){
           alert(json.actionErrors);
     }
       	   genDynamicContent(qsData,type,json);
   }
});
```
流程：<br/>
1）首先在客户端注册一个callback (如:'jsoncallback'), 然后把callback的名字(如:jsonp1236827957501)传给服务器。注意：服务端得到callback的数值后，要用jsonp1236827957501(......)把将要输出的json内容包括起来，此时，服务器生成 json 数据才能被客户端正确接收。<br/>
2）以 javascript 语法的方式，生成一个function , function 名字就是传递上来的参数 'jsoncallback'的值 jsonp1236827957501。<br/>
3）将 json 数据直接以入参的方式，放置到 function 中，这样就生成了一段 js 语法的文档，返回给客户端。<br/>
客户端浏览器，解析script标签，并执行返回的 javascript 文档，此时javascript文档数据,作为参数，传入到了客户端预先定义好的 callback 函数(如上例中jquery $.ajax()方法封装的的success: function (json))里。（动态执行回调函数）可以说jsonp的方式原理上和\<script src="http://跨域/...xx.js"\>\</script\>是一致的(qq空间就是大量采用这种方式来实现跨域数据交换的)。JSONP是一种脚本注入(Script Injection)行为,所以也有一定的安全隐患.<br/>
原理代码：<br/>
```javascript
//客户端的JAVASCRIPT代码 
var script=document.createElement("script"); 
script.src="http://www.pl4cj.com:8888/5/6/action.php?param=123&callback="+fnName; 
document.getElementsByTagName("head")[0].appendChild(script) 
```
//服务器端的PHP代码，一定要有callback来进行回调，在这里加上括号，是让它以语句块的方式来进行解析<br/>
```php
<?php 
<SPAN style="COLOR: #ff00ff">echo $_GET["callback"]."(".json_encode($_GET).");"; 
</SPAN>?
```
jquey是不支持post方式跨域的。<br/>

## 2）—html5 WebSocket跨域：IE浏览器目前不支持WebSocket通信
WebSocket protocol 是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很棒的实现。<br/>
Web Sockets使用了自定义的协议，未加密的连接是ws://加密的连接是wss://。使用自定义的协议而非标准HTTP协议的好处是能够在客户端和服务器端传送非常少的数据，而不必担心http那样字节级的开销，由于传递数据包小，web socket非常适合移动应用。<br/>
WebSocket对象也有一个readyState属性表示当前状态WebSocket.OPENING(0)：正在创建连接；WebSocket.OPEN(1)：已经创建连接；WebSocket.CLOSING(2)：正在关闭连接；WebSocket.CLOSE(3)：已经关闭连接。<br/>
客户端：<br/>
```javascript
var socket = new WebSocket('ws://localhost:8080');// 创建Socket实例，只能是ws或wss，马上尝试创建连接					              
socket.onopen = function(event) {                                        // 打开Socket 
socket.send('I am the client and I\'m listening!');                  // 发送任意字符串JSON.stringify(message)
}
socket.onmessage = function(event) {                                  // 接收到服务器消息时触发
 console.log('Client received a message',event.data); 	//event.data返回的是字符串
}; 
socket.onclose = function(event) {                                         // 监听Socket的关闭
console.log('Client notified socket has closed',event.data); 
}; 
socket.onerror=function(event){                      //event有三个属性wasClean-连接是否明确关闭，code-服务器反
console.log('Connection error');                 //回的状态码，reason-字符串包含服务器返回的消息
}
socket.close()                                                                          // 关闭Socket.... 
```
onmessage事件提供了一个data属性，它可以包含消息的Body部分。消息的Body部分必须是一个字符串，可以进行序列化/反序列化操作，以便传递更多的数据。由于IE不支持WebSocket通信，Guillermo Rauch创建了一个Socket.IO技术。Socket.IO使用检测功能来判断是否建立WebSocket连接，或者是AJAX long-polling连接，或Flash等。可快速创建实时的应用程序。Socket.IO还提供了一个NodeJS API，它看起来非常像客户端API<br/>
```javascript
<script src="http://cdn.socket.io/stable/socket.io.js"></script>
var socket= new io.Socket('localhost',{                         // 创建Socket.IO实例，建立连接
  port: 8080 
}); 
socket.connect(); 
socket.on('connect',function() {                                    // 添加一个连接监听器
  console.log('Client has connected to the server!'); 
});
socket.on('message',function(data) {                           // 添加一个连接监听器
  console.log('Received a message from the server!',data); 
});
socket.on('disconnect',function() {                              // 添加一个关闭连接的监听器
  console.log('The client has disconnected!'); 
}); 
function sendMessageToServer(message) {                // 通过Socket发送一条消息到服务器
  socket.send(message); 
}
```
服务器端：<br/>
```javascript
// 需要HTTP 模块来启动服务器和Socket.IO
var http= require('http'), io= require('socket.io'); 
// 在8080端口启动服务器
var server= http.createServer(function(req, res){ 
res.writeHead(200,{ 'Content-Type': 'text/html' });              // 发送HTML的headers和message
res.end('<h1>Hello Socket Lover!</h1>'); 
}); 
server.listen(8080); 
var socket= io.listen(server);                                               // 创建一个Socket.IO实例，把它传递给服务器
socket.on('connection', function(client){ 
client.on('message',function(event){                                  // 成功！现在开始监听接收到的消息
    console.log('Received message from client!',event); 
  }); 
client.on('disconnect',function(){ 
clearInterval(interval); 
console.log('Server has disconnected'); 
  }); 
});
```
javascript 跨域有两种情况：<br/>
1、基于同一父域的子域之间，如：a.c.com 和b.c.com<br/>
2、基于不同的父域之间，如：www.a.com 和www.b.com<br/>
3、端口的不同，如：www.a.com:8080 和www.a.com:8088<br/>
4、协议不同，如：http://www.a.com 和https://www.a.com<br/>
对于情况3和4，需要通过后台proxy 来解决，具体方式如下：<br/>
a、在发起方的域下创建proxy 程序<br/>
b、发起方的js 调用本域下的proxy 程序<br/>
c、proxy 将请求发送给接收方并获取相应数据<br/>
d、proxy 将获得的数据返回给发起方的js<br/>
代码和ajax调用一致，其实这种方式就是通过ajax进行调用的。<br/>
而情况1和2除了通过后台proxy 这种方式外，还可以有多种办法来解决：<br/>
1、document.domain+iframe（只能解决情况1）：<br/>
  a 、在发起方页面和接收方页面设置document.domain ， 并将值设为父域的主域名(window.location.hostname)<br/>
  b、在发起方页面创建一个隐藏的iframe，iframe 的源是接收方页面<br/>
  c、根据浏览器的不同，通过iframe.contentDocument || iframe.contentWindow.document来获得接收方页面的内容<br/>
  d、通过获得的接收方页面的内容来与接收方进行交互<br/>
这种方法有个缺点，就是当一个域被攻击时，另一个域会有安全漏洞出现。<br/>
当不能使用web socket组合XHR和SSE也是可以实现双向通信的。<br/>

## 3）—Flash：
跟WebSocket一样走的TCP/IP套接字协议<br/>

## 4）—AJAX long-polling 
模拟websocket<br/>

## W5）—IFrame：
该方法只适合主域相同但子域不同的情况，比如 a.com 和 www.a.com，我们只需要给这两个页面都加上一句 document.domain = 'a.com' ，就可以在其中一个页面嵌套另一个页面，然后进行窗体间的交互。<br/>

## 6）CORS：支持POST
CORS定义一种跨域访问的机制，可以让AJAX实现跨域访问。基本思想是使用自定义的HTTP头部允许浏览器和服务器相互了解对方，从而决定请求或响应成功与否。
在发送头信息的时候，会附加一个额外的Origin头部，Origin头部包含请求页面的头部（协议，域名，端口），这样服务器可以很容易的决定它是否应该提供响应。如果服务器认为这个请求可以接受，就在Access-Control-Allow-Origin的头部中回发相同的域信息，如果是公共资源就回发。<br/>
### IE(IE8+支持)对CORS的实现：
引入XDomainRequest，这个对象不发送和接收cookie，只能设置请求头信息中的Content-Type，不能访问返回头，只支持get和post，使用与XHR类似，open（请求类型，URL），只支持异步，接到响应后，没有办法确定响应的状态代码，响应有效触发onload事件，失败触发onerror事件也支持timeout和ontimeout事件.发送post请求时需要设置请求头的contentType属性。<br/>
### 其他浏览器对CORS的实现：
通过XHR实现对CORS的原生支持，与XDR不同，通过SHR跨域对象可以访问status和statusText属性，而且支持同步，限制了不能使用setRequestHeader()自定义头部，不能发送和接收cookie，调用getAllResponseHeader()总返回空字符串。<br/>
默认情况下跨源请求不提供凭据（cookie，http认证以及客户端ssl证明）,通过设置withCredentials为ture，指定某个请求应该发送凭据。服务器接收后会返回Access-Control-Allow-Credentials:true<br/>
CORS提供了一种跨域请求方案，但没有为安全访问提供足够的保障机制。jsonp是get形式，承载的信息量有限，所以信息量较大时CORS是不二选择；<br/>
### Comet：长轮询和流。
SSE：长轮询，短轮询和HTTP流，半双工。SSE支持短轮询、长轮询和HTTP流，而且能在断开连接时自动确定何时重新连接。，要创建新的EventSource对象，并传入一个入口点：<br/>
var source=new EventSource("myevents.php");<br/>
注意：要传入的URL必须与创建对象的页面同源。EventSource的实例有一个readyState属性，值为0表示正连接到服务器，值为1表示打开了连接，值为2表示关闭连接。另外还有以下三个事件：<br/>
open：在建立连接时触发。<br/>
message：在从服务器接收到新事件时触发。<br/>
error：在无法建立连接时触发。<br/>
服务器返回的数据以字符串的格式保存在event.data中。<br/>
默认情况下，EventSource对象会保存于服务器的活动连接。如果连接断开，还会重新连接。这就意味着SSE适合长轮询和HTTP流。如果想强制立即断开连接并且不再重新连接，可以调用close()方法。<br/>

## 7）动态创建script

48、事件
-------
IE的事件流叫事件冒泡，IE5.5更早版本事件冒泡会跳过html元素，其他的都一直冒泡到window对象，DOM事件模型的最独特的性质是，文本节点也触发事件(在IE不会)。IE提出的是冒泡流，而网景提出的是捕获流，后来在W3C组织的统一之下，JS支持了冒泡流和捕获流。但是目前IE6,IE7,IE8均只支持冒泡流，所以为了能够兼容更多的浏览器，建议大家使用冒泡流。<br/>
// 阻止浏览器默认行为兼容性写法<br/>
event.preventDefault ? event.preventDefault() :(event.returnValue = false);<br/>
// 阻止冒泡写法<br/>
event.stopPropagation ? event.stopPropagation(): (event.cancelBubble = true);<br/>
DOM事件流：“DOM2级事件”规定事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。高版本浏览器都会在捕获阶段触发事件对象上的事件，结果就是有两个机会在目标对象上面操作。<br/>
事件处理程序：响应某个事件的函数就叫做事件处理程序。DOM0级的事件处理程序很简单，只会在冒泡阶段被处理。<br/>
0级DOM：<br/>
分为2个：一是在标签内写onclick事件<br/>
　　　　  二是在JS写onlicke=function（）{}函数<br/>
2级DOM：<br/>
只有一个：监听方法，原生有两个方法用来添加和移除事件处理程序：addEventListener()和removeEventListener()。<br/>
它们都有三个参数：第一个参数是事件名（如click）；<br/>
　　　　　　　　　第二个参数是事件处理程序函数；<br/>
　　　　　　　　   第三个参数如果是true则表示在捕获阶段调用，为false表示在冒泡阶段调用。<br/>
  ● addEventListener():可以为元素添加多个事件处理程序，触发时会按照添加顺序依次调用。（这也是为什么DOM0级事件兼容各种浏览器，我们却还是要使用DOM2的原因之一。）<br/>
  ● removeEventListener():不能移除匿名添加的函数。<br/>
而IE与DOM不同，它有自己的方法：attachEvent()和detachEvent()，由于IE8以及更早版本只支持事件冒泡，所以通过attachEvent()添加的事件处理程序都会被添加到冒泡阶段（所以不需要第三个参数）。注意第一个参数是onclick，而非DOM标准的click，在IE中使用attachEvent()与使用DOM0级方法的主要区别在于事件处理程序的作用域，在使用DOM0级方法的情况下，事件处理程序会在其所属元素的作用域内运行，而在使用attachEvent()方法的情况下，事件处理程序在全局作用域中运行，因此this等于window（这点要特别注意！！！）。attachEvent()也能添加多个事件处理程序，但是事件的执行顺序和添加顺序相反。<br/>

区别：如果定义了两个dom0级事件，dom0级事件会覆盖<br/>
dom2不会覆盖，会依次执行。<br/>
dom0和dom2可以共存，不互相覆盖，但是dom0之间依然会覆盖<br/>

## 事件委托：
对“事件处理程序过多”问题的解决方案就是事件委托。事件委托利用了事件冒泡，将监听器安放到它们的父元素，只指定一个事件处理程序，就可以管理某一类型的所有事件。新添加的子元素也会拥有该事件。<br/>
如何能知道是那个子元素被点击：当子元素的事件冒泡到父元素时，你可以检查事件对象的target属性，捕获真正被点击的节点元素的引用<br/>
var event= event|| window.event; //获得event对象兼容性写法<br/>
var target = event.target || event.srcElement;  //获得target兼容型写法<br/>
bind不能为新添的元素添加已经绑定的事件。<br/>
JQuery1.3通过live进行事件委托，但是不能在连缀的DOM遍历方法后面使用，默认把事件绑定到$(document)元素，如果DOM嵌套结构很深，事件冒泡通过大量祖先元素会导致性能损失；为了避免生成不必要的jQuery对象，可以使用一种叫做“早委托”的hack，即在$(document).ready()方法外部调用.live()：<br/>
```javascript
(function($){  
    $("#info_table td").live("click",function(){/*显示更多信息*/});  
})(jQuery);
```
(function($){...})(jQuery)是一个“立即执行的匿名函数”，构成了一个闭包，可以防止命名冲突。使用这个hack时，脚本必须是在页面的head元素中链接和(或)执行的。因为这时候刚好document元素可用，而整个DOM还远未生成。不会产生多余的jquery对象。<br/>
jQuery从1.4开始支持在使用.live()方法时配合使用一个上下文参数：$("td",$("#info_table")[0]).live("click",function(){});“受托方”就从默认的$(document)变成了$("#info_table")[0]，节省了冒泡的旅程，上下文对象使用的是$("#info_table")[0]。<br/>
jQuery 1.4.2干脆直接引入了一个新方法.delegate()，支持在连缀的DOM遍历方法后面调用。<br/>
提示：使用事件委托时，如果注册到目标元素上的其他事件处理程序使用.stopPropagation()阻止了事件传播，那么事件委托就会失效。<br/>
undelegate(): 移除delegate的绑定。<br/>

jquery事件绑定，on和冒泡<br/
我们的页面可以理解为一棵DOM树，当我们在叶子结点上做什么事情的时候（如click一个a元素），如果我们不人为的设置stopPropagation(Moder Browser), cancelBubble(IE)，那么它的所有父元素，祖宗元素都会受之影响，它们上面绑定的事件也会产生作用。<br/>
$('a').bind('click', function() { alert("That tickles!") });<br/>
当我们在a上面点击的时候，首先会触发它本身所绑定的click事件，然后会一路往上，触发它的父元素，祖先元素上所有绑定的click事件。 

## jquery的绑定事件有几种方式 ，请举例说明其优缺点。
jQuery中提供了四种事件监听方式，分别是bind、live、delegate、on，对应的解除监听的函数分别是unbind、die、undelegate、off。
### .bind()
.bind()是最直接的绑定方法 ，会绑定事件类型和处理函数到DOM element上, 这个方法是存在最久的，而且也很好的解决了浏览器在事件处理中的兼容问题。但是这个方法有一些performance方面的问题，看下面的代码：<br/>
$( "#members li a" ).bind( "click", function( e ) {} ); <br/>
$( "#members li a" ).click( function( e ) {} );<br/>
上面的两行代码所完成的任务都是一致的，就是把event handler加到全部的匹配的a元素上。 
效率:<br/>
一方面，我们隐式地把click handler加到所有的a标签上，这个过程是昂贵的;另一方面在执行的时候也是一种浪费，因为它们都是做了同一件事却被执行了一次又一次（比如我们可以把它hook到它们的父元素上，通过冒泡可以对它们中的每一个进行区分，然后再执行这个event handler）。<br/>
优点： <br/>
·这个方法提供了一种在各种浏览器之间对事件处理的兼容性解决方案； <br/>
·非常方便简单的绑定事件到元素上； <br/>
·.click(), .hover()…这些非常方便的事件绑定，都是bind的一种简化处理方式； <br/>
·对于利用ID选出来的元素是非常好的，不仅仅是很快的可以hook上去(因为一个页面只有一个id),而且当事件发生时，handler可以立即被执行(相对于后面的live, delegate)实现方式；<br/>
缺点：<br/>
·它会绑定事件到所有的选出来的元素上； <br/>
·它不会绑定到在它执行完后动态添加的那些元素上；<br/> 
·当元素很多时，会出现效率问题； <br/>
·当页面加载完的时候，你才可以进行bind()，所以可能产生效率问题；<br/>

### .live()
.live()方法用到了事件委托的概念来处理事件的绑定。它和用.bind()来绑定事件是一样的。.live()方法会绑定相应的事件到你所选择的元素的根元素上，即是document元素上。那么所有通过冒泡上来的事件都可以用这个相同的handler来处理了。它的处理机制是这样的，一旦事件冒泡到document上，jQuery将会查找selector/event metadata,然后决定那个handler应该被调用。<br/>
$( "#members li a" ).live( "click", function( e ) {} );<br/>
不需要在每个元素上再去绑定事件，而只在document上绑定一次就可以了。尽管这个不是最快的方式，但是确实是最少浪费的。 <br/><br/>
优点： <br/>
·这里仅有一次的事件绑定，绑定到document上而不像.bind()那样给所有的元素挨个绑定；<br/> 
·那些动态添加的elemtns依然可以触发那些早先绑定的事件，因为事件真正的绑定是在document上； <br/>
·你可以在document ready之前就可以绑定那些需要的事件；<br/>
缺点：<br/> 
·从1.7开始已经不被推荐了，所以你也要开始逐步淘汰它了； <br/>
·Chaining没有被正确的支持；<br/> 
·当使用event.stopPropagation()是没用的，因为都要到达document； <br/>
·因为所有的selector/event都被绑定到document, 所以当我们使用matchSelector方法来选出那个事件被调用时，会非常慢； <br/>
·当发生事件的元素在你的DOM树中很深的时候，会有performance问题；<br/>

### .delegate() 
.delegate()有点像.live(),不同于.live()的地方在于，它不会把所有的event全部绑定到document,而是由你决定把它放在哪儿。而和.live()相同的地方在于都是用event delegation.<br/>
$( "#members" ).delegate( "li a", "click", function( e ) {} )<br/>
·可以选择把这个事件放到哪个元素上； <br/>
·jQuery仍然需要迭代查找所有的selector/event data来决定那个子元素来匹配，但是因为你可以决定放在那个根元素上，所以可以有效的减小你所要查找的元素； <br/>
·可以用在动态添加的元素上；<br/>
缺点：<br/>
·需要查找哪个元素上发生了哪个事件了，尽管比document少很多了，不过，还是得浪费时间来查找；<br/>

### .on()
其实.bind(), .live(), .delegate()都是通过.on()来实现的，.unbind(), .die(), .undelegate(),也是一样的都是通过.off()来实现的
```javascript
// Bind
$( "#members li a" ).on( "click", function( e ) {} ); 
$( "#members li a" ).bind( "click", function( e ) {} ); 
// Live
$( document ).on( "click", "#members li a", function( e ) {} ); 
$( "#members li a" ).live( "click", function( e ) {} );
// Delegate
$( "#members" ).on( "click", "li a", function( e ) {} ); 
$( "#members" ).delegate( "li a", "click", function( e ) {} );
```
优点： <br/>
·提供了一种统一绑定事件的方法； <br/>
·仍然提供了.delegate()的优点，当然如果需要你也可以直接用.bind()；<br/>

##### 总结：
用.bind()的代价是非常大的，它会把相同的一个事件处理程序hook到所有匹配的DOM元素上； <br/>
·不要再用.live()了，它已经不再被推荐了，而且还有许多问题； <br/>
·.delegate()会提供很好的方法来提高效率，同时我们可以添加一事件处理方法到动态添加的元素上； <br/>
·我们可以用.on()来代替上述的3种方法；<br/>

### 当一个DOM节点被点击时候，我们希望能够执行一个函数，应该怎么做？
（1）直接在DOM里绑定事件：\<div onclick=”test()”\>\</div\><br/>
（2）在JS里通过onclick绑定：xxx.onclick = test<br/>
（3）通过事件添加进行绑定：
btn.addEventListener(“click”,function(){<br/>
  alert(his.id);<br/>
},false);//最后的参数是true，是在捕获阶段调用，false则是在冒泡阶段调用<br/>
IE事件处理程序： <br/>
btn.attachEvent(“onclick”,function(){<br/>
alert(“clicked”);   }  );<br/>

## JavaScript的事件流模型都有什么？
“事件冒泡”：事件开始由最具体的元素接受，然后逐级向上传播<br/>
“事件捕捉”：事件由最不具体的节点先接收，然后逐级向下，一直到最具体的<br/>
“DOM事件流”：三个阶段：事件捕捉，目标阶段，事件冒泡<br/>

跨浏览器的事件绑定和解绑程序:<br/>
```javascript
addHandler:function(element,type,handler){
 if(element.addEventListener){   //removeEventListener
    element.addEventListener(type,handler,false);
}else if(element.attachEvent){    //detachEvent
    element.attachEvent(“on”+type,handler);
}else{
  element[“on”+type]=handler;      //element[“on”+type]=null;
}
}
```
## IE和DOM事件流的区别:
执行顺序不一样，参数不一样event||window.event,event.target||event.srcElement，事件类型加不加on，this指向不同（四不同）<br/>

49、http请求头，请求体，cookie在哪个里面？url在哪里面？
------
HTTP通信机制是在一次完整的HTTP通信过程中，Web浏览器与Web服务器之间将完成下列7个步骤：<br/>
（1） 建立TCP连接<br/>
（2） Web浏览器向Web服务器发送请求命令GET/sample/hello.jsp HTTP/1.1<br/>
（3） Web浏览器发送请求头信息<br/>
浏览器发送了一空白行来通知服务器，它已经结束了该头信息的发送。<br/>
（4） Web服务器应答HTTP/1.1 200 OK<br/>
应答的第一部分是协议的版本号和应答状态码<br/>
（5） Web服务器发送应答头信息<br/>
（6） Web服务器向浏览器发送数据<br/>
（7） Web服务器关闭TCP连接<br/>
HTTP请求信息由3部分组成：请求方法URI协议/版本|请求头(Request Header)|请求正文<br/>
请求头包含许多有关的客户端环境和请求正文的有用信息。例如，请求头可以声明浏览器所用的语言，请求正文的长度等。<br/>
Accept：浏览器可接受的MIME类型。 <br/>
Accept-Charset：浏览器可接受的字符集。 <br/>
Accept-Encoding：浏览器能够进行解码的数据编码方式，比如gzip。<br/>
Accept-Language：浏览器所希望的语言种类，当服务器能够提供一种以上的语言版本时要用到。<br/> 
Connection：表示是否需要持久连接。如果Servlet看到这里的值为“Keep-Alive”，或者看到请求使用的是HTTP <br/>
Content-Length：表示请求消息正文的长度。 <br/>
Cookie：设置cookie,这是最重要的请求头信息之一<br/>
Host：初始URL中的主机和端口。 <br/>
Referer：包含一个URL，用户从该URL代表的页面出发访问当前请求的页面。 <br/>
User-Agent：浏览器类型，如果Servlet返回的内容与浏览器类型有关则该值非常有用。<br/>
不同浏览器实际发送的头部有所不同，但是上面的基本上所有浏览器都会发送的<br/>
可以在调用open()和send()方法之间调用setRequestHeader()方法设置自定义的请求头信息。当是POST请求的时候，如果要表单序列化函数serialize(form)，需设置请求头的Content-type为application/x-www-form-urlencoded但是在XMLHttpRequest2级可以使用对象FormData(form)，这里则不用XHR对象设置请求头。<br/>
请求正文：<br/>
请求头和请求正文之间是一个空行，这个行非常重要，它表示请求头已经结束，接下来的是请求正文。请求正文中可以包含客户提交的查询字符串信息。<br/>
HTTP响应也由3个部分构成，分别是：协议状态版本代码描述|响应头(Response Header)|响应正文
响应头(Response Header)响应头也和请求头一样包含许多有用的信息，例如服务器类型、日期时间、内容类型和长度等：响应头和正文之间也必须用空行分隔。　　
cookie在请求头里面,url在请求头里。<br/>

50、{}=={}?   []==[]? null==undefined?
------
==， 两边值类型不同的时候，要先进行类型转换<br/>
### ===:
1)、如果类型不同，就[不相等] <br/>
2)、如果两个都是数值，并且是同一个值，那么[相等]。 <br/>
3)、如果两个都是字符串，每个位置的字符都一样，那么[相等]；否则[不相等]。 <br/>
4)、如果两个值都是true，或者都是false，那么[相等]。 <br/>
5)、如果两个值都引用同一个对象或函数，那么[相等]；否则[不相等]。 <br/>
6)、如果两个值都是null，或者都是undefined，那么[相等]。<br/> 
### ==，根据以下规则：
一、首先看双等号前后有没有NaN，如果存在NaN，一律返回false。<br/>
二、再看双等号前后有没有布尔，有布尔就将布尔转换为数字。（false是0，true是1）<br/>
三、接着看双等号前后有没有字符串, 有三种情况：<br/>
1、对方是对象，对象使用toString()或者valueOf()进行转换；<br/>
2、对方是数字，字符串转数字；（前面已经举例）<br/>
3、对方是字符串，直接比较；<br/>
4、其他返回false<br/>
四、如果是数字，对方是对象，对象取valueOf()或者toString()进行比较, 其他一律返回false<br/>
五、null, undefined不会进行类型转换, 但它们俩相等<br/>
题目结果是false  false  true<br/>

51、随鼠标移动的div
```html
<style>  
body{width:960px;margin:0 auto;}  
ul{list-style:none;width:960px;height:300px;margin-top:100px;}  
 .a{background-color:#0f0;width:200px;height:200px;float:left;margin:20px;}  
 .b{background-color:#00f;width:300px;height:300px;display:none;position: absolute;color: #fff;font-size: 120px;}  
</style> 
<body>     
<ul class="list">  
        <li><div class="a">1</div></li>  
        <li><div class="a">2</div></li>  
        <li><div class="a">3</div></li>  
</ul>  
        <div class="b">1-1</div>  
        <div class="b">2-2</div>  
        <div class="b">3-3</div>  
<script type="text/javascript">  
    $('.b').hide();  
    $('.list li').mousemove(function(e){  
        $('.b').eq($(this).index()).show().css({  
            "top": e.pageY+20,  
            "left": e.pageX+20  
        }).siblings("div").hide();  
    });  
    $('.list li').mouseleave(function(){  
        $('.b').hide();  
    });  
</script>  
</body>  
```

52、浏览器的垃圾回收机制
------
垃圾收集器必须跟踪哪个变量有用哪个变量没用，对于不再有用的变量打上标记，以备将来收回其占用的内存，内存泄露和浏览器实现的垃圾回收机制息息相关， 而浏览器实现标识无用变量的策略主要有下两个方法：<br/>
##### 第一，引用计数法<br/>
跟踪记录每个值被引用的次数。当声明一个变量并将引用类型的值赋给该变量时，则这个值的引用次数就是1。如果同一个值又被赋给另一个变量，则该值的引用次数加1.相反，如果包含对这个值引用的变量又取得另外一个值，则这个值的引用次数减1.当这个值的引用次数变成0时，则说明没有办法访问这个值了，因此就 可以将其占用的内存空间回收回来。<br/>
var a = {};     //对象{}的引用计数为1<br/>
b = a;          //对象{}的引用计数为 1+1 <br/>
a = null;       //对象{}的引用计数为2-1<br/>
所以这时对象{}不会被回收;
IE 6, 7 对DOM对象进行引用计数回收，这样简单的垃圾回收机制，非常容易出现循环引用问题导致内存不能被回收，进行导致内存泄露等问题，一般不用引用计数法。<br/>
##### 第二，标记清除法<br/>
到2008年为止，IE,Firefox,Opera,Chrome和Safari的javascript实现使用的都是标记清除式的垃圾收集策略（或类似的策略），只不过垃圾收集的时间间隔互有不同。<br/>
标记清除的算法分为两个阶段，标记(mark)和清除(sweep). 第一阶段从引用根节点开始标记所有被引用的对象，第二阶段遍历整个堆，把未标记的对象清除。标记清除(mark and sweep)是JavaScript最常见的垃圾回收方式，当变量进入执行环境的时候，比如函数中声明一个变量，垃圾回收器将其标记为“进入环境”，当变量离开环境的时候(函数执行结束)将其标记为“离开环境”。<br/>
垃圾回收器会在运行的时候给存储在内存中的所有变量加上标记，然后去掉环境中的变量以及被环境中变量所引用的变量(闭包)，在这些完成之后仍存在标记的就是要删除的变量了。<br/>
在IE中虽然JavaScript对象通过标记清除的方式进行垃圾回收，但BOM与DOM对象却是通过引用计数回收垃圾的，也就是说只要涉及BOM及DOM就会出现循环引用问题。<br/>

53、null和undefined的区别？
-------
null是一个表示"无"的对象，转为数值时为0；undefined是一个表示"无"的原始值，转为数值时为NaN。<br/>
当声明的变量还未被初始化时，变量的默认值为undefined。 null用来表示尚未存在的对象，常用来表示函数企图返回一个不存在的对象。<br/>
undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。典型用法是：<br/>
（1） 变量被声明了，但没有赋值时，就等于undefined。<br/>
（2） 调用函数时，应该提供的参数没有提供，该参数等于undefined。<br/>
（3） 对象没有赋值的属性，该属性的值为undefined。<br/>
（4） 函数没有返回值时，默认返回undefined。<br/>
null表示"没有对象"，即该处不应该有值。典型用法是：<br/>
（1） 作为函数的参数，表示该函数的参数不是对象。<br/>
（2） 作为对象原型链的终点。<br/>

54、什么叫优雅降级和渐进增强？
------
优雅降级：Web站点在所有新式浏览器中都能正常工作，如果用户使用的是老式浏览器，则代码会检查以确认它们是否能正常工作。由于IE独特的盒模型布局问题，针对不同版本的IE的hack实践过优雅降级了,为那些无法支持功能的浏览器增加候选方案，使之在旧式浏览器上以某种形式降级体验却不至于完全失效。<br/>
渐进增强：从被所有浏览器支持的基本功能开始，逐步地添加那些只有新式浏览器才支持的功能,向页面增加无害于基础浏览器的额外样式和功能的。当浏览器支持时，它们会自动地呈现出来并发挥作用。<br/>

55、对Node的优点和缺点提出了自己的看法？
------
优点：<br/>
1.因为Node是基于事件驱动和无阻塞的，所以非常适合处理并发请求，因此构建在Node上的代理服务器相比其他技术实现（如Ruby）的服务器表现要好得多。<br/>
2.与Node代理服务器交互的客户端代码是由javascript语言编写的，因此客户端和服务器端都用同一种语言编写，这是非常美妙的事情。<br/>
缺点：<br/>
1.Node是一个相对新的开源项目，所以不太稳定，它总是一直在变。<br/>
2.缺少足够多的第三方库支持。看起来，就像是Ruby/Rails当年的样子（第三方库现在已经很丰富了，所以这个缺点可以说不存在了）。<br/>

56、平时如何管理你的项目？
------
先期团队必须确定好全局样式（globe.css），编码模式(utf-8) 等；<br/>
编写习惯必须一致（例如都是采用继承式的写法，单样式都写成一行）；<br/>
标注样式编写人，各模块都及时标注（标注关键样式调用的地方）；<br/>
页面进行标注（例如 页面 模块 开始和结束）；<br/>
CSS跟HTML 分文件夹并行存放，命名都得统一（例如style.css）；<br/>
JS 分文件夹存放 命名以该JS功能为准的英文翻译。
图片采用整合的 images.png png8 格式文件使用 尽量<br/>整合在一起使用方便将来的管理<br/>

57、异步加载和延迟加载
------
1).异步加载的方案： 动态插入script标签<br/>
2)).通过ajax去获取js代码，然后通过eval执行<br/>
3).script标签上添加defer或者async属性<br/>
4).创建并插入iframe，让它异步执行js<br/>
5).延迟加载：有些 js 代码并不是页面初始化的时候就立刻需要的，而稍后的某些情况才需要的。<br/>

58、前端安全问题？
-----
（XSS，sql注入，CSRF）<br/>
### sql注入原理
就是通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令。<br/>
总的来说有以下几点：<br/>
1).永远不要信任用户的输入，要对用户的输入进行校验，可以通过正则表达式，或限制长度，对单引号和双"-"进行转换等。 <br/>
2).永远不要使用动态拼装SQL，可以使用参数化的SQL或者直接使用存储过程进行数据查询存取。 <br/>
3).永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。<br/> 
4).不要把机密信息明文存放，请加密或者hash掉密码和敏感的信息。 <br/>
### XSS原理及防范
Xss(cross-site ing)攻击指的是攻击者往Web页面里插入恶意html标签或者java代码。比如：攻击者在论坛中放一个看似安全的链接，骗取用户点击后，窃取cookie中的用户私密信息；或者攻击者在论坛中加一个恶意表单， 当用户提交表单的时候，却把信息传送到攻击者的服务器中，而不是用户原本以为的信任站点。<br/>
XSS防范方法:<br/>
1).代码里对用户输入的地方和变量都需要仔细检查长度和对”<”,”>”,”;”,”’”等字符做过滤；其次任何内容写到页面之前都必须加以encode，避免不小心把html tag 弄出来。这一个层面做好，至少可以堵住超过一半的XSS 攻击。<br/>
2).避免直接在cookie 中泄露用户隐私，例如email、密码等等。<br/>
3).通过使cookie 和系统ip 绑定来降低cookie 泄露后的危险。这样攻击者得到的cookie 没有实际价值，不可能拿来重放。<br/>
4).尽量采用POST 而非GET 提交表单。<br/>
### CSRF：
是跨站请求伪造，很明显根据刚刚的解释，他的核心也就是请求伪造，通过伪造身份提交POST和GET请求来进行跨域的攻击。<br/>
CSRF的防御:<br/>
1).服务端的CSRF方式方法很多样，但总的思想都是一致的，就是在客户端页面增加伪随机数。 <br/>
2).使用验证码<br/>
## XSS与CSRF有什么区别吗？
XSS是获取信息，不需要提前知道其他用户页面的代码和数据包。CSRF是代替用户完成指定的动作，需要知道其他用户页面的代码和数据包。<br/>
要完成一次CSRF攻击，受害者必须依次完成两个步骤：<br/>
1).登陆受信任的网站A，在本地生成COOKIE<br/>
2).在不登出A的情况下，或者本地COOKIE没有过期的情况下，主动访问危险网站B。<br/>
POST方式可以解决大部分的CSRF问题,但是还是不安全的，根本原因是：服务器无法识别你的来源是否可靠。<br/>
###### 防御CSRF：
1)加验证码<br/>
2)进行二次验证<br/>
3)确认来源是否可靠：验证HTTP Referer 字段（但是主动点击被认为是安全的，比如说扣扣发送的链接）；服务端验证请求的token一致性：token是CSRF不可伪造的东西。在服务端生成一个随机的token，加入到HTTP请求参数中，服务器拦截请求，查看发送的token和服务端的是否一致，若一致，则允许请求。<br/>

59、ie各版本和chrome可以并行下载多少个资源
--------
IE6 两个并发，iE7升级之后的6个并发，之后版本也是6个<br/>
Firefox，chrome也是6个<br/>

60、grunt， YUI compressor 和 google clojure用来进行代码压缩的用法。
------
##### grunt：<br/>
UglifyJS 是基于 NodeJS 的 Javascript 语法解析/压缩/格式化工具<br/>
官网：http://lisperator.net/uglifyjs/ 或者 https://github.com/mishoo/UglifyJS2<br/>
安装：<br/>
$ npm install uglify-js -g<br/>
##### YUI compressor：<br/>
YUI Compressor 是一个用来压缩 JS 和 CSS 文件的工具，采用Java开发。<br/>
使用方法：<br/>
```javascript
// 压缩JS
java -jar yuicompressor-2.4.2.jar --type js --charset utf-8 -v src.js > packed.js
// 压缩CSS
java -jar yuicompressor-2.4.2.jar --type css --charset utf-8 -v src.css > packed.css
```
##### Google Closure Compiler：
官网：https://developers.google.com/closure/compiler/<br/>
使用方法：<br/>
1）在命令行下使用一个google编译好的java程序<br/>
2）使用google提供的在线服务<br/>
3）使用google提供的RESTful API<br/>

61、请解释一下 JavaScript 的同源策略。
------
概念:同源策略是客户端脚本（尤其是Javascript）的重要的安全度量标准。它最早出自Netscape Navigator2.0，其目的是防止某个文档或脚本从多个不同源装载。这里的同源策略指的是：协议，域名，端口相同，同源策略是一种安全协议。 指一段脚本只能读取来自同一来源的窗口和文档的属性。<br/>

## 为什么要有同源限制？
我们举例说明：比如一个黑客程序，他利用Iframe把真正的银行登录页面嵌到他的页面上，当你使用真实的用户名，密码登录时，他的页面就可以通过Javascript读取到你的表单中input中的内容，这样用户名，密码就轻松到手了。<br/>

62、哪些地方会出现css阻塞，哪些地方会出现js阻塞？
------
js的阻塞特性：所有浏览器在下载JS的时候，会阻止一切其他活动，比如其他资源的下载，内容的呈现等等。直到JS下载、解析、执行完毕后才开始继续并行下载其他资源并呈现内容。为了提高用户体验，新一代浏览器都支持并行下载JS，但是JS下载仍然会阻塞其它资源的下载（例如.图片，css文件等）。<br/>
由于浏览器为了防止出现JS修改DOM树，需要重新构建DOM树的情况，所以就会阻塞其他的下载和呈现。<br/>
嵌入JS会阻塞所有内容的呈现，而外部JS只会阻塞其后内容的显示，2种方式都会阻塞其后资源的下载。也就是说外部样式不会阻塞外部脚本的加载，但会阻塞外部脚本的执行。<br/>

## CSS怎么会阻塞加载了？CSS本来是可以并行下载的，在什么情况下会出现阻塞加载了(在测试观察中，IE6下CSS都是阻塞加载）
当CSS后面跟着嵌入的JS的时候，该CSS就会出现阻塞后面资源下载的情况。而当把嵌入JS放到CSS前面，就不会出现阻塞的情况了。<br/>
根本原因：因为浏览器会维持html中css和js的顺序，样式表必须在嵌入的JS执行前先加载、解析完。而嵌入的JS会阻塞后面的资源加载，所以就会出现上面CSS阻塞下载的情况。<br/>
嵌入JS应该放在什么位置？<br/>
1)、放在底部，虽然放在底部照样会阻塞所有呈现，但不会阻塞资源下载。<br/>
2)、如果嵌入JS放在head中，请把嵌入JS放在CSS头部。<br/>
3)、使用defer（只支持IE）<br/>
4)、不要在嵌入的JS中调用运行时间较长的函数，如果一定要用，可以用`setTimeout`来调用setTimeout()无论设置的时间是多少，都会等其他的执行结束后，才会执行<br/>
js 是运行于单线程环境中，定时器作用是在规定时间内将事件加入执行队列，而加入的前提是当前事件队列没有任何东西<br/>

回调的运行机制：<br/>
回调时，被回调的函数会被放在event loop里，等待线程里的所有任务执行完后才执行event loop里的代码。<br/>

## Javascript无阻塞加载具体方式
  ● 将脚本放在底部。\<link\>还是放在head中，用以保证在js加载前，能加载出正常显示的页面。\<script\>标签放在\</body\>前。<br/>
  ● 成组脚本：由于每个\<script\>标签下载时阻塞页面解析过程，所以限制页面的\<script\>总数也可以改善性能。适用于内联脚本和外部脚本。<br/>
  ● 非阻塞脚本：等页面完成加载后，再加载js代码。也就是，在window.onload事件发出后开始下载代码。 <br/>
  （1）defer属性：支持IE4和fierfox3.5更高版本浏览器。<br/>
  （2）动态脚本元素：文档对象模型（DOM）允许你使用js动态创建HTML的几乎全部文档内容。代码如下：<br/>
```javascript
<script>
var script=document.createElement("script");
script.type="text/javascript";
script.src="file.js";
document.getElementsByTagName("head")[0].appendChild(script);
</script>
```
此技术的重点在于：无论在何处启动下载，文件额下载和运行都不会阻塞其他页面处理过程。即使在head里，（除了用于下载文件的http链接）

63、说说你对Promise的理解?
------
ES6 原生提供了 Promise 对象。<br/>
所谓 Promise，就是一个对象，用来传递异步操作的消息。它代表了某个未来才会知道结果的事件(通常是一个异步操作)，并且这个事件提供统一的 API，可供进一步处理。Promise 对象有以下两个特点。<br/>
(1)、对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：Pending(进行中)、Resolved(已完成，又称 Fulfilled)和 Rejected(已失败)。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 这个名字的由来，它的英语意思就是「承诺」，表示其他手段无法改变。<br/>
(2)、一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 Pending 变为 Resolved 和从 Pending 变为 Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件(Event)完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。<br/>
有了 Promise 对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise 对象提供统一的接口，使得控制异步操作更加容易。
Promise 也有一些缺点。首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。第三，当处于 Pending 状态时，无法得知目前进展到哪一个阶段(刚刚开始还是即将完成)。<br/>

64、写一个通用的事件侦听器函数?
------
```javascript
markyun.Event = {
    // 页面加载完成后
     readyEvent : function(fn) {
        if (fn==null) {
             fn=document;
        }
        var oldonload = window.onload;
        if (typeof window.onload != 'function') {
             window.onload = fn;
        } else {
             window.onload = function() {
                oldonload();
                fn();
             };
        }
     },
     // 视能力分别使用dom0||dom2||IE方式 来绑定事件
     // 参数： 操作的元素,事件名称 ,事件处理程序
     addEvent : function(element, type, handler) {
         if (element.addEventListener) {
            //事件类型、需要执行的函数、是否捕捉
            element.addEventListener(type, handler, false);
         } else if (element.attachEvent) {
            element.attachEvent('on' + type, function() {
                handler.call(element);
            });
         } else {
            element['on' + type] = handler;
         }
        },
        // 移除事件
        removeEvent : function(element, type, handler) {
            if (element.removeEnentListener) {
                element.removeEnentListener(type, handler, false);
            } else if (element.datachEvent) {
                element.detachEvent('on' + type, handler);
            } else {
                element['on' + type] = null;
            }
        }, 
        // 阻止事件 (主要是事件冒泡，因为IE不支持事件捕获)
        stopPropagation : function(ev) {
            if (ev.stopPropagation) {
                ev.stopPropagation();
            } else {
                ev.cancelBubble = true;
            }
        },
        // 取消事件的默认行为
        preventDefault : function(event) {
            if (event.preventDefault) {
                event.preventDefault();
            } else {
                event.returnValue = false;
            }
        },
        // 获取事件目标
        getTarget : function(event) {
            return event.target || event.srcElement;
        },
        // 获取event对象的引用，取到事件的所有信息，确保随时能使用event；
        getEvent : function(e) {
            var ev = e || window.event;
            if (!ev) {
                var c = this.getEvent.caller;
                while (c) {
                    ev = c.arguments[0];
                    if (ev && Event == ev.constructor) {
                        break;
                    }
                    c = c.caller;
                }
            }
            return ev;
        }
    };
```
65、Node.js的适用场景？
------
高并发、聊天、实时消息推送。<br/>

66、页面重构怎么操作？
-------
编写 CSS、让页面结构更合理化，提升用户体验，实现良好的页面效果和提升性能。<br/>

67、如何获取UA？
------
```javascript
<script> 
    function whatBrowser() {  
        document.Browser.Name.value=navigator.appName;  
        document.Browser.Version.value=navigator.appVersion;  
        document.Browser.Code.value=navigator.appCodeName;  
        document.Browser.Agent.value=navigator.userAgent;  
    }  
</script>
```

68、cache-control
------
网页的缓存是由HTTP消息头中的“Cache-control”来控制的，常见的取值有private、no-cache、max-age、must-revalidate等，默认为private。Expires 头部字段提供一个日期和时间，响应在该日期和时间后被认为失效。允许客户端在这个时间之前不去检查（发请求），等同max-age的效果。但是如果同时存在，则被Cache-Control的max-age覆盖。<br/>
Expires = "Expires" ":" HTTP-date<br/>
例如：<br/>
Expires: Thu, 01 Dec 1994 16:00:00 GMT （必须是GMT格式）<br/>
如果把它设置为-1，则表示立即过期<br/>
Expires和max-age都可以用来指定文档的过期时间，但是二者有一些细微差别<br/>
1).Expires在HTTP/1.0中已经定义，Cache-Control:max-age在HTTP/1.1中才有定义，为了向下兼容，仅使用max-age不够；<br/>
2).Expires指定一个绝对的过期时间(GMT格式),这么做会导致至少2个问题：(1、客户端和服务器时间不同步导致Expires的配置出现问题。(2、很容易在配置后忘记具体的过期时间，导致过期来临出现浪涌现象；<br/>
3).max-age 指定的是从文档被访问后的存活时间，这个时间是个相对值(比如:3600s),相对的是文档第一次被请求时服务器记录的Request_time(请求时间)<br/>
4).Expires指定的时间可以是相对文件的最后访问时间(Atime)或者修改时间(MTime),而max-age相对对的是文档的请求时间(Atime)。如果值为no-cache,那么每次都会访问服务器。如果值为max-age,则在过期之前不会重复访问服务器。<br/>

69、js操作获取和设置cookie
-----
```javascript
//创建cookie
function setCookie(name, value, expires, path, domain, secure) {
    var cookieText = encodeURIComponent(name) + '=' + encodeURIComponent(value);
    if (expires instanceof Date) {
        cookieText += '; expires=' + expires;
    }
    if (path) {
        cookieText += '; path=' + path;
    }
    if (domain) {
        cookieText += '; domain=' + domain;
    }
    if (secure) {
        cookieText += '; secure';
    }
    document.cookie = cookieText;
}
//获取cookie
function getCookie(name) {
    var cookieName = encodeURIComponent(name) + '=';
    var cookieStart = document.cookie.indexOf(cookieName);
    var cookieValue = null;
    if (cookieStart > -1) {
        var cookieEnd = document.cookie.indexOf(';', cookieStart);
        if (cookieEnd == -1) {
            cookieEnd = document.cookie.length;
        }
        cookieValue = decodeURIComponent(document.cookie.substring(cookieStart + cookieName.length, cookieEnd));
    }
    return cookieValue;
}
//删除cookie
function unsetCookie(name) {
    document.cookie = name + "= ; expires=" + new Date(0);
}
```

70、jQuery中attr()、prop()、data()用法及区别？
------
从性能上对比，.prop() > .data() > .attr()。<br/>
attr返回属性的值（标签自带属性和自定意属性都可以返回）<br/>
prop返回true或false（只能返回标签自带属性，不能返回自定义属性）<br/>
data向被选元素附加数据，或者从被选元素获取数据（即H5的自定义属性）<br/>
attribute表示HTML文档节点属性，property表示JS对象的属性。<br/>
<!-- 这里的id、class、data_id均是该元素文档节点的attribute --><br/>
\<div id="message"class="test" data_id="123"\>\</div\><br/>
// 这里的name、age、url均是obj的property<br/>
var obj ={ name: "CodePlayer", age: 18, url:"http://www.365mini.com/" };<br/>
prop()的设计目标用于设置或获取指定DOM元素(指的是JS对象，Element类型)上的属性(property);<br/>
attr()的设计目标是用于设置或获取指定DOM元素所对应的文档节点上的属性(attribute)。<br/>
在html5中DOM标签可以添加一些data-xxx的属性，可以把data()看作是存取data-xxx这样的DOM附加信息的方法。data()存取的内容可以是字符串、数组和对象。<br/>
\<div data-role="page" data-last-value="43"data-hidden="true"\>\</div\><br/>

71、正则表达式验证邮箱，电话号码
-------
验证邮箱：re =/^(\w-*\.*)+@(\w-?)+(\.\w{2,})+$/<br/>
验证电话号码：区号+号码，区号以0开头，3位或4位；号码由7位或8位数字组成；区号与号码之间可以无连接符，也可以“-”连接： re = /^0\d{2,3}-?\d{7,8}$/;<br/>

72、javascript的本地对象，内置对象和宿主对象
-------
本地对象为array object regexp等可以new实例化,ECMA定义好的对象，是引用类型。<br/>
内置对象是本地对象的一种，只有global 和Math<br/>
宿主为浏览器自带的document,window 等，所有的BOM 和DOM对象。<br/>

72、5个技巧避免不必要的浏览器兼容性问题
-------
1). CSS3 风格的前缀<br/>
如果你正在使用最新的 CSS 代码，比如 box-sizing，或者 background-clip等，确保你使用了合适的供应商前缀。<br/>
```css
-moz- /* Firefox 和其他使用 Mozilla 浏览器引擎的浏览器 */
-webkit- /* Safari，Chrome 和其他使用了 Webkit 引擎的浏览器 */
-o- /* Opera */
-ms- /* IE 浏览器（但不总是 IE） */
```
2). 使用样式重置<br/>
你可以使用 normalize.css 或者其他从网络上能找到的样式重置都可以。这里我给出一个，来自于 Genesis 框架。<br/>
```css
html,body,div,span,applet,object,iframe,h1,h2,
h3,h4,h5,h6,p,blockquote,a,abbr,acronym,address,
big,cite,del,dfn,em,img,ins,kbd,q,s,samp,small,
strike,strong,sub,sup,tt,var,b,u,i,center,dl,dt,
dd,ol,ul,li,fieldset,form,label,legend,table,caption,
tbody,tfoot,thead,tr,th,td,article,aside,canvas,details,
embed,figure,figcaption,footer,header,hgroup,input,menu,
nav,output,ruby,section,summary,time,mark,audio,video {
border: 0;
margin: 0;
padding: 0;
vertical-align: baseline;
}
```
3). 避免 padding 和 width 一起使用<br/>
当你给一个包含 width 的元素加 padding，那它实际显示的要比本应显示的大。因为 width 和 padding 会加到一起。比如一个元素 width 是 100px，又给它加了一个 10px 的 padding。那某些浏览器会将该元素显示成 120px。<br/>
为了 fix 这个问题，像下面这样做：<br/>
```css
*{ 
-webkit-box-sizing: border-box; /* Safari/Chrome 等 WebKit 内核浏览器 */
-moz-box-sizing: border-box; /* Firefox 等 Gecko 内核浏览器 */
box-sizing: border-box; 
}
```
4). 清理浮动<br/>
确保你把浮动都清理掉了，如果不清理掉，可能会出现很奇怪的情况。想要了解更多关于浏览器处理浮动的原理，可以看 Chris Coyier 的这篇文章。可以用下面 CSS 代码清理浮动：<br/>
```css
.parent-selector:after {
    content: "";
    display: table;
    clear: both;
}
```
如果你要把你的大部分代码都包起来，有个更简单的方法就是把它添加到你的 wrap 类里面：<br/>
```css
.wrap:after {
    content: "";
    display: table;
    clear: both;
}
```
这样你的浮动就被清理掉了。<br/>
5). 测试一下<br/>
搭建你自己的跨浏览器测试环境，或者用 Endtest 也可以。<br/>

72、Object.is() 与原来的比较操作符“ ===”、“ ==”的区别？
------
两等号判等，会在比较时进行类型转换；<br/>
三等号判等(判断严格)，比较时不进行隐式类型转换,（类型不同则会返回false）； <br/>
Object.is 在三等号判等的基础上特别处理了 NaN 、-0 和 +0 ，保证 -0 和 +0 不再相同，<br/>
但 Object.is(NaN, NaN) 会返回 true.<br/>
Object.is 应被认为有其特殊的用途，而不能用它认为它比其它的相等对比更宽松或严格。<br/>
