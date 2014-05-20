---
layout: post
title: "三种方式实现瀑布流"
category: 页面布局
tags: [瀑布流]
---

　　　[瀑布流](http://baike.baidu.com/view/7151782.htm?fr=aladdin '瀑布流')的概念其实在现在网页布局或者页面效果已经很常见了，比如你在花瓣网，蘑菇街的web页面中比较常见，今天写一篇文章来讲解一下几种实现瀑布流的方法。

　　　一般瀑布流都是针对等宽不等高的图片进行滚动加载，针对这种情况我们其实可以用三中方式来实现瀑布流布局。

![样图](http://www.thinkphp.cn/Uploads/editor/2013-03-14/514191b325aab.png)

　　　1.原生的javascript来实现。

　　　2.用Jquery来实现。

　　　3.CSS3的布局来实现。

一、原生Js来实现。

首先我们先来搭建页面结构，我们默认先让首页现实6个图片，页面的结构如下。

a.html结构

<pre class="prettyprint lang-js linenums">
&lt;div id="main"&gt;
     &lt;div class="box"&gt;
          &lt;div class="pic"&gt;
               &lt;img src="images/0.jpg"/&gt;
          &lt;/div&gt;
     &lt;/div&gt;
     &lt;div class="box"&gt;
          &lt;div class="pic"&gt;
               &lt;img src="images/1.jpg"/&gt;
          &lt;/div&gt;
     &lt;/div&gt;
     &lt;div class="box"&gt;
          &lt;div class="pic"&gt;
               &lt;img src="images/2.jpg"/&gt;
          &lt;/div&gt;
     &lt;/div&gt;
     &lt;div class="box"&gt;
          &lt;div class="pic"&gt;
               &lt;img src="images/3.jpg"/&gt;
          &lt;/div&gt;
     &lt;/div&gt;
     &lt;div class="box"&gt;
          &lt;div class="pic"&gt;
               &lt;img src="images/4.jpg"/&gt;
          &lt;/div&gt;
     &lt;/div&gt;
     &lt;div class="box"&gt;
          &lt;div class="pic"&gt;
               &lt;img src="images/5.jpg"/&gt;
          &lt;/div&gt;
     &lt;/div&gt;
&lt;/div&gt;
</pre>

b.css结构

<pre class='prettyprint lang-js linenums'>
*{
     margin:0px;
     padding:0px;
}
#main{
     positon:relitive;     
}
.box{
     padding-top:10px;//此处用padding的原因是因为要使用offsetHeight来获取元素的高度，offsetHeight是包含padding而不包含margin 
     padding-left:10px;
     float:left;
}
.pic{
     padding:10px; 
     border:1px sold #CCC;
     border-radius:5px;
     box-shadow:0 0 5px #CCC;
}
.pic img{
     width:165px;
     height:auto;
}
</pre>

按照上面的结构和css样式，我们的前留张图片会按照一行显示，由于高度不同所以下方呈现一种参差不齐的状态，如实例图。此时我们可以思考一下，如果按照瀑布流的样子第七章图片应该罗列在什么地方呢。不难看出，其实我们只需要将第七张图片接入在第一行高度最矮的下面那张即可，并且此时我们需要讲后加入的第七张连同前六张重新组合，在重新计算新的最低高度，继续补齐，这样我们就实现了瀑布流布局。

c.javascript代码

<pre class='prettyprint lang-js linenums'>
window.onload = function(){
     waterfall("main","box");
     window.onscroll  = function(){
     //假设给的数据为json
     var dataInt = {
          "data":[
               {"src":'****/img01.jpg'}
               {"src":'****/img02.jpg'}
               {"src":'****/img03.jpg'}
               {"src":'****/img04.jpg'}
               {"src":'****/img05.jpg'}
               {"src":'****/img06.jpg'}
               {"src":'****/img07.jpg'}
          ]
     }
          if(checkScrollSlide){
               var oParent = document.getElementById("main");
               for(var i = 0,len = dataInt.data.length;i &lt; len;i++){
                    //循环插入main里面，结构和box一样。
                    oParent.append(oBox)
               }
                waterfall("main","box");
          }
     }
}

var waterfall = function(parent,box){
     //将main下的所有class为box的元素取出来
     var oParent = document.getElementById(parent);
     var oBoxs = getByClass();
     //计算整个页面的列数，页面的宽度除以box的宽度

     var oBoxW = oBoxs[0].offsetWidth;//数值型，获得元素的宽度。

     var cols = Math.floor(document.documentElement.clientWidth/oBoxW);//获取页面的宽度

     //设置main的宽度
     oParent.style.cssText = 'width:'+oBoxW * cols + 'px;'+'margin:0 auto;';
     
     //第二行的第一个要出现在第一行的高度最小也就是最矮的元素的下面

     //所以需要一个数组将第一行中的每个元素的高度存起来。
     
     //第二行的第一个元素的top和left应该分别为高度最小的元素的offsetHeight + offsetLeft
     
     var hArr = [];
     for(var i=0;i &lt; oBoxs.length;i++){
          if(i&lt;cols){
               hArr.push(oBoxs[i].offsetHeight);
          }else{//已经到下一行,求出最小的那个元素
              var minH= Math.min.apply(null.hArr);
               var index = hArr.indexOf(minH);
               oBoxs[i].style.position = "absolute";
               oBoxs[i].style.top= minH + 'px';
               oBoxs[i].style.left= oBoxW * index +'px';
               //oBoxs[i].style.left= oBoxs[index] .offsetLeft +'px';
               hArr[index] + = oBoxs[i].offsetHeight;//回写数组更新再找到最小高度
          }
     }
     console.log(hArr)
     

}

//根据class获取元素
var getByClass = function(parentNode,className){
}


//判断是否请求新的数据,当最下面的box的一半进入窗口可视区域的时候 在进行新一轮的取数据
var checkScrollSlide = function(){
     var oParent = document.getElmentById("main");
     var oBoxs = getByClass('box');
     var lastBoxH = oBoxs[oBoxs.length -1].offsetTop + Math.floor(oBoxs[oBoxs.length -1].offsetHeight/2);
     //混杂模式 注意标准还是
     var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
     var height = document.body.clientHeight || document.documentElment.clientHeight;
     if(lastBoxH  &lt; scrollTop  + height ){
          return true;
     }else{
          return false;
     }
}

</pre>

二、用Jquery的方式来实现
其实这里所知的Jq方法，并非指的是用用jq插件而是用jq的方法重写原生的js方法。

<pre class="prettyprint lang-js linenums">

$(window).on('load',function(){
     waterfall();
     $(window).on("scroll",function(){

     var dataInt = {
          "data":[
               {"src":'****/img01.jpg'}
               {"src":'****/img02.jpg'}
               {"src":'****/img03.jpg'}
               {"src":'****/img04.jpg'}
               {"src":'****/img05.jpg'}
               {"src":'****/img06.jpg'}
               {"src":'****/img07.jpg'}
          ]
     }
          if(checkScroll){
               $.each(dataInt.data,function(key,value){
                    
               })
          }
     })
})

var waterfall = funtion(){
     $boxs = $("#mian &gt; div");
     var w = $boxs.eq(0).outerWidth();//可以获得填充的padding的宽度
     var cols = Math.floor($(window).width()/w);
     $('#main').width(cols * w).css("margin","0 auto");
     var hArr = [];
     $boxs.each(function(i,value){
          var h = $boxs.eq(index).outerHeight();
          if(i &lt; cols){
                 hArr.push(h)   
          }else{
               var  minH = Math.min.apply(null.hArr);
               var minIndex = $.inArray(minH,hArr);
               $(value).css({"postion":"absolute","top":minH + "px","left":minHindex * w + "px"});
              hArr[minIndex] += $boxs.eq(index).outlerHeight();
          }
     })     
}

var checkScroll = funtion(){
     var $lastBox = $("#main &gt; div").last();
     var lastBoxDis = $lastBox.offset().top + Math.floor($lastBox.height()/2);
     var scrollTop = $(window).scrollTop();
     var documentH = $(window).height();
     return (lastBoxDis &lt; scrollTop + documentH  ?true:false);
}
</pre>

三、用CSS3的新的布局模型来实现。

页面的结构依然保持不变，采用css3的多栏布局来实现。

<pre class="prettyprint lang-js linenums">

#main{
	/*列宽，css引擎会根据我们的列宽来计算我们的列的数量*/
    -webkit-columns-width:202px;
     -moz-columns-width:202px;
     -o-columns-width:202px;
     -ms-columns-width:202px;

    /*列与列之间的分割线*/
    -webkit-columns-rule:2px dashed #F00;
     -moz-columns-rule:2px dashed #F00;
     -o-columns-rule:2px dashed #F00;
     -ms-columns-rule:2px dashed #F00;

     /*列间距*/
    -webkit-columns-gap:5px;
     -moz-columns-gap:5px;
     -o-columns-gap:5px;
     -ms-columns-gap:5px;
     
}
.box{
     padding:10px 0 0 15px;
}
.pic{
     padding:10px; 
     border:1px sold #CCC;
     border-radius:5px;
     box-shadow:0 0 5px #CCC;
     width:165px;
}
.pic img{
     display :block;
     width:165px;
     height:auto;
}

</pre>


四、总结。
首先说说每一种方法的优缺点：

1.原生js来实现，

优点：可控性比较高，纯原生实现。

缺点：兼容性成本比较高，而且代码量相对交大。


2.Jquery来实现

优点：兼容性比较好，开发者熟悉的代码相对少一点。

缺点：可控性相对原生低一点。


3.css3实现

优点：性能高，代码量小

缺点：图片的排序不可控，滚动取数的逻辑依然需要js来控制，新标准的兼容性比较差。
