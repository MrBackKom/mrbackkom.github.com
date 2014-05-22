---
layout: post
title: "BackgroundWorker组件"
description: "在VS2005中添加了BackgroundWorker组件，该组件在多线程编程方面使用起来非常方便，然而在开始时由于没有搞清楚它的使用机制，走了不少的弯路，现在把我在使用它的过程中的经验与诸位分享一下"
category:  .net控件相关
tags: [" .net控件相关","BackgroundWorker组件"]
---

在VS2005中添加了BackgroundWorker组件，该组件在多线程编程方面使用起来非常方便，然而在开始时由于没有搞清楚它的使用机制，走了不少的弯路，现在把我在使用它的过程中的经验与诸位分享一下。
BackgroundWorker类中主要用到的有这列属性、方法和事件：
重要属性：
1、CancellationPending              获取一个值，指示应用程序是否已请求取消后台操作。通过在DoWork事件中判断CancellationPending属性可以认定是否需要取消后台操作（也就是结束线程）；
2、IsBusy                           获取一个值，指示 BackgroundWorker 是否正在运行异步操作。程序中使用IsBusy属性用来确定后台操作是否正在使用中；
3、WorkerReportsProgress            获取或设置一个值，该值指示BackgroundWorker能否报告进度更新
4、WorkerSupportsCancellation      获取或设置一个值，该值指示 BackgroundWorker 是否支持异步取消。设置WorkerSupportsCancellation为true使得程序可以调用CancelAsync方法提交终止挂起的后台操作的请求；
重要方法：
1、CancelAsync          请求取消挂起的后台操作
2、RunWorkerAsync       开始执行后台操作
3、ReportProgress       引发ProgressChanged事件  
重要事件：
1、DoWork               调用 RunWorkerAsync 时发生
2、ProgressChanged      调用 ReportProgress 时发生
3、RunWorkerCompleted   当后台操作已完成、被取消或引发异常时发生
另外还有三个重要的参数是RunWorkerCompletedEventArgs以及DoWorkEventArgs、ProgressChangedEventArgs。
BackgroundWorker的各属性、方法、事件的调用机制和顺序：

![实例图片](http://img.blog.163.com/photo/EiXVGEXWQmIgpqew1hItyA==/577586652211623213.jpg);

从上图可见在整个生活周期内发生了3次重要的参数传递过程：
参数传递1：此次的参数传递是将RunWorkerAsync(Object)中的Object传递到DoWork事件的DoWorkEventArgs.Argument,由于在这里只有一个参数可以传递，所以在实际应用往封装一个类，将整个实例化的类作为RunWorkerAsync的Object传递到DoWorkEventArgs.Argument；
参数传递2：此次是将程序运行进度传递给ProgressChanged事件，实际使用中往往使用给方法和事件更新进度条或者日志信息；
参数传递3：在DoWork事件结束之前，将后台线程产生的结果数据赋给DoWorkEventArgs.Result一边在RunWorkerCompleted事件中调用RunWorkerCompletedEventArgs.Result属性取得后台线程产生的结果。
另外从上图可以看到DoWork事件是在后台线程中运行的，所以在该事件中不能够操作用户界面的内容，如果需要更新用户界面，可以使用ProgressChanged事件及RunWorkCompleted事件来实现。
