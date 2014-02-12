---
layout: post
title: "用Timing API 来做性能检测"
description: "Timing API是用来辅助分析分析页面耗时，和反应页面性能指标的新标准，对于优化web站点很有意义。"
category: JS标准
tags: [性能,User Timing API]
---

　　　高性能的web应用能够给用户带来良好的体验，随着web应用的逻辑越来越复杂，理解哪些东西会影响你的web站点的性能，对于搭建一个有良好体验的web应用是很重要的。在过去的几年中，浏览器中出现了大量的API来帮助开发人员来分析web页面的网络表现、加载时间等等。但这并没有真正的指出究竟是什么把你的页面性能拉低了，以及真正的瓶颈在哪？[User Timing API ](http://www.w3.org/TR/user-timing/ "User Timing API")提供了若干方法和机制，可以用来确定你的应用程序的时间花销。在这片文章中我讲讲解如何使用[User Timing API ](http://www.w3.org/TR/user-timing/ "User Timing API")
　　　
####如果你不能计算时间，你就不能优化
　　　优化性能的第一步就是找出你的时间是如何花销的，确定js代码的时间开销是确定性能点的的理想方法，幸运的是[User Timing API ](http://www.w3.org/TR/user-timing/ "User Timing API")允许你在特定的js代码块位置调用特定的API方法，然后我们可以得到这些API得到的时序数据，进而来优化我们的web页面。

####高分区时间 和 now()方法
　　　精确的时间测量的一个基本组成部分是精度，再过去我们以毫秒为精度的时间计算是ＯＫ的，但是在一个60FPS的web站点下，一帧需要16毫秒。因此当你只有毫秒级的精度时对于分析来说是不科学的。所以w3c提出了新的时间类型 [高分区时间](http://www.w3.org/TR/hr-time/ 'High Resolution Time')(High Resolution Time),该标准在现代浏览器中已经得到了实现。HRT提供给开发者浮点时间戳，可以精确到微秒级。

　　　在你的应用程序中活的当前时间，你可以调用now()方法，该方法是作为performance接口的一个扩展。调用方法如下面代码所示：

<pre class="prettyprint linenums">
var myTime = window.performance.now();
</pre>

　　　另外一个接口叫作[PerformanceTiming](http://www.w3.org/TR/navigation-timing/#sec-navigation-timing-interface "PerformanceTiming"), 这个接口中定义很多和web应用程序加载相关的时间属性，now()方法返回的时间值就是来自于[PerformanceTiming](http://www.w3.org/TR/navigation-timing/#sec-navigation-timing-interface "PerformanceTiming")的 [navigationStart](http://www.w3.org/TR/navigation-timing/#dom-performancetiming-navigationstart)发生的时间。

####DOMHighResTimeStamp 类型
　　　在过去如果我们想得到一个时间戳通常我们使用的方法就是Date.now()，然后返回给我们一个[DOMTimeStamp](http://www.w3.org/TR/DOM-Level-3-Core/core.html#Core-DOMTimeStamp 'DOMTimeStamp') 。DOMTimeStamp返回的是一个整形的毫秒值。为了更高精度的目的，[DOMHighResTimeStamp](http://www.w3.org/TR/hr-time/#sec-DOMHighResTimeStamp 'DOMHighResTimeStamp')被引入了。此类型同样返回一个毫秒值，但该值为浮点数值。但由于它的浮点，该值可以表示小数毫秒，因此可以产生一毫秒的千分之一的精度。


####User Timing 接口
　　　此接口提供了若干方法，我们利用这些方法，可以在应用程序的不同地方调用，这样就行成了类似通话故事中按照面包屑来回家的线索时间了。这样我们就能知道我们的时间开销是在什么地方了。

####使用 mark()
　　　mark()方法主要是为我们来存储时间戳的。这个方法最有意义的是我们可以给我们的时间戳命名，有点类似于key-velue存储。标准中提供了一些比较有特定意义的名称。如“mark_fully_loaded“，”mark_fully_visible”，“mark_above_the_fold“等等。例如我们可以再程序加载完成的时候打一个标记代码如下

<pre class="prettyprint linenums">
window.performance.mark('mark_fully_loaded');
</pre>

　　　通过在我们的Web应用程序设置命名标记，我们可以收集到一大堆的时间数据，这样我们就可以分析我们的应用程序在什么时间在做什么。

####使用measure()来测量
　　　一旦你设置了一堆时间标记，你要找出它们之间所经过的时间。您可以使用使用measure()方法来做到这一点。measure()方法可以可以计算标记间的时间消耗，当然也可计算你的自定义标记和 [PerformanceTiming](http://www.w3.org/TR/navigation-timing/#sec-navigation-timing-interface "PerformanceTiming") 接口提供的时间点的时间消耗。例如你可以用以下方法来计算DOM加载到完成到你的web应用程序完全加载完成所需的耗时。

<pre class="prettyprint linenums">
window.performance.measure('measure_load_from_dom', 'domComplete', 'mark_fully_loaded');
</pre>

　　　当你调用measure()方法时，所得到的结果和你设置的标记是独立的，以后你可在重新获取。存储是伴随你的应用程序响应而进行的，所以你的应用程序是正常响应的，并且你可以转存这些数据，在你的应用程序完成某些功能后进行分析。

####clearMarks()来清除标记
　　　有时候删除一些标记也是很有用处的，例如你可能会批量运行你的web应用程序所以因此你就行每次都重新运行一次新的标记操作，但新的标记之前一定要清除之前的标记。当你想清除这些标记的时候这个方法就会被用到。就类似于你用秒表计时，当你新一轮操作开始前要秒表归零。

　　　你可分两种方式调用：

　　　window.performance.clearMarks();//清除所有标记

　　　window.peformance.clearMarks('mark_fully_loaded');//清除指定标记。

　　　与clearMarks()方法相类似的方法是清除计算clearMeasures(),用法几乎一模一样。

####获得时间数据
　　　现在我们已经可以很好的利用接口方法来打标记和计算时间间隔了，但我们在某种时刻就想取出时间数据来做性能分析，这时候我们开始用[PerformanceTimeline](http://www.w3.org/TR/performance-timeline/#sec-performance-timeline 'PerformanceTimeline')接口下面的一些方法进行获取我们记录的时间数据了。例如[getEntriesByType()](http://www.w3.org/TR/performance-timeline/#dom-performance-getentriesbytype 'getEntriesByType')方法可以根据参数类型（‘mark’或者‘measure’）来获取数据。返回值为一个列表，这样我们就可以循环跌倒里面的每一条数据，来做进一步的分析。

<pre class="prettyprint linenums">
var markitems = window.performance.getEntriesByType('mark');
var measureitems = window.performance.getEntriesByType('measure');
</pre>

　　　同样你也可以传入指定的标记名称或者计算名称作为参数来获取数据，返回值同样是一数据列表。

<pre class="prettyprint linenums">
var items = window.performance.getEntriesByName('mark_fully_loaded');
</pre>

####举例计算XHR 请求的耗时分析
　　　我们现在就用Timing API 来分析一下我们应用程序中在使用XHR对象发送请求和响应的耗时分析。

　　　通常我们的xhr请求会如下一样

<pre class="prettyprint linenums">
var myReq = new XMLHttpRequest();
myReq.open('GET', url, true);
myReq.onload = function(e) {
  	do_something(e.responseText);
}
myReq.send();
</pre>

　　　接下来我们调整一下，首先我们先定义一个全局变量，用它来存储请求次数，并且用它来区别每次请求的标记和计算，修改后代码如下：

<pre class="prettyprint linenums">
var reqCount = 0;
var myReq = new XMLHttpRequest();
myReq.open('GET', url, true);
myReq.onload = function(e) {
  	window.performance.mark('mark_end_xhr');
  	reqCnt++;
  	window.performance.measure('measure_xhr_' + reqCnt, 'mark_start_xhr', 'mark_end_xhr');
  	do_something(e.responseText);
}
window.performance.mark('mark_start_xhr');
myReq.send();
</pre>

　　　接着我们调用getEntriesByName方法来获取时间数据：

<pre class="prettyprint linenums">
var items = window.performance.getEntriesByType('measure');
for (var i = 0; i &lt; items.length(); ++i) {
  	var req = items[i];
  	console.log('XHR ' + req.name + ' took ' + req.duration + 'ms');
}
</pre>

####总结
　　　上面我们介绍了Timing API及相关方法使用说明，利用Timing API我们就可以做性能分析了。但是如果遇到浏览器不支持Timing API该怎么办呢？不用担心,可以使用一些实现了该标准的[第三方js库](https://gist.github.com/pmeenan/5902672 '第三方的库')或者[在线站点](http://www.webpagetest.org)对web应用进行性能分析。Timing API 就介绍到这里。