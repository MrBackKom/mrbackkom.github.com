---
layout: post
title: "HTML5地理位置教程(2) GeoLocation API[GeoLocation (HTML5 Geolocation)] "
description: ""
category: "HTML5新标准"
tags: [html5,地理位置,geolocation]
---

从本篇文章开始，我们将开始具体的讲述W3C Geolocation API

###一、纬度和经度
我们先看一张图 来理解一下什么是纬度、经度的概念。
![实例图片](http://img0.ph.126.net/0CkC064SvQOCj1vtTtKipA==/1898548718013533784.jpg)
水平方向的先就是纬线，每条线之间的间隔相差69英里，计算单位是度 从赤道开始以零度开始，到地球的两极为90度。
垂直方向的是经线，经线贯穿地球的南北两极，同样每两条之间的差距是69英里。以本初子午线作为0度经线。

###二、 w3c Geolocation API

　　　通过使用w3c Geolocation API，脚本是能够有能力在授权机器上访问相应的位置信息的。这是一个高级API，开发人员不需要考虑过于细节的东西，可以方便的获取地理位置信息，所以无论是通过mac地址、IP、还是GPS，你不用去想他到底是什么方式获取的，只管用就ok了。但有一点你需要注意，由于受到设备或其他因素限制，返回的位置信息并不一定是真正的实际位置，比如在局域网内的PC设备。

###三、当前Geolocation API的支持情况

	Firefox 3.5+

	Chrome 5.0+

	Internet Explorer 9.0+

	Safari 5.0+a

	Opera 10.6+

	iPhone 3.1+

	Android 2.0+

	BlackBerry 6+

在这些浏览器上你是可以使用Geolocation API的，但对于不支持Geolocation API的浏览器上你该如何取做呢？

###四、如果浏览器不支持Geolocation API，该怎么办？

幸运的是，其他开发人员封装了相应的Geolocation库去提供这种获取位置信息的能力，但这些库和标准的Geolocation API相比是有很大差别的而且对于开发人员来说写一套跨所有浏览器的库是一件极具挑战性的。我们来看一些这样的库文件。

##1.Gears

通常大家叫他 google Gears，他是由谷歌提供的工具库，源于一个开源项目，这个开源项目的目的是为浏览器添加高级特性的。

在Gears组件当中，最令人感兴趣的就是Geolocation module，这个模块和w3c Geolocation API是十分相似的，事实上，w3c Geolocation API 原型正是基于Gears。使用方式很简单一行引用
&lt;script type="text/javascript" src="http://code.google.com/apis/gears/gears_init.js"&gt;&lt;/script&gt;

##2.geo-location-javascript

geo-location-javascript，尝试搭建一个能够将所有底层平台差异包装在一起封装成一套简单的API，和w3c的GeolocationAPI十分相似，目前以下这些平台是支持geo-location-javascript框架的

IOS

Android

BlackBerry OS

Gears

Nokia web Runtime

web OS

Mozilla Geode

这个API库包含两个主要的方法：一个是检测你当前使用的设备是否具备定位能力，另一个是定位你的位置。这个API库可以视为是w3c Geolocation API的一个子集，在使用geo-location-javascript之前，你需要做另外一件事。

首先，我们现在应用程序中载入了Gears框架，接着我们再载入geo-location-javascript API。在实际应用程序中，initialize()方法会在onload事件中被调用。

initialize()方法是一个十分重要的前置条件。他所实现的功能就是去检测你的设备是否能个使用定位，如果可以使用定位功能，他就会去尝试定位当前位置。如果设备不支持定位，之后将会给出一个回馈给用户，当然这里的提示是有些生硬的，可以在实际开发中去设计一下。

和其他的地理位置API一样，geo-location-javascript API也包括w3c Geolocation API，在定位之前一定需要用户允许才可以后续处理。

geo_position_js.getCurrentPosition()，这个方法中包含两个回调函数，show_position 和 error_handler.分别对应，成功和失败回调。如果在定位设备位置的时候出现错误，或者用户拒绝定位，取消定位时，error_handler回调将会被触发。否则成功的回调就会被调用。并且设备纬度和经度就可以被程序使用了。

PS：有一点是十分值得注意的，geo-location-javascript API和 w3c Geolocation API 有一点是不一样的，那就是它不像w3c Geolocation API一样支持地理位置的长轮训。如果你需要实现这样的功能，你需要通过setInterval不停地去轮训调用getCurrentPosition()方法。