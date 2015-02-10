---
layout: post
title:  MYSQL实现查询某个时间段内每一天的数据
description: MYSQL实现查询某个时间段内每一天的数据
date:   2015-02-10 20:11:39
categories: blog
tags: mysql 
---
比如查询一周内的访问量，页面显示。如图：  
![title](http://uploadingit.com/file/znhhmlymiqb3mhxy/QQ%E6%88%AA%E5%9B%BE20150210095854.png)

好像Mysql没有提供什么函数可以实现这种功能。目前的做法也只能是根据选择的时间段来生成查询参数。一次查询一天的数据。  
我的查询方法如下：  
<pre>
public Page getData(TglsFamilyPicStaticsQuery query) {
    // 得到查询次数
    int queryCount = 0;
    Date begin = query.getStart();
    Date end = query.getEnd();
    
    // 页面没有选择开始时间，那么需要从数据库查询第1条记录的时间
    if (CommonUtil.isNull(begin)) {
        begin = tglsFamilyPicActivityDao.getMinDateFromActivityPv();
        if (CommonUtil.isNull(begin)) {
            begin = new Date();
        }
        else {
            Calendar c = Calendar.getInstance();
            c.setTime(begin);
            c.set(Calendar.HOUR_OF_DAY, 0);
            c.set(Calendar.MINUTE, 0);
            c.set(Calendar.SECOND, 0);
            begin = c.getTime();
        }
    }
    // 页面没有选择结束时间，那么结束时间等于今天
    if (CommonUtil.isNull(end)) {
        end = new Date();
    }
    
    // 生成查询条件
    queryCount = CommonUtil.compareDay(begin, end);
    
    // 计算查询的起止行
    int queryStart = query.getPageNumber() > 0 ? (query.getPageNumber() - 1) * query.getPageNumber() : 0;
    int queryEnd = queryStart + query.getPageSize() -1;
    
    // 日间查询段
    List<Map<String,Object>> recordsList = new ArrayList<Map<String,Object>>();
    Date tmpBeginTime = (Date) begin.clone();
    for (int i=0;i<queryCount;i++) {
        if (isIn(i,queryStart,queryEnd)) {
            Map<String, Object> dateQuery = new HashMap<String, Object>();
            // 创建日历对象
            Calendar c = Calendar.getInstance();
            c.setTime(tmpBeginTime);
            // 开始时间
            c.set(c.get(Calendar.YEAR), c.get(Calendar.MONTH), c.get(Calendar.DATE), 0, 0, 0);
            dateQuery.put("beginTime", c.getTime()); // 开始时间
            // 结束时间
            c.set(c.get(Calendar.YEAR), c.get(Calendar.MONTH), c.get(Calendar.DATE), 23, 59, 59);
            dateQuery.put("endTime", c.getTime()); // 结束时间
            
            // 查询某一天的数据
            Map<String,Object> record = tglsFamilyPicActivityDao.findOneDayRecord(dateQuery);
            record.put("queryDate",c.getTime());
            recordsList.add(record);
            
            // 天数加1
            tmpBeginTime = CommonUtil.addDate(tmpBeginTime, 1);
        }
    }
    
    Page<Map<String, Object>> page = new Page<Map<String, Object>>(query.getPageNumber(), query.getPageSize(), queryCount);
    page.setResult(recordsList);
    return page;
}

/**
 * 判断v是否在min和max之间
 *
 * @author qincd
 * @email qincd@hyxt.com
 * @date Feb 9, 2015 11:51:26 AM
 */
private boolean isIn(int v,int min,int max) {
    for (int i=min;i<=max;i++) {
        if (v == i) return true;
    }
    return false;
}
</pre>    

那么， 实际上应该看的出，计算时间段内的天数，循环生成1天的查询条件（beginTime,endTime）。然后查询这1天的数据。  
这段代码还包括分页查询，首先计算出要查询时间的起止位置，那么这个起止位置肯定应该是在时间段内的。  
比如，pagesize为10，pagenumber=1，那么查询起止位置是0-9。如果查询时间段为2015.01.01 ~ 2015.01.05，那么实际上是有5天时间的间隔。  
因为最终是每天1条数据，所以总共有5条数据，如此最终会查出5条数据。  

记录这个主要是为以后遇到同样的问题提供参考。