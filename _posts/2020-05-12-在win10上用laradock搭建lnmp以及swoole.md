---
layout: post
title: "laradock上搭建lnmp以及swoole"
date: 2020-05-12 14:32:03
categories: docker
excerpt: docker
---

* content
{:toc}

### Docker 安装使用

首先我们需要在系统安装 Docker 的免费社区版，官方提供 Windows、Mac 及 Linux 等版本下载：

[下载地址](https://www.docker.com/community-edition#/download)

### laradock 

1. 首先将 Laradock 项目代码克隆到本地：

	git clone https://github.com/Laradock/laradock.git

2. 进入 laradock 目录将 env-example 重命名为 .env：

    cp env-example .env

3. 修改里面的参数
	
	PHP_FPM_INSTALL_SWOOLE=true
	WORKSPACE_INSTALL_SWOOLE=true
    NGINX_HOST_HTTP_PORT=8080 //宿主机暴露的端口

4. 安装需要的容器

    docker-compose up -d nginx redis mysql
    
    ps:下载nginx，(可能 也许) 速度太慢，而且还会由于网络的原因失败，可以修改nginx的dockerFile 
    ![docker-nginx](http://hexing-w.github.io/css/pics/docker-nginx.jpg)

5. 要测试配置访问域名指向 Docker 环境目录，我们先在 laradock 父级目录下创建一个与 laradock 同级的 html 目录，然后在 html 目录下运行 拉取自己的项目代码 命令创建一个新的 Laravel 应用，相应的层级目录,修改.env
    
    APP_CODE_PATH_HOST=../html  //挂载目录

重启nginx服务

    docker-compose up -d nginx

进入容器检查一下是否安装成功了 php -m | grep swoole，如果打印出了swoole，就证明安装成功了
或者打开浏览器输入127.0.0.1:8080

![swoole](http://hexing-w.github.io/css/pics/swoole.png)









 







