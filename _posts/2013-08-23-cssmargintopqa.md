---
layout: post
title: "margin-top绑架父节点问题的分析"
description: ""
category: "css疑难杂症"
tags: [css,margin-top绑架父节点问题的分析]
---

现象：
当两个空的块级元素嵌套时，如果内部的块设置有margin-top属性，而且父元素没有下边解决方法所述的特征，那么内部块的margin-top属性会绑架父元素（即将margin-top传递凌驾给了父元素）。
就好比一个小兵，看到上级有漏洞，就假传圣旨，利用漏洞扩张自己的权利。只要设置父元素的border（栅栏）或者padding（隔离墙），就能管住这个调皮的下属。


<pre class="prettyprint lang-js linenums">
&lt;tdiv id="parrent"&gt;
	&lt;tdiv id="box1">&lt;t/div&gt;
&lt;t/div&gt;

#parrent{
	width:500px;
	height:300px;
	background:teal;
}
#box1{
	width:100px;
	height:100px;
	background:aqua;
	margin:20px;
}
</pre>

解决方法：
1、设置父元素或者自身的display:inline-block;
2、设置父元素的border:1px aqua solid;(&gt; 0)
3、设置父元素的padding:1px;(&gt; 0)
4、给父元素设置overflow:hidden;
5、给父元素或者自身设置position:absolute;
6、设置父元素非空，填充一定的内容。

这个现象并不是bug，而是有理论依据的：

《on having layout》
hasLayout 会影响一个盒子和其子孙的边距重叠。根据规范，一个盒子如果没有上补白和上边框，那么它的上边距应该和其文档流中的第一个孩子元素的上边距重叠。

但值得一提的是，只有在FF和Chrome下才会出现这种margin-top绑架父节点的情况，在IE6 IE7中均显示正常，但这恰恰说明了他们是不符合规范的，而FF合Chrome则是严格遵守规范的。