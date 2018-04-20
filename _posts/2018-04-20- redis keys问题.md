---
layout: post
title: "redis keys命令的使用及性能"
date: 2018-04-20 16:14:13
categories: redis 
excerpt: redis
---

* content
{:toc}

###使用

Redis KEYS命令用于搜索具有匹配模式的键。

返回值

具有匹配模式的键列表(Array)。

语法
以下是Redis KEYS命令的基本语法。

	redis 127.0.0.1:6379> KEYS PATTERN


示例

首先，在Redis中创建几个键：mykey1，mykey2和mykey3，并向这些键设置一些值。

	redis 127.0.0.1:6379> SET mykey1 "redis"
	OK 
	redis 127.0.0.1:6379> SET mykey2 "mysql"
	OK 
	redis 127.0.0.1:6379> SET mykey3 "mongodb"
	OK
	Shell

现在，使用”mykey”字符模式开始搜索相关键。

	127.0.0.1:6379> KEYS mykey*
	1) "mykey2"
	2) "mykey1"
	3) "mykey3"

###性能

Keys模糊匹配，请大家在实际运用的时候忽略掉。因为Keys会引发Redis锁，并且增加Redis的CPU占用，情况是很恶劣的

实际应用中有时候会出现需要遍历redis中的所有键值的需求，比如清理没用的键等等。但是keys这个命令性能真的很差，redis官方文档是这么说的：

	Warning: consider KEYS as a command that should only be used in production environments with extreme care. It may ruin performance when it is executed against large databases. This command is intended for debugging and special operations, such as changing your keyspace layout. Don’t use KEYS in your regular application code. If you’re looking for a way to find keys in a subset of your keyspace, consider using SCAN or sets.

由于执行keys命令，redis会锁定，如果数据庞大的话可能需要几秒或更长，对于生产服务器上锁定几秒这绝对是灾难了


