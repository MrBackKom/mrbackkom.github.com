---
layout: post
title: "为什么操作DOM的性能比较低及浅谈virtual DOM(未完......)"
description: "因为对DOM的修改为影响网页的用户界面，重绘页面是一项昂贵的操作。太多的JavaScript DOM操作会导致一系列的重绘操作，为了确保执行结果的准确性，所有的修改操作是按顺序同步执行的。我们称这个过程叫做回流（reflow），同时这也是最昂贵的浏览器操作之一， 回流操作主要会发生在几种情况下："
category:  javacript
tags: ["javacript","DOM","DOM操作"]
---

###一、为什么操作dom会消耗性能

　　　因为对DOM的修改为影响网页的用户界面，重绘页面是一项昂贵的操作。太多的JavaScript DOM操作会导致一系列的重绘操作，为了确保执行结果的准确性，所有的修改操作是按顺序同步执行的。我们称这个过程叫做回流（reflow），同时这也是最昂贵的浏览器操作之一， 回流操作主要会发生在几种情况下：
当对DOM节点执行新增或者删除操作时。
动态设置一个样式时（比如element.style.width="10px"）。
当获取一个必须经过计算的尺寸值时，比如访问offsetWidth、clientHeight或者其他需要经过计算的CSS值（在兼容DOM的浏览器中，可以通过getComputedStyle函数获取；在IE中，可以通过currentStyle属性获取）。
解决问题的关键，就是限制通过JavaScript DOM操作所引发回流的次数。大部分浏览器都不会在JavaScript的执行过程中更新DOM。相应的，这些浏览器将对对DOM的操作放进一个队列，并在JavaScript脚本执行完毕以后按顺序一次执行完毕。也就是说，在JavaScript执行的过程中，用户不能和浏览器进行互动，直到一个回流操作被执行。（失控脚本对话框会触发回流操作，因为他执行了一个中止JavaScript执行的操作，此时会对用户界面进行更新）
如果要减少由于DOM修改带来的回流操作，有两个基本的方法。第一个就是在对当前DOM进行操作之前，尽可能多的做一些准备工作。一个经典的例子就是向document对象中添加很多DOM节点：

<pre class="prettyprint  lang-js linenums" >
for(var i = 0;i &lt; items.length;i++){
var item=document.createElement("li");
item.appendChild(document.createTextNode("Option"+i);
list.appendChild(item);
}
</pre>

这段代码的效率是很低的，因为他在每次循环中都会修改当前DOM结构。为了提高性能，我们需要将这个次数降到最低，对于这个案例来说，最好的办法是建立一个文档碎片（documentfragment），作为那些已创建元素元素的临时容器，最后一次将容器的内容直接添加到父节点中：

<pre class="prettyprint lang-js linenums" >
var fragment = document.createDocumentFragment();
for(vari=0;i &lt; items.length;i++){
var item=document.createElement("li");
item.appendChild(document.createTextNode("Option"+i);
fragment.appendChild(item);
}
list.appendChild(fragment);
</pre>

