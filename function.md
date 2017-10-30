### 函数

> 函数以关键字`func`开头，后面跟着函数名称，函数的参数定义格式为:`name type, name type` ,参数之后需要添加返回数据格式

```go
func average(xs []float64) float64 {
    //TODO
}
```

##### 返回值

1. 函数的返回值可以使用命名,这样在使用`return`关键字的时候，无需添加返回变量

   ```go
   func average(xs []float64) (x float64){
       //TODO
     　return
   }
   ```

2. 多返回值,对于多返回值而言，更多的是使用在错误的返回(`x,err := f()`)，或者在使用布尔值标识操作是否成功(`x, ok := f()`)

   ```go
   func f() (int, int){
       //TODO
   }
   ```

   ​

##### 函数的调用栈

```go
func main() {
	fmt.Println(f1())
}

func f1() int {
	return f2()
}

func f2() int {
	return 1
}
```

每一次调用函数的时候，将函数压入栈，每一次从函数返回的时候，将栈顶的函数取出栈



##### 可变函数

> 可变函数最后一个参数使用`...Type`表示可以接受任意数量的参数

```go
func add(args ...int) int {
    //TODO
}

func main(){
  xs := []int{1,2,3}
  fmt.Println(xs...)
}
```



##### 函数闭包

> 保存对局部变量的引用，使得在多次调用中持续对局部变量的修改和使用的函数

```go
func makeEvenGenerator() func() uint {
	i := uint(0)
	return func() (ret uint) {
		ret = i
		i += 2
		return
	}
}

func main() {
	nextEvent := makeEvenGenerator()
	fmt.Println(nextEvent()) //0
	fmt.Println(nextEvent()) //2
	fmt.Println(nextEvent()) //4
}
```



##### 函数递归

```go
func factorial(x uint) uint {
  if x == 0 {
      return 1
  }
  return x*factorial(x-1)
}
```



##### defer

> 延迟执行函数，也就是在一个函数执行完毕后执行函数

###### 匿名返回值

```go
func a() int {
    var i int
	defer func() {
		i++
		fmt.Println("a defer2:", i)
	}()

	defer func() {
		i++
		fmt.Println("a defer1:", i)
	}()
	return i
}

func main() {
	fmt.Println("a return: ", a())
}
```

其输出语句为:

```go
a defer1: 1
a defer2: 2
a return:  0
```



###### 有名返回值

```go
func a() (i int) {
	defer func() {
		i++
		fmt.Println("a defer2:", i)
	}()

	defer func() {
		i++
		fmt.Println("a defer1:", i)
	}()
	return i
}

func main() {
	fmt.Println("a return: ", a())
}
```

其输出结果为:

```go
a defer1: 1
a defer2: 2
a return:  2
```

对比以上有名函数和匿名函数返回值的情况，可以得出以下结论:

- 多个defer的执行顺序为:后进先出
- 匿名返回值在`return`执行时被声明，有名返回值则是在函数声明的同时被声明，因此`defer`只能访问有名的返回值