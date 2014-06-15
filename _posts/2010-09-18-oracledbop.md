---
layout: post
title: "Oracle中创建表，创建序列，创建自增字段，添加注释， 添加记录，添加触发器，提交 "
description: "Oracle中创建表，创建序列，创建自增字段，添加注释， 添加记录，添加触发器，提交"
category:  Oracle
tags: ["Oracle","Oracle中创建表","Oracle创建序列"]
---

#

<pre class="prettycode lang-js linenum" >
// 创建一个序列
CREATE SEQUENCE Car_GUID increment by 1;
// 创建一个表
CREATE TABLE Car
(
 GUID NUMBER NOT NULL PRIMARY KEY,
 PhoneId Char(11) NOT NULL,
 UserName VarChar(20) NOT NULL
);
// 添加注释
comment on table car is '定位信息用户表';
Comment on column car.guid is '自增字段';
Comment on column Car.PhoneId is '电话唯一标识';
Comment on column Car.UserName is '用户名称';
// 添加一条记录
INSERT INTO Car(GUID, PhoneId, UserName) Values(CAR_GUID.nextval, '13589256783', '张三');
// 提交
commit;

// 触发器
create or replace trigger Trigger_Car_GUID_Insert  
before insert on Car 
for each row
begin
 select CARGUIDSEQ.nextval into  :new.GUID from sys.dual;
end;

</pre>
