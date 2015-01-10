---
layout: post
title:  jQuery学习——入门jQuery选择器之层次选择器
description: jQuery学习——入门jQuery选择器之层次选择器
date:   2015-01-10 16:49:39
categories: blog
tags: jQuery
---
今天说的是层次选择器，那层次选择器有哪几个？  
>$("ancestor descendant")：选取parent元素后所有的child元素  
>$("parent > child")：选取parent元素后所有的直属child元素，何谓“直属”，也就是第一级的意思了  
>$("prev + next")：prev和next是两个同级别的元素. 选中在prev元素后面的next元素  
>$("prev ~ siblings"):选择prev后面的根据siblings过滤的元素。注:siblings是过滤器  

这里要说下，由于最后2个用的比较少，一般会用其他选择器替代，请看下面：  
>$("prev + next")等价于next()  
>$("prev ~ siblings")等价于nextAll()  

具体用法会在后面说到。  
===========================================================================
下面我们来仔细说说这4个层次选择器

**【1】$("ancestor descendant")：选取parent元素后所有的child元素**  
ancestor的中文意思是“祖先”，descendant的中文意思是“后代”，就像css定义层级元素方式一样,只需要不同的元素之间有空格表示，前者父级，后者子级，以此类推，同时我们把它写成这样的形式，$("Element1 Element2 Element3 Element...")。

下面是实例：  
<table>
<tr><td>$("body div")	    </td><td>选取body元素下所有的div元素。                           </td></tr>
<tr><td>$("ul li")	        </td><td>选取ul元素下所有的li元素。                              </td></tr>
<tr><td>$("#test div")	    </td><td>选取id为“test”的元素所包含的所有的div子元素           </td></tr>
<tr><td>$("div#test div")	</td><td>选取id为“test”的div所包含的所有的div子元素            </td></tr>
<tr><td>$(".test div")	    </td><td>选取class为“test”的元素所包含的所有的div子元素        </td></tr>
<tr><td>$("div.test span")	</td><td>选取class为“test”的div所包含的所有的span子元素        </td></tr>
<tr><td>$("span.test .demo")</td><td>选取class为“test”的span所包含的所有的class为demo的元素</td></tr>
<tr><td>$(".test .demo")	</td><td>选取class为“test”的元素所包含的所有的class为demo的元素</td></tr>
</table>


**【2】$("parent > child")：选取parent元素后所有的直属child元素，何谓“直属”，也就是第一级的意思了**  
<table>
<tr><td>$("body > div")	       </td><td>选取body元素下所有的第一级div元素。                             </td></tr>
<tr><td>$("ul > li")	       </td><td>选取ul元素下所有的第一级li元素。                                </td></tr>
<tr><td>$("#test > div")	   </td><td>选取id为“test”的元素所包含的所有的第一级div子元素             </td></tr>
<tr><td>$("div#test > div")	   </td><td>选取id为“test”的div所包含的所有的第一级div子元素              </td></tr>
<tr><td>$(".test > div")	   </td><td>选取class为“test”的元素所包含的所有的第一级div子元素          </td></tr>
<tr><td>$("div.test > span")	/td><td><选取class为“test”的div所包含的所有的第一级span子元素         </td></tr>
<tr><td>$("span.test > .demo")	/td><td><选取class为“test”的span所包含的所有的第一级class为demo的元素 </td></tr>
<tr><td>$(".test > .demo")	   </td><td>选取class为“test”的元素所包含的所有的第一级class为demo的元素  </td></tr>
</table>

**【3】$("prev + next")：prev和next是两个同级别的元素. 选中在prev元素后面的next元素.**  
（1）$("div + p")表示选择紧跟在 div 元素后的 p 一个元素  
（2）$("#demo+img")选择id为demo元素后面的img对象.如果id为demo元素后面没有同级的img对象，那么这个$("#demo+img").length=0  

**【4】$("prev ~ siblings"):选择prev后面的根据siblings过滤的元素。注:siblings是过滤器。  **
（1）$("div ~ p")表示匹配跟在 div 元素后的所有 p 元素  
（2）$("#demo~[title]")选择id为demo的元素后面所有带有title属性的元素，同样如果id为demo的元素后面没有一个带有title属性的元素，那么$("#demo~[title]").length = 0

 

===========================================================================

 

这里说的是最后2个选择器的等价关系

$(".one + div")等价于$(".one").next("div")

$("#prev ~ div")等价于$(".prev").nextAll("div")

 

===========================================================================

这里是实例，参考w3cfuns网站的
{% highlight html %}
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>实例</title>
<script language="javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js"></script>
<script type="text/javascript"> 
$(function(){      
    $("#bt1").click(function(){
        $("div > p").text("我们都是div中的儿子，我们的名字叫做p还记得教程中的$(\"父亲 > 儿子 > ...\")这种关系吗？就是以这种方式选择的我$(\"div > p\")");
    })
    $("#bt2").click(function(){
        $("div + p").text("我是紧跟着div的p既然紧跟着，当然就得使用“+”紧密的连在一起嘛。以这种方式选择的我$(\"div + p\")");
    })
    $("#bt3").click(function(){
        $("div ~ p").text("我们是div的跟随者，我们的名字叫做p要以这种方式选择的我$(\"div ~ p\")");
    })
})
</script>
</head>
 
<body>
<input id="bt1" type="button" value="获取div下所有的p标签"/>
<input id="bt2" type="button" value="匹配紧跟在 div 元素后的 p 一个元素"/>
<input id="bt3" type="button" value="匹配跟在 div 元素后的所有 p 元素"/>
<div>
    <p>1</p>
    <p>2</p>
    <p>3</p>
    <p>4</p>
</div>
<p>5</p>
<p>6</p>
<p>7</p>
<p>8</p>
</body>
</html>
{% endhighlight %} 

========================================================================

ps.全文参考梦三秋和w3cfuns网站

注：本文转自[http://www.cnblogs.com/huige728/archive/2012/07/28/2613162.html](http://www.cnblogs.com/huige728/archive/2012/07/28/2613162.html)