---
layout: post
title: "HHTML5 WebWorker教程(4) 共享webworkers  "
description: "共享web worker，允许多个web应用程序通过一个共享web worker进行切换。其中有一种使用共享webworker的方式是让共享webworker做多个web应用程序的中间层，介于服务器与应用程序间通信。可以创建多个web worker，通过共享web worker来查看同一个图片。代替了与服务器的直接通信，web app将会和共享buffer进行通信，这些数据都是缓存在本地的数据，当连线的时候将会和服务器进行通信"
category: "HTML5新标准"
tags: [html5,WebWorker]
---

共享web worker，允许多个web应用程序通过一个共享web worker进行切换。其中有一种使用共享webworker的方式是让共享webworker做多个web应用程序的中间层，介于服务器与应用程序间通信。可以创建多个web worker，通过共享web worker来查看同一个图片。代替了与服务器的直接通信，web app将会和共享buffer进行通信，这些数据都是缓存在本地的数据，当连线的时候将会和服务器进行通信。

基于你的web 应用程序的逻辑，web worker可以使用html5的离线存储能力，将数据化持久化并与服务器端进行通信。这样在mvc模式下你的model就被优雅的封装在了中心位置。使用共享web worker 这种方式还有其他的好处。

1.可移植性好。2.保持了多窗口数据的一致性。3.减少了内存占用。
