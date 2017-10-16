---
layout: post
title: "redis使用管道提升写入的性能"
date: 2017-10-16 16:05:15
categories: redis
excerpt: redis
---

* content
{:toc}


Redis客户端与Redis之间使用TCP协议进行连接，一个客户端可以通过一个socket连接发起多个请求命令。每个请求命令发出后client通常会阻塞并等待redis服务处理，redis处理完后请求命令后会将结果通过响应报文返回给client,因此当执行多条命令的时候都需要等待上一条命令执行完毕才能执行,
如：get 1 get 2 get 3,其执行过程如下图（左）所示：

![redis-pipe](http://hexing-w.github.io/css/pics/pipe.png)



而管道（pipeline）可以一次性发送多条命令并在执行完后一次性将结果返回，pipeline通过减少客户端与redis的通信次数来实现降低往返延时时间，其过程上图（右）所示 

下面是测试代码

		<?php
		
		$redis = new Redis();
		$redis->connect('127.0.0.1');
		$time = microtime(TRUE);
		$redis->pipeline();
		// $redis->multi();
		for($i = 0; $i<100000;$i++){
			$redis->set("aa_{$i}",$i);
			$redis->get("aa_{$i}");
		}
		$redis->exec();
		$now = microtime(TRUE);
		echo $now - $time;
		$redis->flushdb(0);
		echo PHP_EOL;
		
		$time = microtime(TRUE);
		$redis->multi();
		for($i = 0; $i<100000;$i++){
			$redis->set("aa_{$i}",$i);
			$redis->get("aa_{$i}");
		}
		$redis->exec();
		$now = microtime(TRUE);
		echo $now - $time;
		$redis->flushdb(0);
		echo PHP_EOL;
		
		$time = microtime(TRUE);
		for($i = 0; $i<100000;$i++){
			$redis->set("aa_{$i}",$i);
			$redis->get("aa_{$i}");
		}
		$now = microtime(TRUE);
		echo $now - $time;


结果如下图所示：

![redis-time](http://hexing-w.github.io/css/pics/redis-time.png)

