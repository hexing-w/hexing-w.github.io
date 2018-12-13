---
layout: post
title: "centos7上安装docker"
date: 2018-12-13 14:14:13
categories: php 
excerpt: php
---

* content
{:toc}

### 安装docker

1.Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 

通过uname -r命令查看你当前的内核版本

    uname -r

2.yum包更新到最新

	sudo  yum update

3.卸载旧版本

	sudo yum remove docker  docker-client docker-client-latest  docker-latest docker-latest-logrotate  docker-logrotate docker-common docker-selinux docker-engine-selinux docker-engine

4.安装需要的软件包 yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

	sudo yum install -y yum-utils device-mapper-persistent-data lvm2

5.设置yum源

	sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

6.查看所有仓库中所有docker版本，并选择特定版本安装

	yum list docker-ce --showduplicates | sort -r

7.安装docker

	sudo yum install docker-ce

8.启动服务

	sudo systemctl start docker


9.通过运行hello-world映像验证是否正确安装了docker。

	sudo docker run hello-world

如图所示

![docker-hello-world](http://hexing-w.github.io/css/pics/docker-hello-world.png)


有兴趣的可以直接看官方手册[docker](https://docs.docker.com/install/linux/docker-ce/centos/) 


