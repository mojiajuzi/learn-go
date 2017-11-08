### 并发(Concurrency)
Golang是一门并发而非并行的语言，在讨论Golang中的并发之前，先理解如下两个问题，
1. 什么是并发？
1. 并发与并行的区别是什么?


### 并发
并发指的是同时处理多个任务的能力，下面举个例子：
假设一个人在慢跑，在他晨跑的过程中，告诉他鞋带松了，这个时候他停下来，系好鞋带并重新开始跑
这就是并发的一个典型的例子，这个人有能力做两件事：跑步和系鞋带，也就是说这个人一次能够处理多件事

#### 并行，以及与并发的区别
并行指的是在同一时间做很多事情，这个和并发十分相似，但是它们是有本质区别的
还是上面跑步的那个例子，一个人边跑步边听音乐，也就是说这个人在相同的时间做着两件事请，这个就是并行

从专业的角度来解释一下并行与并发的区别，一个浏览器有各种各样的行为，其中的两个行为就是：页面渲染和文件下载
现在假设我们有一个包含上面两个行为的页面，每一个行为的执行都是相互独立，互不干扰的(这可以通过语言的多线程实现的，比如Java以及Golang)
当该浏览器运行在一个单核的进程中，该进程的上下文环境将会在页面渲染和文件下载之间相互切换，有时处理的是页面渲染；有时候处理的是文件下载。
这个就是并发，并发的进程中处理的任务是周期性交替执行的
还是这个浏览器，不过这个时候是运行在多核上的进程，在相同的时间点，页面渲染和文件下载可能会运行在不同的处理器上核心上，这就是所谓的并发
![](/assets/concurrency-parallelism-copy.png)

并行的处理任务的时间并不一定总是比并发会，这是因为不同的任务运行可能不得不进行相互通信，
例如，当文件下载完毕后，需要弹出消息提示用户文件下载完毕，这个通信信号发生于文件下载以及页面渲染之间，
这个通信的开销在并发系统中是非常低的，而在并发的多核系统中却是非常高


### Goroutines
Goroutines是一个与其他函数/方法并发的函数/方法,Goroutines可以认为是轻量级的线程,
但是相对于线程来说，其占用的资源更少，更廉价，因此一个Go应用中通常包含上千个并发


#### Goroutines与线程(threads)相比的优势
- Goroutines比线程占用的资源更少，Goroutines只占用几KB大小的栈空间，并且占空间可以依据程序动态调节大小，而线程的栈占用是固定的
- 在部分系统中，Goroutines可以在一个线程中实现多路复用，这样可能出现的情况就是，一个线程中包含上千个Goroutine
  如果线程中的某个Goroutine需要等待用户的输入，那么一个新的线程将会被创建,其余Goroutine将会被转移到新的线程中
  鉴于这个特性，在并发处理的时候，需要注意处理好Goroutine，最好的方式是添加一个干净的API接口
- Goroutine之间的通信使用channels,它被设置用来防止Goroutine在共享内存的时候发生相互竞争的情况,channel相当于Goroutine之间通信的管道

#### 如何开启Goroutine
在函数或者方法前面添加`go`关键字，就能开启一个新的Goroutine
```go
package main

import "fmt"

func hello() {
	fmt.Println("Hello world goroutine")
}

func main() {
	go hello()
	fmt.Println("main function")
}
```
如上所示，在主函数中使用关键字`go`创建了一个Goroutine执行`hello`函数,这个时候`hello`函数以及｀main｀函数会并发执行，
运行如上脚本，你会发现只执行了`main`函数，而没有执行`hello`函数,原因是基于一下两点：
- 当一个新的Goroutine开始的时候，控制语句并没有等待新创建的Goroutine执行完成，就立刻执行创建语句后面的代码，新创建
的Goroutine的任何返回都将会被忽略
- main Goroutine应该在其他的Goroutine执行完成之后再结束，否则其他的Goroutine将会被强制终止执行

因此，为了正确执行上面的语句，可以使用`time`包的`Sleep`函数，暂缓main函数的执行
```go
func main(){
    go hello()
    time.Sleep(1 * time.Second)
}
```
上面是使用了暂缓执行这种低级的方式来是的Goroutine执行，Channels使用能够阻止main　Goroutine在其他Goroutine执行完成之前执行完成

#### 同时启动多个Goroutine
```go
package main

import (
	"fmt"
	"time"
)

func numbers() {
	for i := 1; i <= 5; i++ {
		time.Sleep(250 * time.Millisecond)
		fmt.Printf("%d ", i)
	}
}

func alphabets() {
	for i := 'a'; i <= 'e'; i++ {
		time.Sleep(400 * time.Millisecond)
		fmt.Printf("%c ", i)
	}
}

func main() {
	go numbers()
	go alphabets()

	time.Sleep(3000 * time.Millisecond)
	fmt.Println("main terminated")
}

```
运行如上程序，将会固定打印出:
```bash
1 a 2 3 b 4 c 5 d e main terminated
```
![](/assets/Goroutines-explained.png)
#### Channels



### 参考文档

- [Golang tutorial series](https://golangbot.com/learn-golang-series/)