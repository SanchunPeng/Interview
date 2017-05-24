什么是Web Workers？为什么我们需要他们？
-------
循环代码在HTML按钮点击以后执行，这个方法执行是同步的，换句话说这个浏览器必须等到循环完成才能操作，这个会进一步导致浏览器冻结并且没有响应。     
如果你能移动这些繁重的循环到Javascript文件中，采用异步的方式运行，这意味着浏览器不需要等到循环接触，我们可以有更敏感的浏览器，这就是web worker的作用。Web worker帮助我们用异步执行Javascript文件。在 HTML5 的新规范中，实现了 Web Worker 来引入 JavaScript 的 “多线程” 技术，他的能力让我们可以在页面主运行的 JavaScript 线程中加载运行另外单独的一个或者多个 JavaScript 线程；当然 Web Worker 提供不像其他的多线程语言(Java、C++ 等)，主程序线程和 Worker 线程之间，Worker 线程之间，不会共享任何作用域或资源，他们间唯一的通信方式就是一个基于事件监听机制的 message；同时，这并不意味着     JavaScript 语言本身就支持了多线程，对于 JavaScript 语言本身它仍是运行在单线程上的， Web Worker 只是浏览器（宿主环境）提供的一个能力／API。     

我们如何在JavaScript中创建一个worker线程？    
var worker = new Worker("MyHeavyProcess.js");//文件名   
worker.postMessage();//使用“PostMessage”发送信息给worker对象     
worker.onmessage = function (e) {   
document.getElementById("txt1").value = e.data;   
};//当worker线程发送数据的时候，我们在调用结束的时候，通过”onMessage”事件获取   
这个繁重的循环在“MyHeavyProcess.js”的Javascript文件中，以下代码，当Javascript文件想发送信息，他使用”postmessage”，同时任何来自发送者的信息都在“onmessage”事件中接收到。
worker.terminate();//中止Web Worker 
  Web Worker 脚本的文件   
实例化一个 Worker:   
在main.js中，new Worker() 之后会返回一个实例对象，它包含一个 postMessage 方法，可以通过调用这个方法来给 Worker 线程传递信息；通过给这个对象添加onmessage方法，在 Worker 中触发事件通信的时候接收到数据并进行处理。    
// main.js     
var worker = new Worker('./worker.js');     
worker.addEventListener('message', function (e) {   // 监听消息事件   
console.log('MAIN: ', 'RECEIVE', e.data);     
});    
worker.onmessage = function () {   	// 或者可以使用 onMessage 来监听事件    
console.log('MAIN: ', 'RECEIVE', e.data);    
};    
worker.addEventListener('error', function (e) {        // 监听 error 事件     
console.log('MAIN: ', 'ERROR', e);     
console.log('MAIN: ', 'ERROR', 'filename:' + e.filename + '---message:' + e.message + '---lineno:' + e.lineno); });    
worker.onerror = function () {                                        // 或者可以使用 onMessage 来监听事件   
console.log('MAIN: ', 'ERROR', e);    
};     
worker.postMessage({ m: 'Hello Worker, I am main.js' });        // 触发事件，传递信息给 Worker    
在 Worker 的脚本中，我们可以调用全局函数 postMessage 和给全局的 onmessage 赋值来发送和监听数据和事件   
// worker.js    
console.log('WORKER TASK: ', 'running');     
onmessage = function (e) {                                              //监听数据进行处理   
console.log('WORKER TASK: ', 'RECEIVE', e.data); 
postMessage('Hello, I am Worker');                      // 发送数据事件    
} 
addEventListener('message', function (e) {        // 或者使用 addEventListener 来监听事件    
console.log('WORKER TASK: ', 'RECEIVE', e.data);   
postMessage('Hello, I am Worker');    
});    
event 这个事件对象中有几个比较重要的参数需要我们注意：    
  ● event.filename: 导致错误的 Worker 脚本的名称；   
  ● event.message: 错误的信息；   
  ● event.lineno: 出现错误的行号；   

Worker 的环境与作用域   
如前文所述，在 Worker 线程的运行环境中没有 window 全局对象，也无法访问 DOM 对象，所以一般来说他只能来执行纯 JavaScript 的计算操作。    
但是，他还是可以获取到部分浏览器提供的 API 的：   
  ● setTimeout()， clearTimeout()， setInterval()， clearInterval()：有了设计个函数，就可以在 Worker 线程中执行定时操作了；    
  ● XMLHttpRequest 对象：意味着我们可以在 Worker 线程中执行 ajax 请求； 
  ● navigator 对象：可以获取到 ppName，appVersion，platform，userAgent 等信息；
  ● location 对象（只读）：可以获取到有关当前 URL 的信息；

在 Worker 中加载外部脚本    
可以通过 Worker 环境中的全局函数 importScripts() 加载外部 js 脚本到当前 Worker 脚本中，它接收多个参数，参数都为加载脚本的链接字符串，比如：
importScripts('worker2.js', 'worker3.js'); importScripts('worker2.js'); importScripts('worker3.js');   

应用:   
Web Worker 的实现为前端程序带来了后台计算的能力，可以实现主 UI 线程与复杂计运算线程的分离，从而极大减轻了因计算量大而造成 UI 阻塞而出现的界面渲染卡、掉帧的情况，并且更大程度地利用了终端硬件的性能；   
同时把程序之间的任务更清晰、条理化；  
其主要应用有几个场景：   
  ● 对于图像、视频、音频的解析处理；  
  ● canvas 中的图像计算处理；  
  ● 大量的 ajax 请求或者网络服务轮询；   
  ● 大量数据的计算处理（排序、检索、过滤、分析…）；   

Web Worker线程的限制是什么？   
Web worker线程不能修改HTML元素，全局变量和Window.Location一类的窗口属性。你可以自由使用Javascript数据类型，XMLHttpRequest调用等。  
