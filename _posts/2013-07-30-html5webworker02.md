---
layout: post
title: "HHTML5 WebWorker教程(2) 如何使用web worker "
description: ""
category: "HTML5新标准"
tags: [html5,WebWorker]
---

首先当我们要使用某个并非被所有浏览器支持的标准时，我们应该对浏览器对该特性的支持程度做检测。web worker 是属于window对象的属性，我们可以通过这种方式来判断浏览器是否支持web worker

<pre class="prettyprint lang-js linenums">
        isWebWorkerAvailable(){
          return !! window.Worker
        }
</pre>

现在我们已经知道浏览器是否支持web worker。我们就用他来开始写一个小的例子。我们先创建一个HTML名为页面代码如下:

<pre class="prettyprint lang-js linenums">
&lt;!doctype html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;title&gt; web worker : the highest prime number &lt;/title&gt;
&lt;style type="text/css"&gt;
#result{
background-color: yellow;
padding: 20px;
font-size: 140%;
}
&lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;
web worker : the highest prime number
&lt;/h1&gt;
&lt;article&gt;
web worker : the highest prime number so far is:
&lt;output id="result"&gt;&lt;/output&gt;
&lt;/article&gt;
&lt;script type="text/javascript"&gt;
var worker = new Worker("highprime.js");
worker.onmessage = function(event){
document.getElementById("result").textContent = event.data;
}
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>


在worker中我们添加了hrighprime.js 作为参数，接下来我们看一下这个js文件中的代码


<pre class="prettyprint lang-js linenums">
var n = 1;
search:while(true){
n =n+1;
for (var i = 2; i &lt; Math.sqrt(n); i++) {
if(n%i == 0){
continue search;
}
};
postMessage(n);
}
</pre>


当你使用chrome 16以上的版本运行这个页面的时候 ，犹豫chrome的安全策略，worker将不能在本地文件中运行，而是默默的失败了。解决方案可以采用--allow-file-access-from-files这种标示来运行chrome，具体操作可以参见这里。但并不推荐你用这种方式，这种方式只能用于测试可开发用。在chrome 17以上的版本中浏览器会抛出Uncaught Error: SECURITY_ERR: DOM Exception 18这样的错误提示。推荐你在开发的时候搭建本地web站点服务。
消息将会在我们的webapp页面和worker之间通过postMessage()方法进行传递，注意的是这个消息传递的是副本而且消息本身，也就也为着数据不是共享的。我们的主页面和web worker并没有指向同一个对象实例，所以这两端有重复的内存占用。在现代浏览器中，消息传递这个功能是通过在主页面和web worker 这两端通过JSON编码解码的方式实现的。

###加载外部js

可以使用importScripts()这个全局函数，来添加外部js文件或类库，参数为字符串类型，代表要被导入的资源文件。如果你给的参数不包含任何URL，什么都不会发生。如果你传入一个或多个URL地址（包含文件名称），就会在web worker中加载并执行对应js文件。具体代码如下：

<pre class="prettyprint lang-js linenums">
importScripts('s1.js')
importScripts('s2.js')
</pre>

以上的这两行同样可以换成importScripts('s1.js','s2.js')。浏览器将会按顺序加载这些脚本文件，并且在失败的时候会抛出NetworkError这样的异常，当所有的文件都被获取完，在你调用importScripts()的地方，这些脚本将会顺序执行。这些命令将会同步执行。只有当，所有的js文件都被获取并被执行了，importScripts方法才会返回。值得注意的是，唤起worker的页面和被唤起js必须使用相同的协议，如你的页面采用https协议，js文件就不能采用http协议。
以下是一种很有用的方式使用worker 通过REST api 获得JSON数据，之后使用这个数据。

<pre class="prettyprint lang-js linenums">
function startWorker(settings) {
var myWorker = new Worker('scripts/worker.js');
myWorker.addEventListener("message", workerListener, false);
myWorker.postMessage(settings);
}
</pre>


worker.js 如下


<pre class="prettyprint lang-js linenums">
self.addEventListener('message', function(e) {
doSomeWork();
};
function doSomeWork() {
importScripts('http://example.com?callBack=handleWorkerResults');
}
function handleWorkerResults() {
postMessage(result);
}
</pre>
