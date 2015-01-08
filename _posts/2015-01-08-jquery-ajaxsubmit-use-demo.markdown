---
layout: post
title:  使用jQuery ajaxSubmit
description: 使用jQuery ajaxSubmit
date:   2015-01-08
categories: blog
tags: jQuery ajaxForm ajaxSubmit
---
之前一直使用的jQuery自带的$.ajax/$.get/$.post来执行Ajax异步操作。  
可能是思维惯性一直考虑的是使用jQuery自带的Ajax方法操作。但是很明显这样有一个问题：就是如果后台返回的是一个对象（Object/List)，如果还是使用jQuery自带的Ajax方法处理就需要在后台将对象解析成JSON格式或其他格式。  
这样页面才能处理。  
当然，如果你使用Spring mvc，后台返回Map也是可以的。Spring会帮我们做转化。  
如：
{% highlight java %}
@RequestMapping(value = "/genP1data")
@ResponseBody
public Map<String, String> genP1data(ModelMap model, HttpServletRequest request, HttpServletResponse response) throws UnsupportedEncodingException {
    String shopid = request.getParameter("shopid");
    List values = treportManager.getP1data(Long.valueOf(shopid));
    Map<String, String> result = new HashMap<String, String>();
    if(values.size() == 5){
        result.put("y_order", CommonUtil.formatNumber("0", values.get(0)));
        result.put("y_amount", CommonUtil.formatNumber("0.00", values.get(1)));
        result.put("y_nosend", CommonUtil.formatNumber("0", values.get(2)));
        result.put("y_wait", CommonUtil.formatNumber("0", values.get(3)));
        result.put("y_refund", CommonUtil.formatNumber("0", values.get(4)));
    }
    return result;
}
{% endhighlight %}

那么页面就可以直接这样使用jQuery调用： 
{% highlight javascript %}
$.get("${ctx}/report/genP1data",{shopid:shopid},function(data){
    $("#y_order").text(data.y_order);
    $("#y_amount").text(data.y_amount);
    $("#y_nosend").text(data.y_nosend);
    $("#y_wait").text(data.y_wait);
    $("#y_refund").text(data.y_refund);
});
{% endhighlight %}            

但是如果后台返回的是List或一个JavaBean，使用JQuery就不行了。这个时候要使用jQuery ajaxForm实现表单的异步提交。页面无刷新。    
所以，结合jQuery和jQuery.form就可以实现全部请求Ajax化。  
实例：
{% highlight javascript %}
<form action="${ctx}/report/genP3data" id="queryForm" name="queryForm" method="post"></form>
var optionsPost = {
  success: function(data, status){
    $('#contentDiv').html(data);
  }
};
$("#queryForm").ajaxSubmit(optionsPost);
{% endhighlight %}
后台Controller的代码比如返回一个List。请求处理后跳转到List.jsp页面。  
List.jsp页面的代码就是遍历这个List显示。  
那么上面的Ajax中的data就是List.jsp渲染后的页面内容。

有时候会有这样的需求：一个页面要显示很多东西，当然你进入页面前一次性查出所有需要的数据就不需要用Ajax了。   
比如页面要显示的一些数据可以使用Ajax的方式，另外需要的数据是一个查询结果页面，比如上面的list.jsp。那就可以考虑使用jQuery ajaxform了。   
单纯说jquery.form，可能只会想到页面的表单的异步提交。