---
layout: post
title: "HTML5 WebWorker教程(1) web worker简介 "
description: ""
category: "HTML5新标准"
tags: [html5,WebWorker]
---


###简介

webworker是HTML5新标准中一个比较重要并且很高端的特性之一，但目前还没有收到太多的关注。这个特性提供相应的API允许你以一个独立的线程去运行你的javascript代码，并且这个线程不会阻塞你的web应用程序。这些javascript是在一个与主渲染程序或者你的其他用户界面上的javascript程序是平行关系，似乎可以看成是一种并行关系。这样，当我们在这个线程中执行比较“大型”的计算任务，我们的页面就不会出现未响应的现象。

和其他线程相类似，webworker是一个相对重量级的东西，你不应该大量的使用web worker，因为这样会消耗相当大的系统资源，webworker的预期是是用来在有限的系统资源中做耗时较长的任务。他的启动成本和实例的内存成本都很高。

因为这是一个比较新的标准而且是在不断变化调整的标准，不同浏览器在实现web worker 标准的方式也不同。由于对于web worker的实现已经趋于稳定，就像indexedDB一样，很快就能在绝大多数现代浏览器中使用了。

在以往的开发中，当开发者遇到需要长时间的计算时，通常会采用后台计算，而不是让用户界面等待计算完成而阻塞无响应。web worker标准定义的API就是提供一种方式让我们的计算在一个新的线程中进行，而非用户界面中。所以这种少大型的运算，是不会影响用户界面的内存占用和CPU占用，因为他有自己的线程。

通常多线程编程是一个负责的课题，通常需要大量复杂的算法作支持。在其他高级语言例如java中，开发者通常使用类库来避免这样的复杂性。web worker同样提供了封装的很优美并且很简单的API，可以让开发者不必过多的去考虑死循环或者类似的在多线程开发中经常要遇到的问题。web worker旨在消除不友好的“unresponsive script”对话框。

###web worker 可以做什么？

加入你的应用程序完成一个任务需要150毫秒的时候，你就可以考虑使用web worker了，如果你的web app想做成跟本地应用相同的感觉，这个标准可能是80毫秒，如果你正在搭建一个mobile app，这个时间可能需要更短。也许在实现你的应用程序的时候你需要考虑以下几件事：

解码或编码很长的字符串。
负责的数学计算
超大的数组排序
网络请求和相应数据处理
在本地存储中计算和操作数据
预区或缓存数据
图像处理
分析或处理视频，音频数据
后台I/O
处理大型的数组或超大型的JSON数据等等
创建web worker

 

可以使用web workerAPI轻松的创建一个worker实例，例如：

var worker = new Worker("worker.js")

上面个的这行代码将会加载worker.js这个文件，然后再后台运行。你需要调用Worker()这个构造函数，并将需要在后台执行的文件的URI作为参数。如果你想从worker中得到数据，你需要注册该worker的onmessage事件处理函数，例如:

<pre class="prettyprint lang-js linenums">
var worker = new Worker("router.js");
worker.onmessage = function(event){
console.log("called here form the router");
}
</pre>

同样你也可以通过addEventListener来监听你的worker，

<pre class="prettyprint lang-js linenums">
var worker = new Worker("router.js")
worker.addEventListener('message',function(event){
console.log("called here form the router");
},false);
worker.postMessage();//启动这个worker
</pre>


###web worker能做什么？不能做什么？

worker是不能访问“父”页面（即创建页面）的dom，他不能访问以下任何一个对象：

window 对象
document对象
parent对象
并且最后同样重要的是，在worker中不能使用依赖以上对象的javascript库，像jQuery。
由于多线程性质，web worker 只能访问某些特定的javascript特性。下面这些列表时他可以访问的

navigator对象
location对象（只读）
XMLHttpRequest方法
atob()、btoa()方法，可以讲base64编码转成二进制数据，或逆操作
setTimeout() / clearTimeout() and setInterval() / clearInterval()
dump()
应用程序缓存
可以通过importScripts()方法，导入外部js
创建次级worker 即 worker中创建worker
web worker 的执行

在web worker线程自身中，代码会自上而下的同步执行，之后他们会进入到事件响应的异步阶段。这样，就允许将web worker分成两大类：

注册了onmessage事件相应程序的web worker，用来长时间执行任务，并且在后台执行。这个web worker是不会推出的，因为他一直监听新的消息
另一种是没有注册onmessage事件的web worker，处理一个独立的任务（可以从web app 主线程中脱离的），例如抓取和解析超大的json对象。这样的web worker一点操作结束就会退出。（在某些情况下，如果你注册了callback处理，那么将会等待所有的callback结束后再退出。）
浏览器的支持情况

IE 10.0

Chrome 12+

Firefox 5+

Safari 4+

Opera 11+

iOS Safari 5+

Opera Mobile 11+

Android 2.1

Chrome for Android Beta
