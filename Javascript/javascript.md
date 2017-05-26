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

cookie，localStorage和sessionStorage有什么区别？
------
共同点：都是保存在浏览器端，且同源的。<br/>
区别：<br/>
1）cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。<br/>
2）cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。<br/>
3）存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。<br/>
4）数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。<br/>
5）作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。<br/>

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

7、什么是闭包？闭包有什么好处？为什么要用它？使用闭包要注意什么？
----------
闭包：闭包是指有权访问另一个函数作用域中变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，通过另一个函数访问这个函数的局部变量,利用闭包可以突破作用链域，将函数内部的变量和参数传递到外部。该变量和参数不会被垃圾回收机制所回收<br/>
好处 ：<br/>
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

闭包的原理和应用
--------
闭包就是能够读取其他函数内部变量的函数。<br/>
当回调发生时，闭包能记住它原来所在的执行上下文<br/>
它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。<br/>
1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。<br/>
2）闭包会在父函数外部，改变父函数内部变量的值。<br/>

闭包的用途：
-------
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
    return function function(n){
        if(n==0){
            return 0;
        }else if(n==1){
            return 1;
        }
        for(var i=1;i<=n;i++){
            sum=pre+next;
            pre=next;
            next=sum;
        }
        return sum;
    }
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
Javascript如何实现继承？
-------
1）、构造继承<br/>
2）、原型继承<br/>
3）、实例继承<br/>
4）、拷贝继承<br/>
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

原理：<br/>
(1)创建XMLHttpRequest对象,也就是创建一个异步调用对象<br/>
var xhr = new XMLHttpRequest();<br/>
(2)创建一个新的HTTP请求,并指定该HTTP请求的方法、URL及验证信息<br/>
xhr.open('GET', 'example.txt', true);<br/>
(3)设置响应HTTP请求状态变化的函数<br/>
xhr.onreadystatechange =function(){}<br/>
 1)当readystate值从一个值变为另一个值时，都会触发readystatechange事件。<br/>
 2)当readystate==4时，表示已经接收到全部响应数据。<br/>
 3)当status ==200时，表示服务器成功返回页面和数据。<br/>
(4)发送HTTP请求<br/>
xhr.send(); 发送请求到服务器<br/>
(5)获取异步调用返回的数据<br/>
xhr.responseText<br/>
(6)使用JavaScript和DOM实现局部刷新<br/>

优势:<br/>
1）通过异步模式，提升了用户体验<br/>
2） 优化了浏览器和服务器之间的传输，减少不必要的数据往返，减少了带宽占用<br/>
3） Ajax在客户端运行，承担了一部分本来由服务器承担的工作，减少了大用户量下的服务器负载。<br/>

Ajax的最大的特点是什么:<br/>
Ajax可以实现动态不刷新（局部刷新）<br/>
readyState属性 状态 有5个可取值： 0=未初始化 ，1=启动 2=发送，3=接收，4=完成<br/>

Ajax 同步和异步的区别:<br/>
1）同步：提交请求 -> 等待服务器处理 -> 处理完毕返回，这个期间客户端浏览器不能干任何事<br/>
2）异步：请求通过事件触发 -> 服务器处理（这是浏览器仍然可以作其他事情）-> 处理完毕<br/>
ajax.open方法中，第3个参数是设同步或者异步<br/>

ajax的缺点:<br/>
1）ajax不支持浏览器back按钮。<br/>
back和history存在的根本就是url的改变，ajax并没有改变url，用户单击后退按钮访问历史记录时，通过创建或使用一个隐藏的IFRAME来重现页面上的变更。（例如，当用户在Google Maps中单击后退时，它在一个隐藏的IFRAME中进行搜索，然后将搜索结果反映到Ajax元素上，以便将应用程序状态恢复到当时的状态。）但是它所带来的开发成本是非常高的，和ajax框架所要求的快速开发是相背离的。这是ajax所带来的一个非常严重的问题。<br/>
2）安全问题 AJAX暴露了与服务器交互的细节。<br/>
ajax技术就如同对企业数据建立了一个直接通道。这使得开发者在不经意间会暴露比以前更多的数据和服务器逻辑。ajax的逻辑可以对客户端的安全扫描技术隐藏起来，允许黑客从远端服务器上建立新的攻击。还有ajax也难以避免一些已知的安全弱点，诸如跨站点脚本攻击、SQL注入攻击和基于credentials的安全漏洞等。<br/>
3）对搜索引擎的支持比较弱。<br/>
搜索引擎在抓取页面的时候会屏蔽所有的JavaScript代码，而基于ajax技术的web站点所用的到的很重要的技术就是js代码，这样一来ajax载入的内容相对于搜索引擎就是透明的不利于各大搜索引擎的搜索。<br/>
4）破坏了程序的异常机制。<br/>
5）不容易调试。<br/>
跨域： jsonp、 iframe、window.name、window.postMessage、服务器上设置代理页面<br/>

27、Ajax 解决浏览器缓存问题？
------
确保ajax 或连接不走缓存路径：<br/>
1）、在ajax发送请求前加上 anyAjaxObj.setRequestHeader("If-Modified-Since","0")。<br/>
2）、在ajax发送请求前加上 anyAjaxObj.setRequestHeader("Cache-Control","no-cache")。<br/>
3）、在URL后面加上一个随机数： "fresh=" + Math.random();。<br/>
4）、在URL后面加上时间搓："nowtime=" + new Date().getTime();。<br/>
5）、如果是使用jQuery，直接这样就可以了 $.ajaxSetup({cache:false})。这样页面的所有ajax都会执行这条语句就是不需要保存缓存记录。<br/>

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
