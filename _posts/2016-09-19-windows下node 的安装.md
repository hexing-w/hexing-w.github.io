---
layout: post
title: "node js windows下的安装"
date: 2016-09-19 17:29:23
categories: js
excerpt: js
---

* content
{:toc}

其实我安装Node 环境 是想去了解一下hexo滴。。先默默安装环境吧

### node下载 [node官网地址](http://nodejs.org/)

根据自己的电脑下载相应的系统安装程序，我电脑是win7 64 位的,故选择了64位的msi，下载完成后，双击msi，开始默认安装nodejs，这里我们可以修改默认的安装目录。
  

### 安装相关环境

打开安装目录 d:\node ,里面有自带的npm ，直接用npm安装相关环境即可

进入node.js安装目录 d:\node

用cmd打开我们的windows命令界面，键入命令 cd d:\node

#####安装相关组件
键入命令：npm install express 安装express。。。。

键入命令：npm install jade 安装jade。。。

键入命令：npm install debug 安装debug。。。


。。。。安装什么组件取决于环境搭建需求

默认情况下上述组件都是安装在d:\node\node_modules文件夹下 这也是nodejs相关组件的自动查找路径。


### 创建一个工程

<font color="red">键入命令：npm install -g express-generator 切记 一定要安装这个 否则express执行不了！！！</font>

express命令安装在全局才起作用!

所以express安装的时候要用 npm install express -g

或者直接修改全局路径:

npm config set prefix "d:\node"

npm config set cache "d:\node\cache" (先建好cache目录)

键入:express myapp (myapp是随意起的工程名称)
![myapp](http://hexing-w.github.io/css/pics/project.png)


你会发现多了一个 d:\node\myapp 目录

默认情况下：里会自动创建
![myapp](http://hexing-w.github.io/css/pics/create-proj.png)

### demo测试
 复制node_modules到myapp下面

环境搭建到此完工，下面做一个demo测试！

在myapp里创建一个hello.js 内容：
<pre>
var http=require("http");
http.createServer(function(request,response){
	response.writeHead(200,{"Content-Type":"text/plain"});
	response.write("hello world");
	response.end();
}).listen(8888);

console.log("nodejs start listen 8888 port");

进入命令窗口 进入d:\node\myapp目录

键入node hello.js
![helo-world](http://hexing-w.github.io/css/pics/hello-world.png)

打开地址http://127.0.0.1:8888/

发现输出 :
![node-hello](http://hexing-w.github.io/css/pics/node-hello.png)
