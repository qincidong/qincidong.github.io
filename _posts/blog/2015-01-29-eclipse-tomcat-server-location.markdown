---
layout: post
title:  eclipse中server location灰色，如何修改
description: eclipse中server location灰色，如何修改？
date:   2015-01-29 13:42:39
categories: blog
tags: eclipse tomcat
---
Eclipse中tomcat service设置  
选择window ----show view－－－services可以看到服务的面板  
双击tomcat进入配置界面Service Locations（Specify the server path (i.e. catalina.base) and deploy path.   
Server must be published with no modules present to make changes.）选项变灰色无法更改配置。  
若要更改，则先把tomcat下的所有項目移除。并右击，clean...之后方可设置。。。启动后将又变为黑色。  
默认选项为：  
Use workspace metadata(dose not modify Tomcat installation)  
修改选项为：  
Use Tomcat installation(takes control of Tomcat installation)  
![img](http://www.eddll.com/uploadfile/2014/1124/20141124095109102.jpg)
这样在Eclipse启动了tomcat服务器，我們也能夠访问到tomcat本地管理首页。
否则只能在DOS下使用Tomcat的启动命令才能访问Tomcat本地管理首页。

ps:本文转自[http://www.eddll.com/index.php?m=content&c=index&a=show&catid=137&id=1463](http://www.eddll.com/index.php?m=content&c=index&a=show&catid=137&id=1463)
