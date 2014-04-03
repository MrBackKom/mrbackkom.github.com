---
layout: post
title: "HTML5 FileAPI"
description: "在HTML5中，提供了一个关于文件操作的API，通过这个API，对于从web页面上访问本地文件系统的相关处理变得十分简单。到目前为止只有部分浏览器对它提供支持。1.FileList对象和File对象 FileList对象表示用户选择的文件列表，在HTML4中file控件内只允许放置一个文件，但在HTML5中通过添加multiple属性，file控件内允许放置多个文件。控件内的每一个用户选择的文件都是一个file对象，而FileList就是这些file对象的列表，代表用户选择的所有文件。file对象有两个属性，一个是name，代表文件名不包含文件的路径；一个是lastModifiedDate"
category: JS标准
tags: ["fileAPI"]
---

　　　在HTML5中，提供了一个关于文件操作的API，通过这个API，对于从web页面上访问本地文件系统的相关处理变得十分简单。到目前为止只有部分浏览器对它提供支持。

　　　1.FileList对象和File对象

　　　FileList对象表示用户选择的文件列表，在HTML4中file控件内只允许放置一个文件，但在HTML5中通过添加multiple属性，file控件内允许放置多个文件。控件内的每一个用户选择的文件都是一个file对象，而FileList就是这些file对象的列表，代表用户选择的所有文件。file对象有两个属性，一个是name，代表文件名不包含文件的路径；一个是lastModifiedDate，表示文件最后被修改的日期。


<pre class="prettyprint lang-js linenums">
&lt;input type="file" id='file' multiple="multiple" width="80px"/&gt;

&lt;input type="button" id="bt1" value="click" onclick="showFiles();"/&gt;

function showFiles(){
   var file,
       len = document.getElementById('file').files.length;//返回FileList文件列表对象
       for (var i=0; i &lt; len; i++) {
           file = document.getElementById('file').files[i];
           alert(file.name);
       };                 
}               
</pre>

　　　2.Blob对象

　　　提到Blob对象，估计有人会想起OracleDB中Blob字段，意义上有些类似。HTML5中Blob表示二进制原始数据，它提供一个slice()方法，可以通过这个方法访问到字节内部的原始数据块。事实上，上面提到的file对象继承了Blob对象。

　　　Blob对象的两个属性，size：表示一个对象的字节长度。type：表示一个对象的MIME类型，如若是未知类型返回空字符串。

<pre class="prettyprint lang-js linenums">
function showFileInfo(){
    var file = document.getElementById('file').files[0];
    var size = document.getElementById('fileType');
    var type = document.getElementById('fileSize');
    size.innerHTML = file.size;
    type.innerHTML = file.type;
} 
</pre>

![实例截图](http://pic002.cnblogs.com/images/2012/243908/2012031923210145.jpg)

　　　对于图像类型的文件，Blob对象的type属性都是以image/开头，可以利用这个特性对用户选择的文件类型做判断。

<pre class="prettyprint lang-js linenums">
            function showFileInfo(){
                var file = document.getElementById('file').files[0];
                if(checkImage(file)){
                var size = document.getElementById('fileType');
                var type = document.getElementById('fileSize');
                size.innerHTML = file.size;
                type.innerHTML = file.type;
                }
                else{
                    return ;
                }
            }
            function checkImage(file){
                if(!/img\/\w+/.test(file.type)){
                    alert(file.name + "不是图片");
                    return false;
                }
                return true;
            }
</pre>

　　　另外，file控件在HTML5标准中添加了accept属性，用来限制接受的文件类型，但目前各浏览器对齐支持都仅限于在打开文件选择窗口时默认的选择图像文件而已，如果选择其他类型，控件也能接受。

　　　3.FileReader接口

　　　FileReader接口提供了四个方法，其中3个用来读取文件，1个用来中断文件的读取。

<table>
<tr>
	<td> *方法名* </td> <td> *参数* </td> <td> *描述* </td> 
</tr>
<tr>
   <td> readAsBinaryString() </td> 
   <td> file </td> 
   <td> 将文件读取为二进制字符串，通常将它传到后端，后端可以通过这段字符串存储文件 </td> 
</tr>
<tr>
	<td> readAsDataURL() </td>
	<td> file </td>
	<td> 将文件读取为一段dataurl字符串，事实上是将小文件以一种特格式的URL地址直接读取到页面。小文件通常指图片与html等格式文件 </td>
</tr>
<tr>
    <td> readAsText() </td> 
    <td> file [encoding]</td> 
    <td> 将文件以文本的方式读取，其中第二个参数为文本的编码。 </td>
</tr>

<tr>
	<td> abort() </td>
	<td> (none) </td> 
	<td> 中断读取操作。 </td>
</tr>

</table>

　　　需要注意的是，无论读取成功还是失败，方法都不会返回读取结果，结果返回在result属性中。

　　　3.2接口事件

　　　FileReader接口提供了一套完整的事件模型，用于捕获读取文件时的状态。

<table>
<tr><td>事件</td><td>描述</td></tr>
<tr><td>onabort</td><td>数据读取中断时发生</td></tr>
<tr><td>onerror</td><td>数据读取出错时发生</td></tr>
<tr><td>onloadstart</td><td>数据读取开始时发生</td></tr>
<tr><td>onload</td><td>数据读取成功完成时发生</td></tr>
<tr><td>onloadend</td><td>数据读取完成时发生无论读取成功还是失败</td></tr>
<tr><td>onprogess</td><td>数据读取中</td></tr>	
</table>




	        	

	         	

	                	
 

 

 

　



