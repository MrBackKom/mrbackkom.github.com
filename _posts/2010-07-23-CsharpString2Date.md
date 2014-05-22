---
layout: post
title: "C#字符串和时间的相互转换"
description: "C#字符串和时间的相互转换"
category: C#
tags: ["C#","字符串和时间的相互转换"]
---

一、DateTime –> string
时间类型转化成字符串类型， 那是相当的简单，直接调用ToString()方法即可。如：
DateTime dt = DateTime.Now;
string dtStr = dt.ToString();
 
如果想对输出格式化，可以这么写：
dt.ToString("yyyy年MM月 dd日");        //2005年11月5日
dt.ToString("yyyy-MM-dd");                      //2005-11-5
string.Format("{0:d}",dt);                  //2005-11-5
string.Format("{0:D}",dt);                  //2005年11月5日
 
时间类型格式化（成字符类型）可以通过两种方式：1、自定义时间格式。自己定义时间的构成和表示；2、标准 时间格式。由
 标准库提供的有限的表示方式。（详细的情参考列表）。
二、string -> DateTime
string dtString = “2009-10-12 00:12:05”;
DateTime dt = DateTime.Parse(dtStr);                      //方式一
DateTime dt2 = Convert.ToDateTime(dtStr);              //方式二
 
当然DateTime也有多种表示方式 （非格式化成字符串类型），如：
dt.ToFileTimeUtc();                  //127756704859912816
dt.ToLocalTime();                    //2005-11-5 21:21:25
dt.ToLongDateString();            //2005年11月5日
 
当然如果 最后要打印出来，就需要ToString()一下，转化成字符串类型。
 
三、时间的其它方法，属性和运算
dt.Year //2005
dt.Date ......
