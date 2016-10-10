---
layout: post
title: "go main redeclared in this block"
date: 2016-10-10 15:06:23
categories: go
excerpt: go
---

* content
{:toc}

### 运行go 程序报错

D:\go\proj是我的GOPATH目录，在此目录下建立了src项目源文件hello
![go-main](http://hexing-w.github.io/css/pics/go-main.png)
  

hello.go

		package main
		import (
			"fmt"
		)	
		func main() {
			fmt.Println("Hello world")
		
		}

hello_test.go

	package main  
	import (  
	    "fmt"  
	    "time"  
	)    
	func main() {  
	    fmt.Println("Welcome to study golang")  
	    fmt.Println("The time is ", time.Now())  
	} 

运行结果：
![go-test](http://hexing-w.github.io/css/pics/go-test.png)

由图可看出go 程序报错了

解决方案：再建立一个文件夹和hello同层的文件夹test，把文件放进去，再次run两个文件，就没问题了。

原因：同一个目录下面不能有个多 package main
