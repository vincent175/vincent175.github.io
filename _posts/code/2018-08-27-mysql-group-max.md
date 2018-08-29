---
layout: post
title: MySQL分组取最值
category: code
date: 2018/8/28/星期二 17:31:54
description: MySQL分组取最大(最小、最新、前N条)条记录
---

> 在数据库开发过程中，我们要为每种类型的数据取出前几条记录，或者是取最新、最小、最大等等，这个该如何实现？


```sql
create table tb (name vatrchar(10), val int, memo varchar(20))

insert into tb values('a',    2,   'a2')
insert into tb values('a',    1,   'a1')
insert into tb values('a',    3,   'a3')
insert into tb values('b',    1,   'b1')
insert into tb values('b',    3,   'b3')
insert into tb values('b',    2,   'b2')
insert into tb values('b',    4,   'b4')
insert into tb values('b',    5,   'b5')
```

#### 按name分组取val最大的值所在行的数据
###### 方法一：
`select t1.* from tb t1 where val = (select max(t2.val) from tb t2 where t2.name = t1.name) order by t1.name`
###### 方法二：
`select t1.* from tb t1 where not exists(select 1 from tb t2 where t2.name = t1.name and t2.val > t1.val)`
###### 方法三：
`select t1.* from tb t1,(select t2.name,max(t2.val) val from tb t2 group by name) t3 where t1.name = t3.name and t1.val = t3.val order by t1.name`
###### 方法四：
`select t1.* from tb t1 inner join (select t1.name, max(t1.val) val from tb t2 group by name) t3 on t1.name = t3.name and t1.val = t3.val order by t1.name`
###### 方法五：
`select t1.* from tb t1 where 1 > (select count(*) from tb where name = t1.name and val > a.val) order by t1.name`

推荐使用1,3,4种方法，效率稍高一些，第2,5种方法效率稍差一些。

....未完待续


