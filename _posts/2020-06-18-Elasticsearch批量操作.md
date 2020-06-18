---
layout: post
title: "elasticsearch bulk批量操作"
date: 2020-06-18 10:17:03
categories: Elasticsearch
excerpt: Elasticsearch
---

* content
{:toc}

### 批量操作bulk

1、bulk相当于数据库里的bash操作。

2、引入批量操作bulk，提高工作效率，你想啊，一批一批添加与一条一条添加，谁快?

3、bulk API可以帮助我们同时执行多个请求

4、bulk的格式：

bulk请求模板分成action、metadata和doc三部份

action : 必须是以下4种选项之一

　　index(最常用) : 如果文档不存在就创建他，如果文档存在就更新他

　　create : 如果文档不存在就创建他，但如果文档存在就返回错误

　　使用时一定要在metadata设置_id值，他才能去判断这个文档是否存在

　　update : 更新一个文档，如果文档不存在就返回错误

　　使用时也要给_id值，且后面文档的格式和其他人不一样

　　delete : 删除一个文档，如果要删除的文档id不存在，就返回错误

使用时也必须在metadata中设置文档_id，且后面不能带一个doc，因为没意义，他是用_id去删除文档的

metadata : 设置这个文档的metadata，像是_id、_index、_type...

doc : 就是一般的文档格式

5、、bulk一次最大处理多少数据量？

　　bulk会把将要处理的数据载入内存中，所以数据量是有限制的，最佳的数据量不是一个确定的数值，它取决于你的硬件，你的文档大小以及复杂性，你的索引以及搜索的负载。

　　一般建议是1000-5000个文档，如果你的文档很大，可以适当减少队列，大小建议是5-15MB，默认不能超过100M，可以在es的配置文件（即$ES_HOME下的config下的elasticsearch.yml）中。

	bulk 如果一次性数据过多，容易导致超时

			elasticsearch.exceptions.ConnectionTimeout: ConnectionTimeout caused by - ReadTimeoutError(HTTPConnectionPool(host='192.168.0.92', port='9200'): Read timed out. (read timeout=10))


### 批量操作bulk例子




