---
layout: post
title:  "yaf windows 安装 编译"
date:   2015-08-08 15:14:54
categories: php
excerpt: yaf在windows下的安装和 编译

---

* content
{:toc}


### yaf 安装

yaf有TS和NTS版本，先查看下自己php的版本，用phpinfo()查看下thread safety项，如果为enabled的话，选择TS版本，反之选择NTS版本

打开yaf下载地址，点击DLL，然后根据选择对应版本下载
 
下载完成后，将下载的php_yal.dll文件添加到php的ext文件下。然后修改php.ini配置文件，添加一句：extension=php_yal.dll就可以了。

再次查看phpinfo()的时候，就会出现yaf的配置信息了，这样就算完成了

![yaf](http://hexing-w.github.io/css/pics/yaf.jpg)  


###  yaf 在 windows 编译
<pre><code>
E:\wamp\www\yaf\cg>path 

E:\wamp\www\php-5.2.14

E:\wamp\www\yaf\cg>php.exe yaf_cg Sample

DONE
</code>
</pre>
如果出现whoami 或者 hostname 不是内部或外部命令时，请配置环境变量再运行

它会在cg目录下面生成一个output/sample/文件夹。复制出来就OK

OK ~~~nice

原文出处： http://php8848.com/9.html


