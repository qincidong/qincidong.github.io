---
layout: post
title:  sublime text使用
description: 介绍使用sublime text提高工作效率
date:   2015-01-09 16:49:39
categories: blog
tags: sublime tools
---
##1.下载Sublime text
请自行百度下载，稳定版是Sublime text2。不过Sublime text3有更多高级功能。

##2.安装Package Control
使用Sublime text，你可以安装很多插件来提高工作效率。安装插件需要先安装Package Control。  
下面介绍如何安装Package Control。  
安装方法有2种：

（1）访问Package Control站点按照提示复制一段python命令安装Package Control
也可以直接复制这一段到控制台（Ctrl+~）
{% highlight python %}
import urllib2,os; pf='Package Control.sublime-package'; ipp=sublime.installed_packages_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install_opener(urllib2.build_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read()); print 'Please restart Sublime Text to finish installation'  
{% endhighlight %}

注意：执行上面一段代码的前提是你安装了Python。

（2）下载Package Control插件包到插件目录  
插件目录在菜单中打开Preference--Browse Packages，没有自己新建一个  

##3.安装插件
Ctrl+Shift+P(Win) 或 Command+Shift+P(Mac) 调出控制面板，  
常用的命令Install Package (安装扩展)、List Packages (列出全部扩展)、Remove Package (移除扩展)、Upgrade Package (升级扩展)。

下面以安装emmet插件为例：
Emmet 项目的前身是前端开发人员熟知的 Zen Coding（快速编写 HTML/CSS 代码的方案）。在 Sublime Text 编辑器中搭配 Emmet 插件真的是让你编码快上加快。  
在安装Package Control后，按快捷键ctrl+shift+p调出命令面板，输入install package回车。  等待弹出窗口，在弹出窗口中输入emmet。  
找到emmet，回车等待安装。安装后需要重启Sublime Text。  

##4.Emmet插件的使用
1）快速生成HTML5文件
a)第一种方式是输入html:5，然后按tab键/ctrl+e。  
b)第二种方式是输入一个!，然后按tab键/ctrl+e。  
生成的代码如下：
{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	
</body>
</html>
{% endhighlight %}

2）快速生成ul,li代码
比如快速生成一个ul，下面有10个li，每个li拥有class "item序号"  
在编辑器中输入：
{% highlight html %}
ul>.item$*10
{% endhighlight %}

然后按ctrl+e。生成的代码如下：
{% highlight html %}
<ul>
	<li class="item1"></li>
	<li class="item2"></li>
	<li class="item3"></li>
	<li class="item4"></li>
	<li class="item5"></li>
	<li class="item6"></li>
	<li class="item7"></li>
	<li class="item8"></li>
	<li class="item9"></li>
	<li class="item10"></li>
</ul>
{% endhighlight %}

生成其他的代码也一样，比如生成下面的代码：
{% highlight html %}
<div class="container">
	<div class="inner"></div>
</div>
{% endhighlight %}

输入div.container>div.inner，然后ctrl+e就行了。

再比如生成下面的3行5列的表格：
{% highlight html %}
<table id="mytable">
	<tr class="line">
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr class="line">
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
	<tr class="line">
		<td></td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>
</table>
{% endhighlight %}

可以输入table#mytable>(tr.line>td*5)*3，然后ctrl+e。


##5.多行编辑模式
UE、Notepad++都有多行编辑的模式，在sublime text中也不例外。  
有3种方式：  
1）选中一部分字符，然后按ctrl+d，每按一次就会匹配一个相同的内容，不断的ctrl+d，这样所有匹配的都会被选择，并且后面有一个光标可以输入。  
如果想略过某个不选择，可以按ctrl+k。  
2）选中一部分字符，按alt+f3，这样相同的都会选中并出现一个光标可以输入。  
3）按住shift，然后按鼠标右键，然后向下拉。  

##6.复制代码时格式化
从一个文件复制一段代码到另一个文件中，输入直接ctrl+v，则代码时乱的。如果想让代码格式化，可以按ctrl+shift+v。

##7.快速定位到某一行
按ctrl+p，然后输入:你想跳转到的行数，然后回车。比如跳转到100行，输入:100，回车。

##8.快速定位函数，或CSS中的选择器
按ctrl+p，输入@，这样就会列出所有的函数或选择器，然后继续输入你想跳转到的函数或选择器，回车就可以跳转过去。  
而且，在下拉框中选择某个函数或选择器后，编辑器会显示相应的代码，便于你定位。  

##9.行向上或向下移动
跟eclipse中的altl+Up/Down一样，可以使用行向上或向下移动。  
选择要移动的行，按ctrl+shift+Up/Down。如果没有选择就是移动当前行。

##10.删除行
选择要删除的行，按ctrl+x。如果没有选择就是删除当前行。

##11.快速查找某个文件
按ctrl+p，输入文件名，sublime text有模糊匹配，不用输入完整。如果相同文件名的文件有很多，但你知道你要找的文件的某个路径，你输入路径就可以了。

##12.安装和git插件
1）安装git插件
ctrl+shift+p调出命令面板，输入install，在弹出的窗口中输入git，选择git，回车进行安装。  
这个时候Sublime Text只是安装了git插件，但还不能使用git命令，需要在修改Sublimt Text针对git的配置文件  
在git->user settings中增加：
{% highlight json %}
{
	"git_command":"D:/Git/cmd/git.exe"
}
{% endhighlight %}
指向的是你的git程序中的git.exe文件。注意：git的安装路径不能有空格，否则git命令显示乱码。  

2）使用Git插件  
到这里位置，所有的本地环境的配置已经完成。接下来讲讲具体怎么使用Sublime Text 中的git插件连接GitHub。  
首先在Sublime text 打开想要使用git同步的项目目录  
然后使用“Ctrl+Shift+p”打开命令窗口，输入“Git:init”来初始化git化境。 ST2会让你选择需要初始化的Git目录，选择到你的工程目录即可。  
之后就可以正常的使用git命令了。  
使用Git:status来查看当前的状态。  
输入命令后，在弹出窗口中会显示出本地的项目更改状态。  
选择更改的文件，可以直接看到更改的地方，git的diff功能：  
接下来是用git:add命令添加新增加的文件。  
选择“including untracked files”，将新增加的README.md添加到更改当中。

使用git:commit，来提交更改。Sublime Text会自动跳出一个文本文件，你可以在文件的最上方输入这次更改的comments，然后直接关闭这个文件，就会出  发commit操作。并且将你输入的comments座位-m的参数。 这个是非常方便的，比用命令行运行commit 用-  m参数添加评论的方式要方便很多，而且可以随便修改。ctrl+w关闭文件的同时，commit操作自动触发。  

接下来的步骤应该是添加远程仓库(remote repository)，而在Sublime Text中我始终没有找到这条命令，于是只能用命令行去执行了。  

##13.Sublime text2.0.2注册码
直接输入注册码就可以了  
<pre>
Andrew Weber

Single User License

EA7E-855605

813A03DD 5E4AD9E6 6C0EEB94 BC99798F

942194A6 02396E98 E62C9979 4BB979FE

91424C9D A45400BF F6747D88 2FB88078

90F5CC94 1CDC92DC 8457107A F151657B

1D22E383 A997F016 42397640 33F41CFC

E1D0AE85 A0BBD039 0E9C8D55 E1B89D5D

5CDB7036 E56DE1C0 EFCC0840 650CD3A6

B98FC99C 8FAC73EE D2B95564 DF450523
</pre>

