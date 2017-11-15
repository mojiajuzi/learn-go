### defer, panic,cover
不完全翻译Golang Bolg,[defer-panic-and-recover](https://blog.golang.org/defer-panic-and-recover)

#### defer
defer将一个函数推入列表，并在所有函数执行完成之后执行，因此常用来处理一些善后工作

defer包含以下规则:
- 如果defer函数包含参数，那么参数的值在执行到defer声明的地方的时候就已经确定了
- 多个defer函数，是按照先进后出的规则处理的
- 如果指定了返回参数名称，那么defer是可以修改返回参数的值，如果没有指定名称，那么将不能修改


#### Panic
Panic是一个内建函数，被设计用来停止程序控制流继续运行并引发一个异常.

假设有一个函数F,当F执行到panic的时候，会触发一下事件:
- 函数F将会停止运行
- 定义在F函数中使用defer声明的的函数将会继续运行
然后F函数返回给调用者,对于调用者而言，就像是调用了一个异常函数.然后进程继续执行，
直到调用者这个goroutine中的所有函数执行完毕为止，这个时候程序就会崩溃


#### Recover
Recover是一个内建的函数，被设计用来对Panic进行控制

在正常的执行过程中，调用recover将会返回一个`nil`,除此之外没有任何影响
如果当前的goroutine中出现异常，那么将会捕获异常值，并且使得goroutine继续运行

####示例
```go
package main

import "fmt"

func main() {
	f()
	fmt.Println("returned normally from f.")
}

func f() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("Recovered in f", r)
		}
	}()
	fmt.Println("calling g.")
	g(0)
	fmt.Println("Returned normally from g")
}

func g(i int) {
	if i > 3 {
		fmt.Println("panicking!")
		panic(fmt.Sprintf("%v", i))
	}
	defer fmt.Println("defer in g", i)
	fmt.Println("Printing in g", i)
	g(i + 1)
}
```

