---
layout:       post
title:        解决Mac系统下 iTunes无法连接iPhone(iPad)，因为收到来自此设备的无效响应
description: 
banner: 
categories: 
- 玩意儿
---

<br />

iPhone越狱后一直无法使用数据线连接到Mac电脑，弹出类似于下面的窗口，在中文网上没有找到解决方案，一般都是在xp或win7电脑下的解决方法，

![代码](http://ww2.sinaimg.cn/large/61bf40fagw1ecv1y969ncj20gs07qjro.jpg)

[这篇文章](http://w1nd.me/2014/01/24/response2itunes/)提出了

    WIN7：删除C:/programdata/apple/lockdown
    XP：删除C:\Documents and Settings\All Users\Application Data\Apple\lockdown
    iOS1：删除var/root/Library/Lockdown

担心删除iOS激活后会有问题，使用C:/programdata/apple/lockdown作为关键词进行搜索，找到了对应的Mac下的目录/private/var/db/lockdown，删除该文件夹下的内容，又弹出“没有权限”，最后在[这个网站](https://discussions.apple.com/message/25924500#25924500)得知是因为上述目录的权限问题，连接时不能写入plist，将其改为777即可

发现很多朋友遇到类似的问题，写出来希望被搜索引擎索引到。

