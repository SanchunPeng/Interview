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

##2、媒介查询
html5要适应各种分辨率的移动设备，应该使用rem这样的尺寸单位。这样页面在不同设备下就能保持一致的网页布局。但是从工作量和复杂度方面来考虑，确有不足。
简单的解决办法是：文字流式布局，控件弹性布局，图片等比缩放。     
<meta name="viewport"   content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">    
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
