---
layout: post
title:  "sqoop从oralce导入hive错误问题解决（日期格式转换）"
date:   2017-07-13 19:00:00 +0800
categories: xsw hadoop
description: "sqoop从oralce导入hive错误问题解决（日期格式转换）"
keywords: sqoop,hive,oracle,hadoop,sqoop导入
---

# sqoop从oralce导入hive错误问题解决（日期格式转换）

```
Error: java.io.IOException: SQLException in nextKeyValue
	at org.apache.sqoop.mapreduce.db.DBRecordReader.nextKeyValue(DBRecordReader.java:277)
	at org.apache.hadoop.mapred.MapTask$NewTrackingRecordReader.nextKeyValue(MapTask.java:556)
	at org.apache.hadoop.mapreduce.task.MapContextImpl.nextKeyValue(MapContextImpl.java:80)
	at org.apache.hadoop.mapreduce.lib.map.WrappedMapper$Context.nextKeyValue(WrappedMapper.java:91)
	at org.apache.hadoop.mapreduce.Mapper.run(Mapper.java:144)
	at org.apache.sqoop.mapreduce.AutoProgressMapper.run(AutoProgressMapper.java:64)
	at org.apache.hadoop.mapred.MapTask.runNewMapper(MapTask.java:787)
	at org.apache.hadoop.mapred.MapTask.run(MapTask.java:341)
	at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:164)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:415)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1709)
	at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:158)
Caused by: java.sql.SQLException: ORA-01861: 文字与格式字符串不匹配
```


##### 问题：数据切分字段使用日期类型，在导入的时候报错！ORA-01861: 文字与格式字符串不匹配，即日期格式不匹配，转换失败！


##### 解决办法：将该切分字段改成 string 类型，并修改sqoop参数配置--split-by "to_char(D_DATE,'yyyy-mm-dd')"，成功解决问题！

