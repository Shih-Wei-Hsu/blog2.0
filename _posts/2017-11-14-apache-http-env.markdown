---
layout: post
title:  "windows64位下apacheHttpServer2.4.x环境搭建与配置"
date:   2017-11-14 16:00:00 +0800
tag: http
category: 2017年11月
description: "windows64位下apacheHttpServer2.4.x环境搭建与配置，测试一个小demo"
keywords: httpd,cgi.apache http,http服务,windows64位,python cgi
type: coding
user: kevis
---

### 一、下载apache http server
apache http server：[https://httpd.apache.org/docs/current/platform/windows.html#down](https://httpd.apache.org/docs/current/platform/windows.html#down)
```   
        这里提供了三个站点下载，推荐Apache Lounge镜像站点快
```
### 二、安装配置apache http server
![apacheHttpServer解压路径](/images/apacheHttpServer.png)
```
        第一步下载后直接解压，我的解压路径为：E:/study/apacheHttp/Apache24,如上图
        
        然后进行配置，打开 conf/httpd.conf文件，修改以下几处:
        1.将所有的 c:/apache 替换为 apacheHttpServer解压路径,如 E:/study/apacheHttp/Apache24
        2.找到Listen 80 将 端口改为 8080,访问端口
        3.找到 ServerName www.example.com:80 ,去掉 #
        4.找到 ScriptAlias /cgi-bin/ "c:/Apache24/cgi-bin/" ,将其替换为 ScriptAlias /cgi-bin/ "e:/study/apacheHttp/Apache24/cgi-bin/"
        5.找到 AddHandler cgi-script .cgi,这个是cgi所允许的脚本文件后缀,下文会用python写CGI,需要添上 .py 在后面
        修改后,以管理员身份运行CMD,进入Apache24\bin 目录,输入 httpd.exe -k install -n "Apache24"  安装服务（注：Apache24为安装服务名称）
        
```
### 三、启动apache http server
```    
        执行/bin/httpd.exe,然后浏览器输入 http://localhost:8080/,页面出现 It works! 则说明启动成功
```   
### 四、CGI Demo
```
       修改/htdocs/index.html(服务主页面):
       添加一个 a 链接 <a href="cgi-bin/hello.py?name=xushiwei" >谁啊？</>
```

```
       使用python编写CGI脚本
       示例 hello.py :
      1  #! E:/study/python/python.exe
      2  # -*- coding: UTF-8 -*-
      3  # CGI处理模块
      4  import cgi, cgitb 
      5  # 创建 FieldStorage 的实例化
      6  form = cgi.FieldStorage() 
      7  # 获取数据
      8  site_name = form.getvalue('name')
      9  print("Content-type: text/html\n\n")
      10 print("hello world")
      11 print(site_name )
```

```
        上述示例中:
               第一行 指定python解释器,路径对应到自己的python安装路径。
               第二行 指定脚本编码格式,若脚本中出现中文,必须指定UTF-8格式
               第九行 指定响应的文件类型,必须指定
        如不满足上述条件,可能会得到500内部错误页面
```    
### 五、启动服务,Demo结果

```
        浏览器进入服务,点击主页面的 a 链接,跳转到 hello.py,得到 hello world xushiwei.如图:
    
```
![hello world xushiwei](/images/helloxsw.png)  
#### 小笔记
```
    1.在httpd.conf中可以指定服务主页面,DirectoryIndex index.html
    2.如果仅仅想开放自己某个文件夹,可以在CMD中使用 python -m http.server 9000 命令开启服务,他人就可通过http://yourhost:9000访问到你的目录
    3.python cgi文件头部第一行解释器的路径一定要写对，尤其要注意 ！号，必须是英文字符，不然报错：AH01222: don't know how to spawn child process
    4.卸载Apache24服务，必须用管理员身份打开CMD,在bin目录下，执行httpd.exe -k uninstall –n “Apache24” 命令卸载服务
```