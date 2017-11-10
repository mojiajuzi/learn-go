### Error
对于在Golang中的函数或者方法而言，错误的返回，需要放在最后一位。
对于习惯性的判断，一般将代表错误的返回值与`nil`进行比较，如果返回的是`nil`则表示没有错误发生。


#### 类型定义
在Golang中，`error`的定义是一个接口，其里面只包含一个`Error`方法，该方法返回`string`类型错误信息
```go
type error interface {
    Error() string
}
```

#### 错误声明的方式

##### 使用结构的字段
定义一个结构，然后使用结构来获取错误的信息,比如`filepath`包中的`PathError`结构，其定义如下:
```go
type PathError struct {
    Op string
    Path string
    Err error
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}
```
其`*PathError`实现了`Error`接口，我们就可以如下利用其错误信息，组装自己的信息
```go
func main(){
    f, err := os.Open("/test.txt")
    if err, ok := err.(*os.PathError); ok {
        fmt.Println("File at apath", err.Path,"failed to open")
    }
    fmt.Println(f.Name(), "opened successfully")
}
```

##### 使用结构体的方法
比如`DNSError`结构体的定义如下:
```go
type DNSError struct {
    ...
}

func (e *DNSError) Error() string {
    ...
}

func (e *DNSError) Timeout() bool {
    ...
}

func (e *DNSError) Temporary() bool {
    ...
}
```
因此在项目中我们可以依据其返回错误不同，进行不同的处理
```go
func main() {
	addr, err := net.LookupAddr("www.baidu.com")
	if err, ok := err.(*net.DNSError); ok {
		if err.Timeout() {
			// ...
		} else if err.Temporary() {
			// ...
		} else {
			// ...
		}
		return
	}
	fmt.Println(addr)
}
```

##### 直接比较
可以直接定义一个变量,然后再用来进行比较，例如`filepath`中
```go
var ErrBadPattern = errors.New("syntax error in pattern")
```

#### 参考资料
- [Error Handling](https://golangbot.com/error-handling/)