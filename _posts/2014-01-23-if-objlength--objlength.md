---
layout: post
title: "if (obj.length === +obj.length)判断分析"
description: "163博客文章迁移"
category: JS框架源码分析
tags: [Underscorejs源码分析,博客迁移]
---
{% include JB/setup %}

　　　underscore.js 用来操作数组数据十分的方便，今天在阅读underscore.js源码的时候发现了一个判断语句，一时没看懂是什么意思，后来仔细分析了一下，也到网上查阅相关的资料。
　　　
   
<pre class="prettyprint lang-javascript">
   if (obj.length === +obj.length){
      for (var i = 0,length = obj.length; i &lt; length; i++){
      	if (iterator.call(context, obj[i], i, obj) === breaker)
      		return;
      }
   }
</pre>
　　　源码如上，请注意if判断,看上去很奇怪很难懂，先把这句话的意思表达一起，如果换一种写法你肯定就会明白了，其实这句话等价于另外一种写法 :

　　　if (typeof obj.length === "number" && !isNaN(obj.length))　　　

　　　看上去这样写似乎更能够让人看懂，而且也能达到效果，但为什么作者要这样写呢，接下来我们需要关注两个地方：

　　　第一个就是‘===’，全等符号

　　　第二个就是‘+’ 加号

　　　先说一下全等号的作用，在一般的if判断中，我们更多的是使用‘==’，这种模式比如if(2 == '2')，这时候我们得到的返回结果为true，但在‘===’，这种模式下，我们得到的结果是false。这是为什么呢，因为全等符号是不会将比较的对象进行类型转换的。再说一下‘+’，在此处说所起到的作用,你可以动手做一个实验，在控制台中执行下面这句话+'2',你可以看到返回值为数字类型的2，其实不难理解，'+'号其实是将后面跟的操作数转型成了数字类型。我们在是想一下作者这样写的目的，如果obj是一个string类型，如"abc",我们可以拿到length属性，如果是一个function，或者一个数组，我们都可以拿到他们的length属性，但如果是一个object类型的数据，它可能是不包含length属性的。对于非数组、非字符串、非函数类型的数据，我们可以尝试使用for in循环来遍历数据。这样看来其实作者这样的写法，更是想能区分数组类型的数据或者类数组数据如字符串等。