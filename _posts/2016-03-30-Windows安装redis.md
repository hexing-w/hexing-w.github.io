---
layout: post
title: "Windows下安装redis"
date: 2016-03-30 23:31:23
categories: php
excerpt: php
---

* content
{:toc}

### redis简介

redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hashs（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

Redis 是一个高性能的key-value数据库。 redis的出现，很大程度补偿了memcached这类key/value存储的不足，在部分场合可以对关系数据库起到很好的补充作用。它提供了Python，Ruby，Erlang，PHP客户端，使用很方便。

### Windows下安装redis

下载地址[redis](https://github.com/dmajkic/redis/downloads)下载到的Redis支持32bit和64bit。根据自己实际情况选择，我选择64bit。把64bit文件内容拷贝到需要安装的目录下,比如：D:\redis\redis-2.4.5。

打开一个cmd窗口，使用cd命令切换到指定目录（D:\redis\redis-2.4.5）运行 redis-server.exe redis.conf 。运行以后出现如下界面![redis](http://hexing-w.github.io/css/pics/redis.jpg)

这就说明Redis服务端已经安装成功。

重新打开一个cmd窗口，使用cd命令切换到指定目录（D:\redis\redis-2.4.5）运行 redis-cli.exe -h 127.0.0.1 -p 6379，其中 127.0.0.1是本地ip，6379是redis服务端的默认端口。运行成功如下图所示。
这样，Redis windows环境下搭建已经完成

![redis_server](http://hexing-w.github.io/css/pics/redis_server.jpg)

这样，Redis windows环境下搭建已经完成

![redis_start](http://hexing-w.github.io/css/pics/redis_start.jpg)

正确输出 hell word，测试成功！

### php中的使用

下载 dll文件

[php_redis.dll](http://download.csdn.net/download/bluesky321/5355093)
php_redis.dll 扩展 For PHP5.5.x

测试平台：Windows7 (FastCGI PHP5.5.12 Apache 2.4.9)

包含 Non Thread Safe 和 Thread Safe 两个版本

首先把php_redis.dll 放入php 的ext 文件夹，然后在php.ini配置文件中加入如下代码：

extension=php_redis.dll

重启web服务器

### php中的使用

代码如下
<pre>  
    < ?php
    $redis = new Redis();
    $redis->connect('127.0.0.1',6379);
    $redis->set('test','hello redis');
    echo $redis->get('test');
    ?>
</pre>

输出 hello  redis 成功!

安装时遇到的坑：

安装的时候，因为版本号不对，给自己挖了坑，结果是有redis扩展，but...phpinfo打印的时候并没有输出redis的信息，于是再根据phpinfo里面对应的Thread Safety 和php版本号重新下载。

别说话，让我哭会儿....宝宝心里苦，但宝宝还是说了。

[redis](http://www.cnblogs.com/ikodota/archive/2012/03/05/php_redis_cn.html)的一些用法

原文出处： http://www.cnblogs.com/yun007/p/3705353.html




