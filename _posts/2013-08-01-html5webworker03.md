---
layout: post
title: "HHTML5 WebWorker教程(3) 专用web worker "
description: ""
category: "HTML5新标准"
tags: [html5,WebWorker]
---

其实专用web worker这个词是我自己起的，因为我实在想不出用什么名字，暂且先称之为专用web worker。专用web worker是在后台能够让你的js运行。当你的web worker运行的时候，worker可以通过发送消息给事件处理程序实现跟web主页面进行通信即postmessage

专用web worker有益于需要大量消耗CPU的任务，如：计算素数，计算位置，计算路径等等，并且也适合掩盖服务器端的通信缓慢。同时平行的用户界面处理不会因这些计算而受到干扰。

专用web worker 提供两个事件：

onmessage:当接收到信息的时候触发，这个事件会携带一个事件对象和数据信息。

onerror:当web worker线程发生错误的时候会触发这个事件，这个事件对象将携带着这个错误信息。

接下来我们做这样一个例子，我们启动一个页面然后创建一个web worker，让这个webworker从服务器端拉取数据，一旦接收到数据，这个web worker就将数据发送个主页面。在实际的工作中，我们可以让web worker去服务器端通信，我们的页面主负责UI交互处理。

在这个例子中我们可能要做一下的一些事情，通过构造函数来创建webworker，然后通过这个web worker的addEventListener去监听web worker接收到的消息。然后根据得到的信息在dom上画出节点。此处为本实例的演示地址。

在此web worker中他本身做了两步，首先，在readTweets()方法中通过Tweeter的API来获取信息数据。然后通过callback来处理数据。除此之外，我们可以使用app的缓存还有离线缓存，以便我们在低网速的情况下依然可用。

通过web worker 进行数据转换

web worker 特别适合做耗时较长的任务，在如今的web 应用程序中，我们经常会遇到要处理大量数据的时候，假设当你在处理一个大的json串的时候，当你解析他的时候可能需要花费250毫秒，这样的话，你就应该使用web worker。你的用户就不会因为你转换数据的时候只能呆呆的看着未响应的页面，或者大发雷霆，或者用一秒不到的时间关闭网页，在野不访问这个页面。接下来我们来做一个用web worker来做json字符串转换的例子。

我们在主页面中的新建worker

<pre class="prettyprint lang-js linenums">
var worker = new Worker("worker-parser.js");
//用来接收转换回来的json对象
worker.onmessage = function(e){
var jsonobj = e.data;
showData(jsonobj);
}
//
//JSONText 为你要转换的json字符串
worker.postMessage(JSONText);

在worker-parser.js中处理json，然后通过postMessage传回对象

self.onmessage = function(e){
var jsonText = e.data;
var jsonObj = JSON.parse(jsonText);
self.postMessage(jsonObj);
}
</pre>

可传递对象

在FF和chrome13以上的版本，通过一种结构化克隆的方式，我们可以与web worker发送或接收ArrayBuffers，postMessage方法不仅仅只能发送字符串，而且还能发送复杂的数据类型，例如File，Blob，ArrayBuffer，JSON对象。结构化克隆算法是对于任何一名程序员来说都很重要，但是他始终是一个要花费成百上千毫秒的复制操作。

Chrome 17版本以上的浏览器，提供了另一种高效的方式，这种方式的原理就是消息传递，这种方式也被成为可传递对象。这个实现保证了数据转移,而不是从一个环境到另一个拷贝，从意义上讲，这是一个移动操作而非复制操作，这大大提高将数据发送到web worker的性能。这有点类似于其他语言中的引用操作（按引用传递）。才传统的引用传递中，我们会得到一个对应那个值的指针引用。然而举个例子，当我们在我们的主程序应用页面中把一个ArrayBuffer传递给一个web worker的时候，原始的ArrayBuffer将会被清除，我们将不能再访问它。相反，这个ArrayBuffer的上下文被转移到了web worker的上下文中，只有在web worker的作用域中才能访问这个ArrayBuffer。

在chrome17版本中，可以使用webkitPostMessage(),来传递可转换对象，这个方法携带两个参数，ArraryBuffer 消息 和要被转换的对象列表

worker.webkitPostMessage(arraryBuffer,[arrayBuffer])。