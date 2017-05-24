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

优点：极高的扩展性和可用性      
1）通过良好的编程，控制保存在cookie中的session对象的大小。    
2）通过加密和安全传输技术（SSL），减少cookie被破解的可能性。   
3）只在cookie中存放不敏感数据，即使被盗也不会有重大损失。    
4）控制cookie的生命期，使之不会永远有效。偷盗者很可能拿到一个过期的cookie。    

缺点：    
1）Cookie数量和长度的限制。每个domain最多只能有20条cookie，每个cookie长度不能超过4KB，否则会被截掉。      
2）安全性问题。如果cookie被人拦截了，那人就可以取得所有的session信息。即使加密也与事无补，因为拦截者并不需要知道cookie的意义，他只要原样转发cookie就可以达到目的了。    
3）有些状态不可能保存在客户端。例如，为了防止重复提交表单，我们需要在服务器端保存一个计数器。如果我们把这个计数器保存在客户端，那么它起不到任何作用。
所以为了绕开cookie个数限制，一些开发人员使用了子cookie，使用cookie存储多个名值对，用&连接。   

cookie的构造：       
cookie的名称不区分大小写，可以设置cookie域，路径，失效时间（没定义的话是会话结束失效），安全标志（SSL）e.g.Set-Cookie:name-value;domain=.wrox.com;path=/;secure，指定在.erox.com域或子域下的所有路径发送https请求时都会带上cookie。都会进行URL编码。    

web storage和cookie的区别：    
Web Storage的概念和cookie相似，区别是它是为了更大容量存储，在cookie之外存储会话数据的途径设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。除此之外，Web Storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。      

Web Storage定义了两种用于存储数据的对象，sessionStorage和localStorage，前者严格用于在一个浏览器会话中存储数据，浏览器关闭后立即删除，后者用户跨会话持久化数据并遵循跨域安全策略。要访问同一个localStorage对象，页面必须来自同一个域名（子域名无效），同一种协议，同一端口。    

但是Cookie也是不可以或缺的：      
Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。浏览器的支持除了IE７及以下不支持外，其他标准浏览器都完全支持(ie及FF需在web服务器里运行)，值得一提的是IE总是办好事，例如IE7、IE6中的UserData（可以应用在页面的某个元素上，每个文档最多128KB，每个域名最多1M，在使用userData之前需要再DOM元素上使用style的behavior属性，通过setAttribute()保存数据，最后必须使用save(数据空间名称)指定数据空间名称，最后就可以使用load(数据空间名称)方法指定同样的数据空间名称来获取数据，每次添加或者删除后都要使用save进行提交更改，用户数据会跨越会话永久存在，所以需要使用removeAttribute删除释放，也不安全）其实就是javascript本地存储的解决方案。所以通过简单的代码封装可以统一到所有的浏览器都支持web storage。很容就能够兼容。    

在较高版本的浏览器（IE8+）中，js提供了sessionStorage和globalStorage。在HTML5中提供了localStorage来取代globalStorage。html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。    
localStorage和sessionStorage都具有相同的操作方法，例如（setItem、getItem）（也可以用点的方式存取）和removeItem,key(index)：获得index位置处的值的名字等，也可以通过Storage对象间接调用，因为localStorage，sessionStorage都是Storage对象的实例。     

sessionStorage：       
可以跨页面刷新存在，浏览器崩溃后重启依然可用（IE不行）只能由最初给对象存储数据的页面可以访问，所以对多页应用有限制。IE8可以通过这个对象将数据写入磁盘，设置数据之前使用sessionStorage.begin()。成功之后使用sessionStorage.commit()，主要用于仅针对会话的小段数据存储，如果需要跨越会话存储数据，用localStorage或globalStorage更合适。

globalStorage：      
跨越会话存储数据，globalStorage不是Storage实例，使用globalStorage["wrox.com"]确定针对特定域名的存储空间，globalStorage["wrox.com"]是Storage实例，也就是该域可以访问存储在上面的数据。同时也要遵从类似同源策略的规则设置和访问。没有使用removeItem()或者delete删除，或者用户没有清除浏览器缓存，globalStorage中存储的数据会一直保存在磁盘上，适合存储文档或用户偏好设置。     

localStorage：     
是Storage实例，该对象在HTML5规范中作为持久保存客户端的数据的方案取代了globalStorage。访问规则事先也设定，相当于globalStorage[location.host]。    
对localStorage，globalStorage，sessionStorage进行操作都会触发storage事件。    

本地存储和事务存储之间的区别:    
本地存储数据持续永久，但是会话在浏览器打开时有效知道浏览器关闭时会话变量重置    

6、HTML5的离线储存怎么使用，工作原理能不能解释一下？
--------
离线检测：       
HTML5定义了一个navigator.onLine属性，ture表示能上网，由于浏览器兼容问题，配合使用online和offline事件，当网络从在线转为离线触发offline，转为在线触发online，都在window对象上触发。      

离线储存：    
所谓的离线存储就是将一些资源文件保存在本地，这样后续的页面加载将使用本地的资源文件，在 离线的情况下可以继续访问web应用。在用户与因特网连接时，更新用户机器上的缓存文件。   
原理：HTML5的离线存储是基于一个新建的.appcache文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像cookie一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。    
```javascript
localStorage 长期存储数据，浏览器关闭后数据不丢失；   
localStorage.setItem("key","value");//数据添加到本地存储采用键值对,value也可以是一个js对象。     localStorage.setItem(“I001”,JSON.stringify(country));//也可以存储json格式的数据   
var item = localStorage.getItem("key");//从本地存储中检索数据  
```
本地存储没有生命周期，它将保留直到用户从浏览器清除或者使用Javascript代码移除。
sessionStorage 数据在浏览器关闭后自动删除。    
如何使用：       
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

HTML5中的应用缓存
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

浏览器是怎么对HTML5的离线储存资源进行管理和加载    
在线的情况下，浏览器发现html头部有manifest属性，它会请求manifest文件，如果是第一次访问app，那么浏览器就会根据manifest文件的内容下载相应的资源并且进行离线存储。如果已经访问过app并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的manifest文件与旧的manifest文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。    
离线的情况下，浏览器就直接使用离线存储的资源。      
步骤：     
1）html5是使用一个manifest文件来标明那些文件是需要被存储，对于manifest文件，文件的mime-type必须是text/cache-manifest类型。       
2）cache manifest下直接写需要缓存的文件，这里指明文件被缓存到浏览器本地；在network下指明的文件，强制必须通过网络资源获取的；在failback下指明是一种失败的回调方案，比如无法访问，就发出404.htm请求。           

6、iframe有那些优缺点？ 
----------
优点：     
1）解决加载缓慢的第三方内容如图标和广告等的加载问题    
2）Security sandbox    
3）并行加载脚本    

缺点：       
1）在网页中使用框架结构最大的弊病是搜索引擎的检索程序无法解读这种页面，不利于SEO;     
2）iframe会阻塞主页面的Onload事件；     
3）iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。即使内容为空，加载也需要时间。     
4）框架结构有时会让人感到迷惑，页面很混乱，没有语义。
使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题。   
