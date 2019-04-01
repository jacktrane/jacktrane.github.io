---
title: 简单聊一下表Select count
comments: true
url: database/select/count
categories:
  - 学习
tags:
  - 数据库
abbrlink: 5a45e1fe
date: 2019-04-02 00:32:19
---

#### 数据如图: 
{% asset_img 1.png 元图 %}

#### 按照*方式查找 :
`SELECT count(*) FROM test`

结果如下: 
{% asset_img 2.png count(*) %}

#### 按照指定字段查询 : 
`SELECT count(name) FROM test`


结果如下: 
{% asset_img 3.png count(name) %}

#### 是按照虚拟字段查询:
SELECT count(1) FROM test


结果如下: 
{% asset_img 4.png count(1) %}

#### 为什么会造成以上的差异呢?
原因是: 
count(*)其实是按每个字段读一次，对比每个字段的count，最终得出最大值6
{% asset_img 5.png count(*) %}
count(name)则是仅读name字段下的值，此刻name中是只有4个值的，其中NULL不占空间
{% asset_img 6.png count(name) %}
count(1)很有趣，实际上它是虚拟了一个字段，并以此此填充相应记录中的虚拟值，从而得出count(1)为6
{% asset_img 7.png count(1) %}

#### 总结：
对于查单个字段总量，可以使用count(字段)
但很多时候我们查询一般都是查总的记录量的，在数据量少的条件下count(1)和count(*)差异性并不高，但如果数据量达到一定量级可以使用count(1)
