---
layout: post
title:  "php 在windows 下安装mongodb"
date:   2015-09-17 13:41:54
categories: php
excerpt: mongodb
---

* content
{:toc}

### 安装mongodb

1.根据自己电脑配置，下载[mongodb](http://www.mongodb.org/downloads)

2.将下载好的目录解压缩到安装目录，比如d:mongodb

3.建立d:\mongodb\db和d:\mongodb\log文件夹

4.运行cmd进入dos 命令，执行以下命令
<pre><code>
　cd d:\mongodb\bin

　d:\mongodb\bin>mongod --dbpath "d:\mongodb\db" --logpath "d:\mongodb\log" --install 
--serviceName "mongodb"

</code></pre>
<font color="red">	  【attention】:一定要以管理员身份运行，不然，恭喜你进坑了</font>


5.测试连接
	输入以下命令,还是在bin目录下执行
<pre><code>
 net start  mongodb
</code></pre>
如果出现以下以下界面，就代表我们已经装好了mongo服务

![mongo](http://hexing-w.github.io/css/pics/mon.png) 


如果出现以下语句报错
<pre><code>
2015-09-17T12:09:04.576+0800 warning: Failed to connect to 127.0.0.1:27017, reas
on: errno:10061 由于目标计算机积极拒绝，无法连接。
2015-09-17T12:09:04.578+0800 Error: couldn't connect to server 127.0.0.1:27017 (
127.0.0.1), connection attempt failed at src/mongo/shell/mongo.js:146
exception: connect failed
</code></pre>
<font color="red">【attention：】需要把db文件夹下的mongod.lock 文件删除掉</font>

ok~~~~接下来就是在php里面安装mongo扩展了



### php 安装mongo 扩展


Github上已经提供了用于window平台的预编译php [mongodb驱动二进制包](https://s3.amazonaws.com/drivers.mongodb.org/php/index.html)，你可以下载与你php对应的版本，但是你需要注意以下几点问题：

VC6 是运行于 Apache 服务器
'Thread safe'（线程安全）是运行在Apache上以模块的PHP上，如果你以CGI的模式运行PHP，请选择非线程安全模式（' non-thread safe'）。


VC9是运行于 IIS 服务器上。
下载完你需要的二进制包后，解压压缩包，将'php_mongo.dll'文件添加到你的PHP扩展目录中（ext）。ext目录通常在PHP安装目录下的ext目录。


<pre><code>
 extension=php_mongo.dll
</code>
</pre>

重启服务器

通过浏览器访问phpinfo，如果安装成功，就会看到类型以下的信息：
![mongo](http://hexing-w.github.io/css/pics/mongo.png) 

OK ~~~nice,赶紧试试吧




