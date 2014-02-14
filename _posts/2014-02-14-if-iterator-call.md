---
layout: post
title: "iterator.call(context,obj[i],i,obj)===breaker判断分析"
description: "Underscore是一个非常实用的JavaScript库，提供许多编程时需要的功能的支持，他在不扩展任何JavaScript的原生对象的情况下提供很多实用的功能。创造一个小而美的公司非常值得尊敬，小而美的框架同样散发着那样的令人着迷的魅力。无论你写一段小的js代码，还是写一个大型的HTML5应用，underscore都能帮上忙。目前，underscore已经被广泛使用，例如，backbone.js唯一强依赖的库就是underscore.js,本文是对源码中if (iterator.call(context, obj[i], i, obj) === breaker)的判断语句分析"
category: JS框架源码分析
tags: [Underscorejs源码分析]
---


　　　underscorejs是一个非常实用的JavaScript库，提供许多编程时需要的功能的支持，他在不扩展任何JavaScript的原生对象的情况下提供很多实用的功能。创造一个小而美的公司非常值得尊敬，小而美的框架同样散发着那样的令人着迷的魅力。无论你写一段小的js代码，还是写一个大型的HTML5应用，underscorejs都能帮上忙。目前，[underscorejs](http://underscorejs.org 'underscore')已经被广泛使用，例如，backbone.js唯一强依赖的库就是[underscorejs](http://underscorejs.org 'underscore'),如果你需要经常操作数组和集合类数据，[underscorejs](http://underscorejs.org 'underscore')绝对是个不错的选择。

　　　在阅读Underscorejs源码中你会常看到一句类似这样的判断语句if(iterator.call(context, obj[i], i, obj) === breaker)，这行代码的上下文如下


<pre class="prettyprint linenums">
var breaker = {};
var each = _.each = _.forEach = function (obj, iterator, context) {
    if (obj == null) return;
    if (nativeForEach &amp;&amp; obj.forEach === nativeForEach) {
        obj.forEach(iterator, context);
    } else if (obj.length === +obj.length) {
        for (var i = 0, l = obj.length; i &lt; l; i++) {
            if (iterator.call(context, obj[i], i, obj) === breaker) return;
        }
    } else {
        for (var key in obj) {
            if (_.has(obj, key)) {
                if (iterator.call(context, obj[key], key, obj) === breaker) return;
            }
        }
    }
};	
</pre>


　　　iterator是作为each方法的迭代器参数传入，目的就是要遍历数组或集合中的每一项来执行此迭代方法，若为数组迭代函数的参数分别为该项和该项的索引，以及数组本身，若为集合或者对象时则遍历属性。

　　　在阅读代码的时候我们会发现，作者在第一行添加了全局的变量breaker，且为空对象，切记是空对象不是undefined，并且此该变量是做为if语句的判断条件，在===成立的情况下跳出迭代器中断迭代。if (iterator.call(context, obj[i], i, obj) === breaker)仔细看这行代码你就会觉得奇怪，为何会有这样的判断，作为迭代器本身其实不应该影响框架本身的逻辑，但为何要这样判断，而且如果我们假设迭代器函数可能有两种情况，1.有返回值 2.无返回值。

　　　先说无返回值情况。按照执行无返回值时执行函数会丢回一个undefined,undefined与breaker作全等比较返回值为false。若有返回值情况，返回值的业务逻辑也很少会与框架的标准逻辑，或者说不应该与框架逻辑出现耦合关系。也许你会想到是这样一种可能，作者想要迭代器作为if判断时一定会被执行一次，但这种想法显然是不成立的,因为for循环自身会执行该逻辑。至此也许你会陷入疑惑，这样做的原因是什么呢？

　　　那么让我们假设一种情况，倘若你有一个数组[1,2,3],你在用迭代器函数迭代这个数组的时候，想在2这个数组元素被找到的时候结束业务逻辑，这时候你该怎么办，其实breaker精妙之处就在此,请看下面的代码：


<pre class="prettyprint linenums">
var _arry = [1,2,3];
_.each(_arry,function(item,index,arr){
	if(item == 2){
	var _breaker = {};
	return _breaker;//切记此处不可直接返回{} 如return {}浏览器在解析词法时抛错！
}
}) 
</pre>



　　　由此可见作者这样做的目的是为了提高框架的可控性，当然作者这样写法的目的可能还有很多，也希望有想法的人可以交流。最后祝大家元宵节、情人节，双节快乐！