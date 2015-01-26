---
layout: post
title:  将普通Java工程转换为Web工程
description: 将普通Java工程转换为Web工程
date:   2015-01-26 16:49:39
categories: blog
tags: project web eclipse
---
把一个普通的eclipse项目转成web项目  
1. 编辑工程的.project文件。
添加
{% highlight xml %}
<nature>org.eclipse.wst.common.project.facet.core.nature</nature> 
{% endhighlight %}

到
{% highlight xml %}
<natures>
...  
</natures>
{% endhighlight %}

2. 刷新项目,右击项目–>Properties—>Project Facets，在右边勾选Java、javascript(非必须)与Dynamic Web Module

3. 如果你的项目源代码文件夹不是WTP默认的src，则需要到Java Build Path中去添加相应源代码文件夹。
 
4. 如果你的项目Web文件夹不是WTP默认的WebContent，则需要修改文件。
 
打开文件: <项目目录>/.settings/org.eclipse.wst.common.component，
 
找到
{% highlight xml %}
<wb-resource deploy-path="/" source-path="/WebContent"/>
{% endhighlight %}

并修改，如
{% highlight xml %}
<wb-resource deploy-path="/" source-path="/web"/>
{% endhighlight %}

ps:本文转自[http://qq85609655.iteye.com/blog/752319](http://qq85609655.iteye.com/blog/752319)