---
layout: post
title:  "ORACLE 笔记 ———— 阶乘的实现"
date:   2017-11-27 16:00:00 +0800
tag: sql
category: 2017年11月
description: "ORACLE 笔记,实现阶乘的两种方法,使用自定义函数与使用plsql实现"
keywords: 阶乘,factorial,oracle函数
type: coding
user: kevis
---

### ORACLE 笔记一
#### 函数与游标的使用

##### oracle实现阶乘的两种方法
###### 自定义函数实现
```
create or replace function factorial (start_dt in date,end_dt in date)
return number 
as 
factorial number:=1;
val number:=1;
cursor index_val
is 
  select * from table_name where data_dt between start_dt and end_dt ;
begin
  for myindex in index_val loop
      val := myindex.columnX;
      factorial := factorial * val;
  end loop;
  return factorial;
```
###### plsql实现

```
select exp(sum(ln(columnX)) from table_name;
```
在后续使用中，还需要了解以下几点：
- shell 连接 oracle / mysql
- oracle中编写 java (http应用)
- oracle 触发器、存储过程的使用
- oracle 跨库查询 创建 database link

