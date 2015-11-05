<!-- ---
layout: post
title:  "php 在windows 下安装mongodb"
date:   2015-09-17 13:41:54
categories: go
excerpt: go
--- -->

* content
{:toc}

### 下载golang
1.下载golang 安装包[golang](http://www.golangtc.com/download)
 
2.下载好了以后，在环境变量设置GOROOT和GOPATH,GOROOT设置为golang SDK的安装路径，
GOPATH即为项目开发路径，最好是将路径设置为安装路径下的src目录中

3.输入env查看变量是否设置成功

![mongo](http://hexing-w.github.io/css/pics/go.png) 

4.ok 开启我们的hello world 之旅吧
<pre><code>
package main  
import(
	"fmt"
)

 func main() {
	fmt.Println("Hello world ,welcome to golang ");
}

</code></pre>
<font color="red">	  【attention】:一定要配置好你的环境变量</font>


5.这样我们就在电脑上面安装好了golang
OK ~~~nice,赶紧试试吧




