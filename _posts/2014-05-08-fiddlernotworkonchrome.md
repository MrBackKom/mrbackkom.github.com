---
layout: post
title: "fiddler无法抓取chrome解决方法(转载)"
description: ""
category:  开发工具
tags: ["开发工具常见问题","fiddler"]
---

[原文地址](http://blog.csdn.net/spring21st/article/details/7662327)

前端开发中，不可避免的要和服务器端进行联调，少了fiddler这个利器可不行。



由于无线开发需要配置UA，我使用chrome进行访问，但是今儿一早过来，发现fiddler无法抓取chrome的请求了。

想想昨天对chrome进行的操作，我觉得问题应该出在代理的身上。



实际上fiddler是可以抓chrome的请求的。
由于我的chrome安装了代理管理的插件SwitchySharp，无论选择直接连接还是选择使用代理连接，插件都会屏蔽fiddler的设置。
fiddler会自动给浏览器设置一个代理127.0.0.1 端口8888，并且记忆浏览器的代理设置，所有的请求先走fiddler代理，再走浏览器代理。
如果使用插件，可能会直接屏蔽了fiddler的代理，因此无法监听到请求了。

chrome下的解决方法，代理插件选择“使用系统代理设置”选项，fiddler又重新能看到chrome的请求了。
或者不使用插件，不用卸载，chrome很方便禁用一个插件。然后使用浏览器默认的代理设置方式就ok了。

使用代理插件是为了方便切换代理，但是可能会导致fiddler等工具无法使用。真是鱼和熊掌不可兼得。
