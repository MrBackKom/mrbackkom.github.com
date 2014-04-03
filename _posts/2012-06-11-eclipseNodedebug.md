---
layout: post
title: "eclipse 调试nodejs 发生Failed to connect to standalone V8 VM错误的解决方案"
description: "对于语言学习来说，有一个好的调试工具，能够大大提高语言的学习效率，本人在学习nodejs的时候，很想调试nodejs，看看代码究竟是怎么运行的，于是乎查找如何调试nodejs，发现有很多朋友用eclipse添加 google v8调试插件就可调试了，于是也尝试了下，感觉还不错，毕竟要比干巴巴的debug好的多，至于如何搭建调试环境，我就不多说了，请看下面这个链接中的文章就好了，文章虽然是英文写的，但图文共存，不难理解。"
category: 工具使用
tags: ["eclipse","Node调试"]
---

　　　对于语言学习来说，有一个好的调试工具，能够大大提高语言的学习效率，本人在学习nodejs的时候，很想调试nodejs，看看代码究竟是怎么运行的，于是乎查找如何调试nodejs，发现有很多朋友用eclipse添加 google v8调试插件就可调试了，于是也尝试了下，感觉还不错，毕竟要比干巴巴的debug好的多，至于如何搭建调试环境，我就不多说了，请看下面这个链接中的文章就好了，文章虽然是英文写的，但图文共存，不难理解。

　　　[https://github.com/joyent/node/wiki/Using-Eclipse-as-Node-Applications-Debugger](https://github.com/joyent/node/wiki/Using-Eclipse-as-Node-Applications-Debugger 'tips')

　　　但估计很多人也在调试的时候发现，按照文章中说的第一次调试是成功的，等在第二次调试的时候却发生了这样的错误，如下图：

![实例图片](http://pic002.cnblogs.com/images/2012/243908/2012061123060442.png)

　　　其实这个错误的原因很简单，就是提示框中所提到的无法连接到v8 虚拟机。解决他的方法也很简单，

　　　首先确保你的node服务已经启动：

　　　mrbackkom$ node --debug app-node.js

　　　debugger listening on port 5858

　　　listening to http://localhost:8124

　　　其次、点击debug 昆虫图标旁边的小三角按钮 选择debug configurations，之后点击你曾经建立过的v8虚拟机，再次点击右下角 debug按钮，又可以重新调试了。