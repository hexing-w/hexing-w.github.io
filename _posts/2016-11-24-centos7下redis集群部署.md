---
layout: post
title: "redis-cluster集群部署"
date: 2016-11-24 14:53:23
categories: redis
excerpt: redis
---

* content
{:toc}

### 安装redis-3.2.3
1:wget --no-check-certificate http://download.redis.io/releases/redis-3.2.3.tar.gz

2:tar xzf redis-3.2.3.tar.gz

3:mv redis-3.2.3 usr/local/redis-3.2.3

4:cd usr/loacl/redis-3.2.3

5:make

6:make test 

###创建集群所需要的目录
	mkdir  /home/redis-cluster/{9000,9001,9002,9003,9004,9005}
	cp  ~/redis-3.2.3/redis.conf  /home/redis-cluster/9000
	..
	..
	cp  ~/redis-3.2.3/redis.conf  /home/redis-cluster/9005

###修改配置文件redis.conf
vim  /home/redis-cluster/9000/redis.conf

	daemonize yes
	port 9000  (端口为目录对应名字即每个redis实例所占的端口号)
	cluster-enabled yes
	cluster-config-file nodes.conf
	cluster-node-timeout 5000
	appendonly yes

<font color="red" ><b>注意：拷贝之后的redis.conf要修改对应9001/9002/9003/9004/9005目录下面redis.conf文件中的port参数</b></font>  

###分别启动这6个redis实例

	/redis-3.2.3/src/redis-server /home/redis-cluster/9001/redis.conf
    /redis-3.2.3/src/redis-server /home/redis-cluster/9002/redis.conf
	/redis-3.2.3/src/redis-server /home/redis-cluster/9003/redis.conf
    /redis-3.2.3/src/redis-server /home/redis-cluster/9004/redis.conf
	/redis-3.2.3/src/redis-server /home/redis-cluster/9005/redis.conf
启动之后使用命令查看redis的启动情况ps -ef|grep redis
如下图则说明成功
![redis-cluster](http://hexing-w.github.io/css/pics/redis-cluster.png)

###执行redis的创建集群命令创建集群
/redis-3.2.3/src/redis-trib.rb create --replicas 1 192.168.59.132:9000 192.168.59.132:9001 192.168.59.132:9002 192.168.59.132:9003 192.168.59.132:9004 192.168.59.132:9005

执行上面的命令的时候会报错，因为是执行的ruby的脚本，需要ruby的环境

错误内容：/usr/bin/env: ruby: No such file or directory

所以需要安装ruby的环境，这里推荐使用yum install ruby安装

	yum install ruby

然后再执行创建集群命令，还会报错，提示缺少rubygems组件，使用yum安装

错误内容：

	./redis-trib.rb:24:in `require': no such file to load --rubygems (LoadError)
	from ./redis-trib.rb:24
	yum install rubygems

再次命令，还会报错，提示不能加载redis，是因为缺少redis和ruby的接口，使用gem 安装
错误内容：

	/usr/lib/ruby/site_ruby/1.8/rubygems/custom_require.rb:31:in `gem_original_require': no such file to load
	 -- redis (LoadError)from /usr/lib/ruby/site_ruby/1.8/rubygems/custom_require.rb:31:in `require'from ./redis-trib.rb:25

	gem install redis

再次执行命令，正常执行 如图
![redis-cluster-test](http://hexing-w.github.io/css/pics/redis-cluster-test.png)

好啦 redis集群即搭建成功
![redis-cluster-connect](http://hexing-w.github.io/css/pics/redis-cluster-connect.png)