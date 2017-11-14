---
layout: post
title: "nginx配置location总结及rewrite规则写法"
date: 2016-09-10 11:02:13
categories: nginx
excerpt: nginx
---

* content
{:toc}


### location正则写法

一个实例

	location  = / {
	  # 精确匹配 / ，主机名后面不能带任何字符串
	  [ configuration A ] 
	}
	
	location  / {
	  # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
	  # 但是正则和最长字符串会优先匹配
	  [ configuration B ] 
	}
	
	location /documents/ {
	  # 匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索
	  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
	  [ configuration C ] 
	}
	
	location ~ /documents/Abc {
	  # 匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索
	  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
	  [ configuration CC ] 
	}
	
	location ^~ /images/ {
	  # 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条。
	  [ configuration D ] 
	}
	
	location ~* \.(gif|jpg|jpeg)$ {
	  # 匹配所有以 gif,jpg或jpeg 结尾的请求
	  # 然而，所有请求 /images/ 下的图片会被 config D 处理，因为 ^~ 到达不了这一条正则
	  [ configuration E ] 
	}
	
	location /images/ {
	  # 字符匹配到 /images/，继续往下，会发现 ^~ 存在
	  [ configuration F ] 
	}
	
	location /images/abc {
	  # 最长字符匹配到 /images/abc，继续往下，会发现 ^~ 存在
	  # F与G的放置顺序是没有关系的
	  [ configuration G ] 
	}
	
	location ~ /images/abc/ {
	  # 只有去掉 config D 才有效：先最长匹配 config G 开头的地址，继续往下搜索，匹配到这一条正则，采用
	    [ configuration H ] 
	}
	
	location ~* /js/.*/\.js

* 已=开头表示精确匹配
	如 A 中只匹配根目录结尾的请求，后面不能带任何字符串。

* ^~ 开头表示uri以某个常规字符串开头，不是正则匹配
* ~ 开头表示区分大小写的正则匹配;
* ~* 开头表示不区分大小写的正则匹配
* / 通用匹配, 如果没有其它匹配,任何请求都会匹配到

顺序 no优先级：

(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~* 正则顺序) > (location 部分起始路径) > (/)

上面的匹配结果
按照上面的location写法，以下的匹配示例成立：

* / -> config A
	精确完全匹配，即使/index.html也匹配不了

* /downloads/download.html -> config B
	匹配B以后，往下没有任何匹配，采用B

* /images/1.gif -> configuration D
	匹配到F，往下匹配到D，停止往下

* /images/abc/def -> config D
	最长匹配到G，往下匹配D，停止往下
	你可以看到 任何以/images/开头的都会匹配到D并停止，FG写在这里是没有任何意义的，H是永远轮不到的，这里只是为了说明匹配顺序

* /documents/document.html -> config C
	匹配到C，往下没有任何匹配，采用C

* /documents/1.jpg -> configuration E
	匹配到C，往下正则匹配到E

* /documents/Abc.jpg -> config CC
	最长匹配到C，往下正则顺序匹配到CC，不会往下到E

	

### 实际使用

	server {
	        listen       80;
	        server_name  api.ssgame.cn ;
	        root   "E:\phpstudy\WWW\sim\service\Public";
	        location / {
	            index  index.html index.htm index.php;
	            #autoindex  on;
	        }
			# 重写规则信息
	        if (!-f $request_filename){
	            rewrite (.*) /index.php/$1 last;
	        }
	        location ~ \.php(.*)$ {
	            fastcgi_pass   127.0.0.1:9000;
	            fastcgi_index  index.php;
	            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
	            include        fastcgi_params;
	        }
			# 处理静态文件请求，这是nginx作为http服务器的强项
			# 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使
	        location ~ .*\.(gif|jpeg|png)$ {
	            root "E:\phpstudy\WWW\sim\server\cn_server\service\Public";
	        }
	}

#### Rewrite规则

 rewrite功能就是，使用nginx提供的全局变量或自己设置的变量，结合正则表达式和标志位实现url重写以及重定向。rewrite只能放在server{},location{},if{}中，并且只能对域名后边的除去传递的参数外的字符串起作用，例如 http://seanlook.com/a/we/index.php?id=1&u=str 只对/a/we/index.php重写。语法rewrite regex replacement [flag];

如果相对域名或参数字符串起作用，可以使用全局变量匹配，也可以使用proxy_pass反向代理。

如果相对域名或参数字符串起作用，可以使用全局变量匹配，也可以使用proxy_pass反向代理。

表明看rewrite和location功能有点像，都能实现跳转，主要区别在于rewrite是在同一域名内更改获取资源的路径，而location是对一类路径做控制访问或反向代理，可以proxy_pass到其他机器。很多情况下rewrite也会写在location里，它们的执行顺序是：

	执行server块的rewrite指令
	执行location匹配
	执行选定的location中的rewrite指令

如果其中某步URI被重写，则重新循环执行1-3，直到找到真实存在的文件；循环超过10次，则返回500 Internal Server Error错误。

#### flag标志位

* last : 相当于Apache的[L]标记，表示完成rewrite
* break : 停止执行当前虚拟主机的后续rewrite指令集
* redirect : 返回302临时重定向，地址栏会显示跳转后的地址
* permanent : 返回301永久重定向，地址栏会显示跳转后的地址

因为301和302不能简单的只返回状态码，还必须有重定向的URL，这就是return指令无法返回301,302的原因了。这里 last 和 break 区别有点难以理解：

1. last一般写在server和if中，而break一般使用在location中
2. last不终止重写后的url匹配，即新的url会再从server走一遍匹配流程，而break终止重写后的匹配
3. break和last都能组织继续执行后面的rewrite指令

#### if指令与全局变量

#####  if判断指令
语法为if(condition){...}，对给定的条件condition进行判断。如果为真，大括号内的rewrite指令将被执行，if条件(conditon)可以是如下任何内容：

* 当表达式只是一个变量时，如果值为空或任何以0开头的字符串都会当做false
* 直接比较变量和内容时，使用=或!=
* ~正则表达式匹配，~*不区分大小写的匹配，!~区分大小写的不匹配

-f和!-f用来判断是否存在文件
-d和!-d用来判断是否存在目录
-e和!-e用来判断是否存在文件或目录
-x和!-x用来判断文件是否可执行

##### 全局变量

下面是可以用作if判断的全局变量
	
	$args ： #这个变量等于请求行中的参数，同$query_string
	$content_length ： 请求头中的Content-length字段。
	$content_type ： 请求头中的Content-Type字段。
	$document_root ： 当前请求在root指令中指定的值。
	$host ： 请求主机头字段，否则为服务器名称。
	$http_user_agent ： 客户端agent信息
	$http_cookie ： 客户端cookie信息
	$limit_rate ： 这个变量可以限制连接速率。
	$request_method ： 客户端请求的动作，通常为GET或POST。
	$remote_addr ： 客户端的IP地址。
	$remote_port ： 客户端的端口。
	$remote_user ： 已经经过Auth Basic Module验证的用户名。
	$request_filename ： 当前请求的文件路径，由root或alias指令与URI请求生成。
	$scheme ： HTTP方法（如http，https）。
	$server_protocol ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
	$server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
	$server_name ： 服务器名称。
	$server_port ： 请求到达服务器的端口号。
	$request_uri ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
	$uri ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
	$document_uri ： 与$uri相同。
	例：http://localhost:88/test1/test2/test.php
	$host：localhost
	$server_port：88
	$request_uri：http://localhost:88/test1/test2/test.php
	$document_uri：/test1/test2/test.php
	$document_root：/var/www/html
	$request_filename：/var/www/html/test1/test2/test.php

#### 常用正则

	. ： 匹配除换行符以外的任意字符
	? ： 重复0次或1次
	+ ： 重复1次或更多次
	* ： 重复0次或更多次
	\d ：匹配数字
	^ ： 匹配字符串的开始
	$ ： 匹配字符串的介绍
	{n} ： 重复n次
	{n,} ： 重复n次或更多次
	[c] ： 匹配单个字符c
	[a-z] ： 匹配a-z小写字母的任意一个
	小括号()之间匹配的内容，可以在后面通过$1来引用，$2表示的是前面第二个()里的内容。正则里面容易让人困惑的是\转义特殊字符。

原文出处：http://www.w3school.com.cn/php/php_filter.asphttps://segmentfault.com/a/1190000002797606