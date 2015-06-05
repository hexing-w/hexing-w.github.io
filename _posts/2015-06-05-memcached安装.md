---
layout: post
title:  "memcached windows 安装"
date:   2015-06-05 19:14:54
categories: php
excerpt: memcache 和memcached 的区别

---

* content
{:toc}


### memcache 和memcached 的区别

在自己的新程序中打算全面应用memcached技术，这个很容易理解这是memcached是内存缓存，但是怎么还有memcache呢？ 

其实很简单，memcache是php的一个扩展，用于php管理memcached，php-memcache.dll。
 
如果安装了memcached不安装扩展，那么php无法操控memcached，但是命令行使用起来没有问题 

如果安装了memcache扩展但是没有安装memcached服务，那么这个就无法使用 

只有同时安装了memcached服务和memcache扩展才可以在PHP中使用memcached提高动态网站性能


###  安装memcache的php扩展
1. php_memcache.dll 下载：

下载地址：php_memcache.zip                                                   http://www.2cto.com/uploadfile/2012/0713/20120713110240777.zip

2. 把php_memcache.dll放到php的ext目录:
 
   例如：D:\WampServer\bin\php\php5.3.11\ext\php_memcache.dll

3. 打开 php.ini 文件:
我的php.ini的位置：D:\WampServer\bin\php\php5.3.11\php.ini

4. 在 php.ini上增加一行:
extension=php_memcache.dll
 
5. 重启Wampserver的apache服务

### 安装memcached

 
1. Memcached-win64 下载：
 
a. 下载最新版：http://blog.couchbase.com/memcached-windows-64-bit-pre-release-available
 
b. 直接下载： memcached-win64-1.4.4-14.zip

http://www.2cto.com/uploadfile/2012/0713/20120713110308123.zip

2. 解压放某个盘下面，比如:
 
D:\WampServer\bin\memcached\memcached.exe
 
3. 在终端（也即cmd命令界面）下输入以下命令安装windows服务:
 
D:\WampServer\bin\memcached>memcached.exe -d install
 
4. 再输入下面命令启动：
 
D:\WampServer\bin\memcached>memcached.exe -d start
