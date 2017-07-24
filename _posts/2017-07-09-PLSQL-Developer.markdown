---
layout: post
title:  "PLSQL Developer 打开表后中文为??? 乱码问题解决"
date:   2017-07-09 23:05:00 +0800
tags: xsw sql
category: 2017年7月
description: "PLSQL Developer 打开表后中文为??? 乱码问题解决"
keywords: plsql,乱码
---
### 问题描述: 
```
本地只安装了 PLSQL Developer ,没有安装Oracle数据库. 
连的是服务器端的数据库.PLSQL Developer打开表之后中文是乱码的. 
运行sql脚本也是乱码.
```
### 解决方案: 
在一篇帖子中找到解决办法,[http://bbs.csdn.net/topics/190048377](http://bbs.csdn.net/topics/190048377) 
是在本地配置环境变量 
```
LANG=zh_CN.GBK 
NLS_LANG=”SIMPLIFIED CHINESE_CHINA.ZHS16GBK”
```  


