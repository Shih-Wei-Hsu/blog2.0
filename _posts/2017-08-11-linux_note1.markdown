---
layout: post
title:  "如何修改linux的启动级别"
date:   2017-08-10 21:00:00 +0800
tag: xsw note
category: 2017年8月
description: "如何修改linux的启动级别"
keywords: linux,启动级别
type: coding
user: kevis
---

### linux下有一下启动级别：

```
# 0 - halt (Do NOT set initdefault to this) 
# 1 - Single user mode 
# 2 - Multiuser, without NFS (The same as 3, if you do not have networking) 
# 3 - Full multiuser mode 
# 4 - unused 
# 5 - X11 
# 6 - reboot (Do NOT set initdefault to this)
```

```
0：停机(不要设置为启动默认级别)
1：单用户模式
2：多用户，无NFS(如果您没有配置网络，该级别与3一样)
3：完全多用户模式
4：不使用
5：X11图形模式
6：重启(不要设置为启动默认级别)
```

要修改启动级别，需进入到/etc目录下，然后vi inittab进行修改；

```
id:5:initdefault:
......
```

如果想让系统不用图形模式登陆可将id:5:initdefault: 中的5改为3即可，保存退出。
