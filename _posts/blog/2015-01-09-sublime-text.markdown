---
layout: post
title:  sublime text使用
description: 介绍使用sublime text提高工作效率
date:   2015-01-09 16:49:39
categories: blog
tags: sublime tools emmet zen-code
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
emmet插件，原名Zen-code，使用说明：[http://www.cnblogs.com/cobby/archive/2012/05/09/2492473.html](http://www.cnblogs.com/cobby/archive/2012/05/09/2492473.html)  
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

有时我们需要对一片区域的所有行进行同时编辑， Ctrl + Shift + L 可以将当前选中区域打散，然后进行同时编辑：
![img](http://img0.tuicool.com/JJVRF3.gif)

有打散自然就有合并， Ctrl + J 可以把当前选中区域合并为一行：
![img](http://img1.tuicool.com/fQVrei.gif)

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

##14.快速查找&替换
多数情况下，我们需要查找文中某个关键字出现的其它位置，这时并不需要重新将该关键字重新输入一遍然后搜索，我们只需要使用 Shift + ←/→ 或 Ctrl + D 选中关键字，然后 F3 跳到其下一个出现位置， Shift + F3 跳到其上一个出现位置，此外还可以用 Alt + F3 选中其出现的所有位置（之后可以进行多重编辑，也就是快速替换）。
![img](http://img2.tuicool.com/ANRj2qF.gif)

##15.标准查找&替换

另一种常见的使用场景是搜索某个已知但不在当前显示区域的关键字，这时可以使用 Ctrl + F 调出搜索框进行搜索：
![img](http://img0.tuicool.com/EnENBvm.jpg)
以及使用 Ctrl + H 进行替换：
![img](http://img1.tuicool.com/qY3iau.jpg)
关键字查找&替换

对于普通用户来说，常规的关键字搜索就可以满足其需求：在搜索框输入关键字后 Enter 跳至关键字当前光标的下一个位置， Shift + Enter 跳至上一个位置， Alt + Enter 选中其出现的所有位置（同样的，接下来可以进行快速替换）。

Sublime Text的查找有不同的模式： Alt + C 切换大小写敏感（Case-sensitive）模式， Alt + W 切换整字匹配（Whole matching）模式，除此之外Sublime Text还支持在选中范围内搜索（Search in selection），这个功能没有对应的快捷键，但可以通过以下配置项自动开启。
<pre>
	"auto_find_in_selection": true
</pre>
这样之后在选中文本的状态下范围内搜索就会自动开启，配合这个功能，局部重命名（Local Renaming）变的非常方便：
![img](http://img2.tuicool.com/UFNVBz.gif)

使用 Ctrl + H 进行标准替换，输入替换内容后，使用 Ctrl + Shift + H 替换当前关键字， Ctrl + Alt + H 替换所有匹配关键字。

##16.正则表达式查找&替换

正则表达式 是非常强大的文本查找&替换工具，Sublime Text中使用 Alt + R 切换正则匹配模式的开启/关闭。Sublime Text的使用 Boost里的Perl正则表达式风格 。

出于篇幅原因，本文不会对正则表达式进行详细介绍， Mastering Regex （中译本： 精通正则表达式 ）对正则表达式的原理和各语言下的使用进行了详细介绍。此外网上有大量正则表达式的优秀教程（ “正则表达式30分钟入门教程” 和 MSDN正则表达式教程 .aspx)），以及在线测试工具（ regexpal 和 regexer ）。

##17.多文件搜索&替换

使用 Ctrl + Shift + F 开启多文件搜索&替换（注意此快捷键和搜狗输入法的简繁切换快捷键有冲突）：
![img](http://img0.tuicool.com/A3uy2uy.jpg)
多文件搜索&替换默认在当前打开的文件和文件夹进行搜索/替换，我们也可以指定文件/文件夹进行搜索/替换。

##18.跳转（Jumping）

Sublime Text提供了强大的跳转功能使得我们可以在不同的文件/方法/函数中无缝切换。就我的使用经验而言，目前还没有哪一款编辑器可以在这个方面超越Sublime Text。

###18.1跳转到文件

Ctrl + P 会列出当前打开的文件（或者是当前文件夹的文件），输入文件名然后 Enter 跳转至该文件。

需要注意的是，Sublime Text使用模糊字符串匹配（Fuzzy String Matching），这也就意味着你可以通过文件名的前缀、首字母或是某部分进行匹配：例如， EIS 、 Eclip 和 Stupid 都可以匹配 EclipseIsStupid.java 。
![img](http://img1.tuicool.com/ZV3aE3v.gif)

###18.2跳转到符号

尽管是一个文本编辑器，Sublime Text能够对代码符号进行一定程度的索引。 Ctrl + R 会列出当前文件中的符号（例如类名和函数名，但无法深入到变量名），输入符号名称 Enter 即可以跳转到该处。此外，还可以使用 F12 快速跳转到当前光标所在符号的定义处（Jump to Definition）。
![img](http://img2.tuicool.com/AZzueq.gif)
比较有意思的是，对于Markdown， Ctrl + R 会列出其大纲，非常实用。
![img](http://img0.tuicool.com/YVRfua.jpg)

###18.3跳转到某行

Ctrl + G 然后输入行号以跳转到指定行：
![img](http://img1.tuicool.com/eI3IBb.gif)

###18.4组合跳转

在 Ctrl + P 匹配到文件后，我们可以进行后续输入以跳转到更精确的位置：

>@ 符号跳转：输入 @symbol 跳转到 symbol 符号所在的位置  
># 关键字跳转：输入 #keyword 跳转到 keyword 所在的位置  
>: 行号跳转：输入 :12 跳转到文件的第12行。  

![img](http://img2.tuicool.com/nU3e6z.gif)
所以Sublime Text把 Ctrl + P 称之为 “Go To Anything” ，这个功能如此好用，以至于我认为没有其它编辑器能够超越它。

##19.文件夹（Folders）

Sublime Text支持以文件夹做为单位进行编辑，这在编辑一个文件夹下的代码时尤其有用。在 File 下 Open Folder ：
![img](http://img2.tuicool.com/AzInym.jpg)
你会发现右边多了一个侧栏，这个侧栏列出了当前打开的文件和文件夹的文件，使用 Ctrl + K, Ctrl + B 显示或隐藏侧栏，使用 Ctrl + P 快速跳转到文件夹里的文件。

编辑代码时我们经常会开多个窗口，所以分屏很重要。 Alt + Shift + 2 进行左右分屏， Alt + Shift + 8 进行上下分屏， Alt + Shift + 5 进行上下左右分屏（即分为四屏）。
![img](http://img0.tuicool.com/MFnEz2.gif)

分屏之后，使用 Ctrl + 数字键 跳转到指定屏，使用 Ctrl + Shift + 数字键 将当前屏移动到指定屏。例如， Ctrl + 1 会跳转到1屏，而 Ctrl + Shift + 2 会将当前屏移动到2屏。

##20.全屏（Full Screen）

Sublime Text有两种全屏模式：普通全屏和无干扰全屏。

个人强烈建议在开启全屏前关闭菜单栏（Toggle Menu），否则全屏效果会大打折扣。
F11 切换普通全屏：
![img](http://img1.tuicool.com/YfaQVrN.jpg)
Shift + F11 切换无干扰全屏：
![img](http://img2.tuicool.com/uAVbMb.jpg)

##21.自动完成（Auto Completion）
Sublime Text 支持一定的自动完成，按 Tab 自动补全。
![img](http://img2.tuicool.com/MveQ3e.jpg)

##22.括号（Brackets）

编写代码时会碰到大量的括号，利用 Ctrl + M 可以快速的在起始括号和结尾括号间切换， Ctrl + Shift + M 则可以快速选择括号间的内容，对于缩进型语言（例如Python）则可以使用 Ctrl + Shift + J 。
![img](http://img0.tuicool.com/ZRnEBj7.gif)

此外，我使用 BracketHighlighter 插件以高亮显示配对括号以及当前光标所在区域，效果如下
![img](http://img1.tuicool.com/Qf22ai6.gif)

##23.命令行（Command Line）
尽管提供了Python控制台，但Sublime Text的控制台仅支持单行输入，十分不方便，所以我使用 Sublime REPL 以进行一些编码实验（Experiments）。
![img](http://img2.tuicool.com/eQZB7zR.gif)

##24.sublime text2 中标签高亮效果BracketHighlighter插件
1、打开package Control，选择install Package
![img](http://segmentfault.com/img/bVbZ4t)

2.输入BracketHighlighter，回车

3.这样该插件会自动安装，安装后所有的提示高亮都是白色或没有提示。按 preferences-->package settings-->Bracket highlighter-->Bracket settings-Default会打开一个文件

4.将"bracket_styles"中的style改为hightlight

5.将"bracket_styles"以下的样式类型去掉注释
![img](http://segmentfault.com/img/bVbZ4u)

6.在color scheme查看你所用的配色类型，我用的是Monokai
![img](http://segmentfault.com/img/bVbZ4v)

7.在Color Scheme - Default文件中找到你所使用的配色文件（我的是Monokai.tmTheme），打开     
8.添加以下代码。
{% highlight html %}
<dict>
        <key>name</key>
        <string>Bracket Curly</string>
        <key>scope</key>
		<string>brackethighlighter.curly</string>
        <key>settings</key>
        <dict>
            <key>foreground</key>
            <string>#A6E22E</string>
        </dict>
</dict>
{% endhighlight %}
通过添加这样的代码块并修改颜色和样式名称，获得自己喜欢的高亮提示。

##25.SublimeCodeIntel插件
SublimeCodeIntel插件实现的功能：  
1.代码提示；可以提示HTML的属性，CSS样式等。输入一个字符，自动提示；  
![代码自动提示](http://uploadingit.com/file/jojrq5a64y9ykspn/sublime_text_code_intel_1.png)
2.跳转到变量定义的地方。选中变量，按ctrl+f3.  


注：本文部分内容转载：  
[http://www.tuicool.com/articles/AJR7Rn3](http://www.tuicool.com/articles/AJR7Rn3)  
[http://segmentfault.com/blog/taotao123/1190000000437871](http://segmentfault.com/blog/taotao123/1190000000437871)