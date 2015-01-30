---
layout: post
title:  MyEclipse自动提示快捷键失效
description: MyEclipse自动提示快捷键失效
date:   2015-01-30 14:20:39
categories: blog
tags: myeclipse keys
---
按ctrl+shift+L两次，将调出Eclipse设置快捷键的Window。  
输入Content Assist，列出了很多，将Content Assist这个快捷键设置为Alt+/，When选择“In dialogs and Windows”。  
这样设置了之后，你会发现按alt+/还是没有提示。  

Window->Preferences->Java->Editor->Content Assist，将“Enable auto activation”选上。  
然后再Window->Preferences->Java->Editor->Content Assist->Advanced中，可以看到设置了Content Assist快捷键后，还要在这里设置  
Content Assist包含的类型。  如图：
![img](http://uploadingit.com/file/mngpsaxpnhzvyye1/QQ%E6%88%AA%E5%9B%BE20150130142453.png)
这样设置之后就可以使用了。
