---
layout: post
title: "javascript 零散变量问题  "
description: ""
category: JS语法
tags: ["javascript"]
---

javascript 允许在多个地方生命变量，但无论在哪里声明变量都等同于在函数顶部声明。这就是所谓的声明提升
所以当先使用变量再在函数后面是声名时很可能会导致逻辑错误。看这样一段代码

<pre class="prettyprint lang-js linenums">
	myname = “zhaolong”
	function alertName(){
	    alert(myname);
	     var myname = "zhaolong01"
	    alert(myname);
	}
	alertName();
</pre>

此时你可能会认为第一个alert会弹出全局变量myname的值 zhaolong 然后接着弹出函数内变量myname的值“zhaolong01”
但其实真正的结果是第一个alert弹出undefined，第二弹出“zhaolong01”。
这是因为在函数内部myname被视为是函数的局部变量（尽管他在调用后声明的）,所有的变量声明都会提升到函数的顶部。
所以为了避免逻辑错误最好是将变量的声明放在函数的顶部，另外请注意如果，在alertName这个方法中如果去掉 第二号myname前面的var 关键字
此时，将不再是声明操作，而是赋值操作，这时第一个alert弹出的将是全局变量“zhaolong” 而后是新赋值的“zhaolong01”，并且此时全局变量的值也发生了改变	