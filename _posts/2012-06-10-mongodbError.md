---
layout: post
title: "关于couldn't connect to server 127.0.0.1 shell/mongo.js:84 exception: connect failed 问题"
description: "今天在启动mongodb的时候，突然间发现这个错误 couldn't connect to server 127.0.0.1 shell/mongo.js:84 exception: connect failed,觉得很奇怪,之前没有遇到过,在google还有百度上搜一下,发现也有很多人遇到这个问题。"
category: mongodb常见错误
tags: [mongodb]
---


　　　今天在启动mongodb的时候，突然间发现这个错误 couldn't connect to server 127.0.0.1 shell/mongo.js:84 exception: connect failed,觉得很奇怪,之前没有遇到过,在google还有百度上搜一下,发现也有很多人遇到这个问题。

　　　解决方式按照搜索的结果分为两种：

　　　一是删除，dbpath下的mongod.lock,然后重启，服务。

　　　二是检查服务是否启用。


　　　也许是我操作不对，也许是我没领会人家的解决方案，木有解决这个问题，后来经过思考终于找到原因，其实与第二中错误原因相同，但是由于mongo没有将dbpath设成默认启动时访问的db路径，导致找不到数据库存放路径，而导致服务启动时出错。


　　　解决方式先终端中输入：

　　　$ ./mongod -dbpath=../data/db

　　　之后新开终端启动 mongo

　　　zhaomatoMacBook-Pro:bin mrbackkom$ ./mongo

　　　MongoDB shell version: 2.0.6

　　　connecting to: test
　　　
