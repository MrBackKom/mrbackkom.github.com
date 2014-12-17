---
layout: post
title: "浅析React对比更新规则"
description: "在这片文章中将浅析，Reactjs的对比算法和渲染工作，希望能够借此优化你的应用程序。"
category:  javacript
tags: ["javacript","Reactjs","Reactjs对比规则"]
---

在这片文章中将浅析，React的对比算法和渲染工作，希望能够借此优化你的应用程序。

 **对比算法**

在开始研究细节之前，让我们先来看看React是怎么书写的。看下面一段代码，用来创建一个组件，

<pre class="prettyprint  lang-js linenums">
        var MyComponent = React.createClass({
               render: function() {
                      if (this.props.first) {
                         return <div className="first"><span>A Span</span></div>;
                      } else {
                         return <div className="second"><p>A Paragraph</p></div>;
                      }
        }});
</pre>


你需要理解有一点是非常重要的，这段代码中render的并非真的DOM节点，他们仅仅是一些轻量级的javascript对象，我们叫他们virtual DOM。

我们接下来用以下几个步骤来看看React中，Render发生变化时的最少步骤。

 - 1.我们创建一个<MyComponent first={true} />
 - 2.替换first的值为false；
 - 3.删除MyComponet

这三步，分别对应的DOM结构为：

    1：创建节点<div className="first"><span>A Span</span></div>
    2：替换className 将first 替换为second ，将<span>A Span</span> 节点 替换为 <p>A Paragraph</p>
    3：删除节点<div className="second"><p>A Paragraph</p></div>

**层层对比**

求任意两棵树之间的修改的最小数量的复杂度是O(n^3).但React所用的方法在解决这个问题的时候使用的是一种更加简单强大的方法，复杂度为O(n)。

其实React只是在树的每层进行对比。这就大大降低了复杂度，并且在web应用程序中很少存在在一个树中移动一个容器,通常情况下都是在同级的子节点中进行移动，因此这种对比也是低消耗的。


![](http://7mnnrl.com1.z0.glb.clouddn.com/09918f07833307ba8aa048cc0dfc0d88.png)

**列表**

让我们假设一下，如果有一个组件，它包含另外5个子组件，接着我们先这个列表新插入一个组件。很明显就单单这些信息而言，在发生变化前后两个的两个组件列表中搭建映射关系是非常困难的。

默认情况下，React会把第一个链表的第一个组件和第二个链表的第一个组件关联起来，类似下面左边的图，你也可以提供一个key属性，来帮助React搭建这个映射关系。在实际中，通过这种方式在子节点中找到唯一的key也是非常方便的。

http://7mnnrl.com1.z0.glb.clouddn.com/70b8cc915557675fd67b3a8d7653328a.png

**组件**

一个React应用，通常是由多个用户自定义组件构成的，而且这些组件大部分都转换成了通常是div表示的树形结构。这些额外的信息通常是由diff算法考虑的，比如React之比较具有相同class的组件。
例如：如果一个 Header 组件 被替换成一个 ExampleBlock组件，React会移除header 并且创建一个example块。这时候是不需要比较的两个容器，因为两个不同的东西是不需要比较的。直接替换就ok了

![](http://7mnnrl.com1.z0.glb.clouddn.com/febd0d56404f8d679be57ee67db75cb1.png)


**事件代理**

给DOM节点添加事件监听是非常慢而且很消耗性能的。
相反，React实现了一个目前常用的方式叫做事件代理。React进一步重新实现了w3c的事件系统，这就意味着IE8 事件处理上的bug，你是不用在考虑了，而且是跨浏览器的。
简单说说这事怎么实现的，首先注册一个事件的监听在document根节点上。当一个事件触发的时候，浏览器可以给我们目标DOM节点（target）。为了能让事件在DOM循环里传播，React并没有在vritual dom循环中迭代。
事实上，在使用过程中每一个React组件有一个唯一的ID，我们可以通过简单的字符串操作来获得这些id。通过用hash map来存储这些事件监听，我们发现，性能好于在vd上监听他们。
浏览器为每一个事件和事件的监听者创建了一个新的事件对象。这样的好处就是你可以保留这个事件对象的引用，甚至可以修改它。但与此同时又意味着你将会有一个较大的内存消耗。在启动的时候React为这些对象搭建了一个缓冲池。当一个事件对象需要被用到的时候，将会被从缓冲池中取出并使用，这样大大减少了GC。

**Render 渲染**

***批量渲染***
当你在一个组件中调用setState的时候，React将会把他标记为dirty状态，事件循环之后，React会找到所有的dirty状态的组件，并一次重新redner他们。
这种批量操作也是创建高性能app的关键点，而且用js实现起来也是十分苦难的，但在reactjs中已经帮你实现了。

![](http://7mnnrl.com1.z0.glb.clouddn.com/c53f24c81edc94dafee7b3606c81af8e.png)



***子树级渲染***

当调用setState方法时，将会重新渲染virtual DOM。如果在根节点元素上调用setState，此时React 应用程序将重新渲染，所有的组件，无论是不是发生了变化，组件的render方法都会被调用，这听起来很恐怖哈，但是实际上这种做法的表现很棒，究其原因就是我们并没有真的操作真实的dom节点。

首先我们谈论的是怎么展示用户界面，因为用户的屏幕空间是有限的，通常情况下你有成百上千的元素一次性的展示出来，Javascript语言本身已经足够快了，对于业务逻辑的管理也已经足够了。

另外很重要的一点，当我们使用React写代码的时候，通常情况下你是没必要在已发生改变了就在根节点上调用setState方法。而你只需要，在在时间处理的组件上调用即可了。从头到尾遍历所有的组件是不常见的做法。这就意味着触发的变更通常可以锁定在用户的交互区域。

![](http://7mnnrl.com1.z0.glb.clouddn.com/f473ffded458f6d34ba2f33a78ce9f67.png)

***选择性子树级渲染***

最后，其实你是权限去阻止一些子树重绘的。前提是你在自定义的组件上实现了下面这个方法

<pre class="prettyprint  lang-js linenums">
boolean shouldComponentUpdate(object nextProps, object nextState)
</pre>


基于组件的上一个、下一个的属性和状态（props/state），你可以通知React组件没有发生变化，没必要重绘。如果你善于使用这个方法，这将给你的应用程序带来巨大的性能提升。为了能够使用这个方法，你就必须能够比较Javascript对象，这样同样要面对若干问题，比如使用深比较还是浅比较，如果是深比较，我们应该使用不可变的数据结构或者做深拷贝。并且你必须时刻记得这个方法将会被一只调用。

***最后***

如果你想进一步的提高性能，你可以尽可能少得调用setState方法并且用shouldComponentUpdate方法来阻止重绘整个子树。