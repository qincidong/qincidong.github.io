---
layout: post
title:  MyEclipse中修改发布到Tomcat中的名称
description: MyEclipse中修改发布到Tomcat中的名称
date:   2015-01-16 20:04:39
categories: blog
tags: myeclipse tomcat project rename tools
---
其实并不是第一次遇到这个问题了，但是每次最开始都不知道怎么回事。  
在浏览器打开显示的就是一片空白，搞不清楚怎么回事。  
工程结构如下：
![http://uploadingit.com/file/7na3tzwbi534s80k/1.png](http://uploadingit.com/file/7na3tzwbi534s80k/1.png)

但是，添加工程到tomcat时，显示的名称并不是想要的。如下：
![http://uploadingit.com/file/view/kwwuvj7ggj1btt9a/2.png](http://uploadingit.com/file/kwwuvj7ggj1btt9a/2.png)

那么，怎么修改名称呢？？  
找到工程目录下的.mymetadata文件，打开，可以看到：  
![http://uploadingit.com/file/view/fjjvweei08zwmnum/3.png](http://uploadingit.com/file/fjjvweei08zwmnum/3.png)
修改context-root就OK了。

修改后就正常了。  
![img](http://uploadingit.com/file/vlxebzzddh00aiqt/4.png)