---
layout: post
title: "python安装模块时出现 error:Microsoft Visual C++ 14.0 is required"
date: 2018-03-23 20:04:33
categories: python 
excerpt: python
---

* content
{:toc}


在安装scrapy时出现报错，如图所示：

![pyc.png](http://hexing-w.github.io/css/pics/pyc.png)

通过提示我们可以得知是由于c++依赖库不存在，所以在对包进行编译的时候出错，故我们可以去直接下载二进制安装包进行安装。跟着地址进去提示下载build-tools 

然后选择了用源码安装,先在这个[地址](https://www.lfd.uci.edu/~gohlke/pythonlibs/)根据自己的机器安装，下载对应所需要的模块，如图所示

![twisted.png](http://hexing-w.github.io/css/pics/twisted.png)


在cmd中输入dos命令，进入下载好的whl文件夹下

		C:\Users\Administrator\AppData\Local\Programs\Python\Python36-32\Scripts>pip install Scrapy-1.5.0-py2.py3-none-any.whl

执行完之后会看到如图所示：

![twisted.png](http://hexing-w.github.io/css/pics/twisted.png)


这样scrapy就安装好了



