---
layout: post
title: "goroutine 并发"
date: 2016-11-30 16:46:13
categories: go
excerpt: go
---

* content
{:toc}

#### goroutine
goroutine是Go并行设计的核心。goroutine说到底其实就是线程，但是它比线程更小，十几个goroutine可能体现在底层就是五六个线程，Go语言内部帮你实现了这些goroutine之间的内存共享。执行goroutine只需极少的栈内存(大概是4~5KB)，当然会根据相应的数据伸缩。也正因为如此，可同时运行成千上万个并发任务。goroutine比thread更易用、更高效、更轻便。

goroutine是通过Go的runtime管理的一个线程管理器。goroutine通过go关键字实现了，其实就是一个普通的函数。

	go hello(a,b,c)

通过关键字go 启动一个goroutine

	package main 
	inmport(
		"fmt"
		"runtime"
	)
	
	func say(s string){
		runtime.GoSched()
		for i:=1;i<=5;i++{
			fmt.Println(s)
		}
	}

	func main(){
		go say("hello")
		say("world")

    }
我们可以看到go 关键字很方便的就实现了并发编程，上面多个goroutine运行在 同一个进程中，共享内存数据。不过，从设计上面来看的话，不要通过通信来共享，而是要通过共享来通信


###channels

goroutine运行在相同的地址空间，因此访问共享内存必须要做好同步，那么goroutine之间如何进行数据的通信呢，go提供了一个很好的通信机智channel.channel可以与Unix shell 中的双向管道做类比：可以同它发送或者接收值，这些值只能是特定的类型：channel类型，定义Channel时，也需要定义发送到channel值的类型，注意，必须使用make 创建channel

	







