---
title: 关于http接受post请求的错误总结-go版本
comments: true
url: post/error/go/verison
categories:
  - 学习
tags:
  - 问题
  - go
abbrlink: 915cd1e
date: 2018-08-17 23:12:12
---

#### 总结
go对HTTP请求中的form-data类型body会解析到结构体的两个字段中，前一个是用于处理文本的map，后一个是用于处理文件流的地址map。

旧版本中对于content-type为text/plain等文本格式或不设置content-type的字段解析到文本map，将content-type:image/png等文件格式的解析到地址map中1.10.3版本中将有填content-type(现在暂时只验证过text/plain)的字段解析到地址map中，没填content-type解析到文本map中，这样的话对于请求过来的符合标准协议的text/plain字段我就无法在文本map中找到，从而导致一堆的不可访问。

#### 故事背景
在提供一个POST请求接口给一个其他部门的同事之后,该同事使用自己的编写的程序进行提交时发现返回有问题,我打log发现他提交的数据被go解析到了文件map中,于是在大神的帮助下进行抓包研究发现该同事提交的data中的值含有postman中没有提交的content-type:text/plain,遂查了一下标准协议,发现该提交是没有问题的。
百思不得其解。
问题如下:
{% asset_img 1.png 问题 %}

#### 定位过程
1. 使用fiddler模拟请求,在服务器使用tcpdump抓包发现是一样的
{% asset_img 2.png fildder请求 %}
{% asset_img 3.png tcpdump抓包 %}

但依然出现此问题
{% asset_img 1.png 问题 %}
2. 本着go版本不会有问题的思想,使用控制变量法,让程序在windows\linux\docker上build与跑,还是没有找到问题
3. 好的,那说不定是go的问题,于是控制变量法,在linux上使用1.9.7和1.10.3两个go版本上跑了一遍,发现问题通了.....
{% asset_img 5.png 结果 %}
#### 最后
目前仅发现go版本的1.10.3出现,暂时没有验证其他的,但这对于以前正式运行的项目来说是致命的。但也庆幸能够发现这个问题,要不然上线了就G了,感谢大神的帮忙。