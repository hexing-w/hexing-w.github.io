---
layout: post
title: "Python中使用protobuf"
date: 2017-06-29 15:24:33
categories: python
excerpt: python
---

* content
{:toc}

###安装

首先下载对应语言版本的protobuf[protobuf](https://github.com/google/protobuf)

到python目录，编译

	python setup.py build 
	python setup.py install 
	python setup.py test

验证安装是否成功

![protobuf-test](http://hexing-w.github.io/css/pics/protobuf-test.png)

如果有报错，ptotoc 没找到，可以在./src下编译protoc,或者直接下载[protoc](https://repo1.maven.org/maven2/com/google/protobuf/protoc/3.3.0/)


###使用

	syntax = "proto3";
	message hx
	{
		string name = 1;
		uint32 age = 2;
		string sex = 3;
		string address = 4;
		
	}

	
	D:\python\protobuf-3.3.0\src>protoc -I=../result --python_out=../result ../resul
	t/hx.proto

其中 -I是源文件目录 --python_out是文件输出目录 最后的参数addressbook.proto是你需要编译的协议文件，

编译好之后你就会在目标目录里面看到输出的结果文件，hx_pb2.py文件

导入生成的文件并使用

	from proto import hx_pb2
	
	info = hx_pb2.InfoRequest()
	info.ParseFromString(byteArray.ReadBytes())
