---
layout: post
title:  invalid value for import
description: java.lang.IllegalArgumentException: Page directive: invalid value for import
date:   2015-01-21 15:43:39
categories: blog
tags: jsp tomcat exception
---
我的项目原来用的tomcat版本是apache-tomcat-7.0.53，后来为了安全原因将版本升至\apache-tomcat-7.0.57,发现有的jsp页面出现下面的异常：

java.lang.IllegalArgumentException: Page directive: invalid value for import

仔细检查发现是<%@ page import=" java.util.*;"%>一句引起的错误。把<%@ page import=" java.util.*;"%>中的分号去掉就好了，改为：

<%@ page import=" java.util.*"%> 所以以后写import要规范。项目中可能还有多处这种问题，大部分情况是开发人员写代码太随意的原因。

注：本文转自[http://blog.csdn.net/baozhengw/article/details/42100725](http://blog.csdn.net/baozhengw/article/details/42100725)