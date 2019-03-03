WebPack打包原理
-----------
&nbsp;&nbsp;把所有依赖打包成一个bundle.js文件，通过代码分割成单元片段按需加载

WebPack的优势
-----------
1) webpack是以commonJS的星矢来书写脚本，但对AMD/CMD的支持也很全面，方便旧项目进行代码迁移。<br/>
2）能被模块化的不仅仅是JS了。<br/>
3）开发便捷，能替代部分grunt/gulp的工作，比如打包、压缩混淆、图片转base64等
4）扩展性强，插件机制完善。<br/>

什么是loader,什么是plugin
-------------
&nbsp;&nbsp;loader用于加载某些资源文件。因为webpack本身只能打包commonJS规范的JS文件，对于其他资源如CSS,img等，是没有方法加载的，这时就需要对应的loader将资源转化，从而进行加载。<br/>
&nbsp;&nbsp;plugin用于扩展webpack的功能。不同于loader，plugin的功能更加丰富，比如压缩打包，优化，不止局限于资源的加载。

什么是bundle，什么是chunk，什么是module？
--------------
bundle：是由webpack打包出来的文件；<br/>
chunk：是指webpack在进行模块依赖分析的时候，代码分割出来的代码块；<br/>
module：是开发中的单个模块；<br/>

webpack和gulp的区别？
--------------
&nbsp;&nbsp;webpack是一个模块打包器，强调的是一个前端模块化方案，更侧重模块打包，我们可以把开发中的所有资源都看成是模块，通过loader和plugin对资源进行处理。<br/>
&nbsp;&nbsp;gulp是一个前端自动化构建工具，强调的是前端开发的工作流程，可以通过配置一系列的task，第一task处理的事情（如代码压缩，合并，编译以及浏览器实时更新等）。然后定义这些执行顺序，来让glup执行这些task，从而构建项目的整个开发流程。自动化构建工具并不能把所有的模块打包到一块，也不能构建不同模块之间的依赖关系。<br/>

如何自动生成webpack配置文件？
----------
```
  webpack-cli/vue-cli
```

什么是模块热更新？有什么优点？
-----------
&nbsp;&nbsp;模块热更新是webpack的一个功能，他可以使得代码修改之后，不用刷新浏览器就可以更新。在应用过程中替换添加删除模块，无需更新加载整个页面，是高级版的自动刷新浏览器。<br/>
&nbsp;&nbsp;优点：只更新变更内容，以节省宝贵的开发时间。调整样式更加快速，几乎相当于在浏览器中更新样式。

webpack-dev-server和http服务器的区别？
----------
webpack-dev-server使用内存来存储webpack开发环境下的打包文件，并且可以使得模块热更新，比传统的http服务队开发更加有效。

什么是长缓存？在webpack中如何做到长缓存优化？
------------
&nbsp;&nbsp;浏览器在用户访问页面的时候，为了加快加载速度，会对用户访问的静态资源进行存储，但是每一次代码升级和更新都需要浏览器去下载新的代码，最方便和最简单的更新方式就是引入新的文件名称。<br/>
&nbsp;&nbsp;在webpack中，可以在output给出输出的文件制定chunkhash,并且分离经常更新的代码和框架代码，通过NameModulesPlugin或者HashedModulesPlugin使再次打包文件名不变。

什么是Tree-sharking？
------------
tree-sharking是指在打包中去除那些引入了，但是在代码中没有被用到的那些死代码。
