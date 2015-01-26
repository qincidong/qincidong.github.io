---
layout: post
title:  eclipse启动tomcat出现OOM（out of memory）
description: eclipse启动tomcat出现OOM（out of memory）
date:   2015-01-26 14:49:39
categories: blog
tags: eclipse tomcat oom
---
1）双击Server面板的tomcat图标；
![eclipse启动tomcat出现OOM（out of memory）](http://uploadingit.com/file/hiir34ndfstxuytb/eclipse_tomcat_oom1.png)
2） 点击“Open launch configuration”； 
![http://uploadingit.com/file/1yacizt0kye1llla/eclipse_tomcat_oom2.png](http://uploadingit.com/file/1yacizt0kye1llla/eclipse_tomcat_oom2.png)
3）在Arguments面板的VM arguments中增加：
![http://uploadingit.com/file/mirkrivgst0nkjrm/eclipse_tomcat_oom3.png](http://uploadingit.com/file/mirkrivgst0nkjrm/eclipse_tomcat_oom3.png)
<pre>
-Xms1024m -Xmx1024m -Xss512k -XX:PermSize=64M -XX:MaxNewSize=256m -XX:MaxPermSize=128m -Djava.awt.headless=true
</pre>
ps:本文转自[http://qq85609655.iteye.com/blog/752319](http://qq85609655.iteye.com/blog/752319)