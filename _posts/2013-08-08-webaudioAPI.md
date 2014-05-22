---
layout: post
title: "HTML5 Web Audio API 系列教程（1）   "
description: "起初开发人员在后台播放音频采用的是&lg;bgsound&gt;标签，通过这种方式，通过这种方式，当用户访问某一个页面时，页面的开发者可以在后台播放背景音乐，从而提高用户体验。但这个标签当时仅有IE支持，其他的浏览器不支持也不采用，这种非标准的播放方式。后来NetScape 通过&lt;embed&gt;标签实现了相似的特性，实现了和bgsound几乎相同的功能。 Flash，是第一个可以跨浏览器来实现音频播放的技术实现方式，但他需要一个flash插件支持，也就是说如果你想用flash在浏览器中播放音频，就必须在你的浏览器中装一个flash插件。随着HTML5技术的发展，如今我们有了一种新的方式来实现音频功能"
category: "HTML5新标准"
tags: [html5,音频,Audio]
---



###一、音频在web前端开发中的发展

起初开发人员在后台播放音频采用的是&lg;bgsound&gt;标签，通过这种方式，通过这种方式，当用户访问某一个页面时，页面的开发者可以在后台播放背景音乐，从而提高用户体验。但这个标签当时仅有IE支持，其他的浏览器不支持也不采用，这种非标准的播放方式。后来NetScape 通过&lt;embed&gt;标签实现了相似的特性，实现了和bgsound几乎相同的功能。
Flash，是第一个可以跨浏览器来实现音频播放的技术实现方式，但他需要一个flash插件支持，也就是说如果你想用flash在浏览器中播放音频，就必须在你的浏览器中装一个flash插件。随着HTML5技术的发展，如今我们有了一种新的方式来实现音频功能 -----------&lt;audio&gt;
虽然&lt;audio&gt;标签让我们摆脱了插件，但是在实现复杂交互的应用和游戏当中依然存在很多的局限性。这些局限性包括以下几点

1.没有精确的定时控制

2.单次播放音频的数量比较低

3.没有可靠的方法去预缓冲的声音

4.没有能力适用于实时效果

5.没有办法分析的声音

不过目前已经有很多人在努力尝试通过 Audio Api去解决这些问题，一个很典型的例子就是，火狐提供的Audio Data Api。与Audio Data API相比，Web Audio API是一个全新的模式，完全从&lt;audio&gt;标签分离开来，他是一个更高级的javascript API。web Audio API的目的就是给游戏引擎，还有一些比如混音，过滤等和音频操作相关的任务，提供一种处理音频的能力。

###二、The Audio Context
web audio api 是都构建在一个叫做音频上下文的基础上的。音频上下文是一个有向图的节点，定义了音频的音频流的源（通常是一个音频文件）到目的地。举个最简单的例子

 ![实例图片](http://img1.ph.126.net/CgEOsWVjVgENm-vtEGmO9w==/6597284171425133767.jpg)

 ###三、初始化 Audio Context

初始化音频上下文，web audio api 目前已经在chrome和safari浏览器，以及移动版safari浏览器（ios6）上已经实现了，可以通过javascript进行调用。在这些浏览器中，audio上下文的构造函数是需要呆着一个webkit前缀的，所以你在创建的时候是要通过new webkitAudioContext  而不是 new AudioContext。但是随着标准化在浏览器厂商之间的推进没这个方式将会被改变的，但现在你可以通过这种方式来尝试在其他浏览器中去创建audio上下文。

<pre class="prettyprint lang-js linenums">
var contextClass = (window.AudioContext ||
window.webkitAudioContext ||
window.mozAudioContext ||
window.oAudioContext ||
window.msAudioContext);
if (contextClass) {
// Web Audio API 可用.
var context = new contextClass();
} else {
// Web Audio API 不可用
}
</pre>

###四、audio 节点类型

Audio context 最主要的一个用处就是来创建audio节点，概括的说 audio节点类型，有以下几种：
Source nodes 源节点
Sound sources such as audio buffers, live audio inputs, &lt;audio&gt; tags, oscillators,
and JS processors
Modification nodes 修改节点
Filters, convolvers, panners, JS processors, etc.
Analysis nodes 分析节点
Analyzers and JS processors
Destination nodes 目标节点
Audio outputs and offline processing buffers
Source 节点，并不一定依赖于声音文件，同时可以用实时输入设备来作为source节点，比如电话、麦克等外接设备。不过目标节点（Destination nodes）通常是一个扬声器，或者声音输出设备。
任何音频节点的输出可以通过connect()方法，被连接到任何其他音频结点的输入。举个例子：

<pre class="prettyprint lang-js linenums">
// Create the source.
var source = context.createBufferSource();
// Create the gain node.
var gain = context.createGain();
// Connect source to filter, filter to destination.
source.connect(gain);
gain.connect(context.destination);
</pre>

 
主意那个context.destination,他是一个特殊的结点，它能够唤起你系统的默认声音输出设备。
         以下是一个简单的音频图谱：

![实例图片](http://img1.ph.126.net/InFFjoXFmh92ocvERi_iJw==/3283405603430027707.png)

 
这样的连接图建立起来之后，我们是可以通过动态的方式去改变他，比如node.disconnect(outputNumber)，通过这个方式来断开连接.
举个例子，我们可以避开gain节点，直接创建一个从源节点到目标节点的通道。

<pre class="prettyprint lang-js linenums">
source.disconnect(0);
gain.disconnect(0);
source.connect(context.destination);
</pre>

###五、加载和播放声音

多版本的应用程序可能用同一个缓冲区，这种模式几乎是不可避免的。
加载一个声音到web audio api 中，我们可以使用xmlhttprequest并用contenxt.

decodeAudioData来处理响应结果，这些可以异步发生，不糊阻塞UI线程。

<pre class="prettyprint lang-js linenums">
var request = new XMLHttpRequest();
request.open('GET', url, true);
request.responseType = 'arraybuffer';
// Decode asynchronously
request.onload = function() {
context.decodeAudioData(request.response, function(theBuffer) {
buffer = theBuffer;
}, onError);
}
request.send();
</pre>

当你加载了你的buffer，你就可以为这个buffer创建一个源节点（AudioBufferSourceNode）了，之后把这个源节点链接到音频图中，之后调用源节点的start(0)方法，或者调用源节点的stop(0)方法来停止播放，值得注意的是这些方法必须是要在当前这个audio context中。

<pre class="prettyprint lang-js linenums">
function playSound(buffer) {
var source = context.createBufferSource();
source.buffer = buffer;
source.connect(context.destination);
source.start(0);
}
</pre>

###六、总结

把你之前看到的列表总结起来，在一个游戏当中，我们需要将这些javascript抽象出来，例如我们抽象出一个bufferloaderclass，这个类通过已知的路径然后返回audio buffer。举个例子：

<pre class="prettyprint lang-js linenums">
function BufferLoader(context, urlList, callback) {
    this.context = context;
    this.urlList = urlList;
    this.onload = callback;
    this.bufferList = new Array();
    this.loadCount = 0;
}
 
BufferLoader.prototype.loadBuffer = function(url, index) {

    var request = new XMLHttpRequest();
    request.open("GET", url, true);
    request.responseType = "arraybuffer";
 
    var loader = this;
 
    request.onload = function() {
        // Asynchronously decode the audio file data in request.response
        loader.context.decodeAudioData(
            request.response,
            function(buffer) {
                if (!buffer) {
                    alert('error decoding file data: ' + url);
                    return;
                }
                loader.bufferList[index] = buffer;
                if (++loader.loadCount == loader.urlList.length)
                    loader.onload(loader.bufferList);
            },
            function(error) {
                console.error('decodeAudioData error', error);
            }
        );
    }
 
    request.onerror = function() {
        alert('BufferLoader: XHR error');
    }
 
    request.send();
};
 
BufferLoader.prototype.load = function() {
    for (var i = 0; i &lt; this.urlList.length; ++i)
        this.loadBuffer(this.urlList[i], i);
};
window.onload = init;
var context;
var bufferLoader;
function init() {
context = new webkitAudioContext();
bufferLoader = new BufferLoader(
context,
[
'../sounds/hyper-reality/br-jam-loop.wav',
'../sounds/hyper-reality/laughter.wav',
],
finishedLoading
);
bufferLoader.load();
}
function finishedLoading(bufferList) {
var source1 = context.createBufferSource();
var source2 = context.createBufferSource();
source1.buffer = bufferList[0];
source2.buffer = bufferList[1];
source1.connect(context.destination);
source2.connect(context.destination);
source1.start(0);
source2.start(0);
}

</pre>
