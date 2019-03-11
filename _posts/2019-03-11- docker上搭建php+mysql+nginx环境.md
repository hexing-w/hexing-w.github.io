---
layout: post
title: "docker上搭建php+mysql+nginx环境"
date: 2019-03-11 09:45:03
categories: docker
excerpt: docker
---

* content
{:toc}

### docker环境的安装

1. 用docker ps命令查看是否安装docker 。没有安装docker环境的 可以按之前的文章或者百度docker环境的搭建

### 安装nginx 

1. 安装

	docker pull nginx

2. 准备好本地配置文件

在本地准备好nginx的配置文件，运行nginx的时候需要用
	
	mkdir /var/www/nginx/conf.d
	touch default.conf

配置default.conf

	server {
    listen       80;
    server_name  localhost;

    location / {
        index index.html index.php;
        if (!-e $request_filename) {
            rewrite  ^/(.*)$  /index.php?s=$1  last;
            break;
        }
        root   /var/www/html;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        fastcgi_pass 172.16.0.6:9001;  //或者改成codeforphp_phpfpm：9000
        fastcgi_index  index.php;
        include        fastcgi_params;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny  all;
    }
}
3. 运行nginx

	docker run -d 
    -p 8081:80  
    -v /var/nginx/www/html:/var/www/html  
    -v /var/www/nginx/conf.d:/etc/nginx/conf.d     
    --name codeforphp_nginx 
    nginx

各个参数的解释： 各个参数配置遵循这个规则： 本地目录:容器地址

--name 是tag名字

-p 端口映射

-v 配置文件夹

-d 后台运行

### 安装php 

1. 安装

	docker pull php:7.0-fpm


2. 运行php

	docker run -d 
    -v /var/nginx/www/html:/var/www/html 
    -p 9001:9000 
    --name codeforphp_phpfpm 
    php:7.0-fpm-hx

应用程序的本地地址在/var/nginx/www/html文件夹，在/var/nginx/www/phpfile   里边新建  php.ini 文件

3. 安装php扩展

先进入容器： docker exec -it 743ec9a86f68 bash 
	
	docker-php-ext-install pdo_mysql
	docker-php-ext-install redis

### 安装mysql 

1. 安装
	docker pull mysql:5.7.17

2.运行

	docker run -d 
   -p 3307:3306 
   --name mysql 
   -e MYSQL_ROOT_PASSWORD=12345678 
   registry.saas.hand-china.com/tools/mysql:5.7.17

### 测试

1. 查看服务

	docker ps 
![docker-ps](http://hexing-w.github.io/css/pics/docker-ps.png)

STATUS 皆为up,即运行中 


2. 查看搭建的应用

![docker-apply](http://hexing-w.github.io/css/pics/docker-apply.png)










 







