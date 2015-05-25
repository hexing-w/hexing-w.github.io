---
layout: post
title:  "php 在apache下配置主机"
date:   2015-05-16 11:47:20
categories: php
excerpt: php 。
---

* content
{:toc}

### 配置虚拟主机

######第一步

找到Apache的配置文件目录下的httpd.conf文件，找到下面这两行：

\# Virtual hosts

\#Include conf/extra/httpd-vhosts.conf

把它改成下面这样：

  \# Virtual hosts

  Include conf/extra/httpd-vhosts.conf

意思是启用虚拟主机的设置文件httpd-vhosts.conf，虚拟主机的设置都在这个文件下
设置，不用去改httpd.conf,这样比较清晰。


######第二步

找到Apache的配置文件httpd.conf。然后按照下列步骤进行配置。

    LoadModule rewrite_module modules/mod_rewrite.so
在apache配置文件里面找到这一行，把前面的注释#号去掉。  

       
      Options FollowSymLinks      
      AllowOverride None      
      Order deny,allow     
      Deny from all 
 
然后再找到这一段代码，把Deny from all改成Allow from all。这样等一下虚拟主机访问就能正常运行了。

######第三步

找到Apache的\conf\extra目录下的httpd-vhost.conf这个文件。
添加：

<VirtualHost 127.0.0.2:80>      
    DocumentRoot "E:/wamp/www/blog"     
    ServerName www.blog.com 

<//VirtualHost>

其中：
DocumentRoot "E:/wamp/www/blog"   这是你的虚拟主机的被授权的目录
ServerName www.blog.com  这是你要访问的域名

######第四步

找到C:\Windows\System32\drivers\etc

打开hosts文件，添加127.0.0.2    www.blog.com

######第五步

重启wamp测试吧