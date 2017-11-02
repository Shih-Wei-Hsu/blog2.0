---
layout: post
title:  "HBuilder开发python环境搭建"
date:   2017-10-16 16:00:00 +0800
tag: python
category: 2017年10月
description: "HBuilder开发python环境搭建"
keywords: python,HBuilder,环境搭建
type: coding
user: kevis
---

### 一、下载HBuilder

>    HBuilder官网：[http://www.dcloud.io/](http://www.dcloud.io/)

### 二、下载安装Python

>    Python官网：[Python所有版本](https://www.python.org/downloads/windows/),环境变量在安装过程中会自动配置

### 三、下载PyDev插件
    
>    PyDev官网：[PyDev所有版本](http://www.pydev.org/update_sites/)
    
### 四、安装PyDev插件

     1. 将features文件下的所有内容粘贴到HBuilder的features文件夹下。
     2. 将plugins文件下的所有内容粘贴到HBuilder的plugins文件下。

### 五、配置Python解释器

```
    路径：
       HBuilder->工具->选项->PyDev->Interpreters->Python Interpreters
    配置：
       Interpreter Name : 自定义
       Interpreter Executable : 选择Python的安装目录
```
