
### alice包简介

```go
type Chain struct {
    //一些字段
}
```
该结构体的行为就像是一个初始化的`http.Handler`的列表，该结构体一旦被创建，将会是不可变的，并且永远保持这初始化时相同的执行顺序


```go
func New(constructors ...Constructor)Chain
```
依据给定的中间件列表，创建一个新的chain,新的服务没有其他的函数，初始化之后，在中间件之后只能调用`Then`函数

```go
func (c Chain) Append(constructors ...Constructor)Chain
```
扩展已经存在的中间件，新添加的将会加入末尾
```go
//请求经过的顺序为：m1,m2
stdChain := alice.New(m1,m2)

//请求经过的中间件为：m1,m2,m3,m4
extChain := stdChain.Append(m3,m4)
```

```go
func (c Chain) Extend(chain Chain)Chain
```
将一个chain添加到另外一个chain上
```go
//请求经过的顺序为：m1,m2
stdChain := alice.New(m1,m2)
ext1 := alice.New(m3,m4)
//请求经过的中间件为：m1,m2,m3,m4
extChain := stdChain.Extend(ext1)
```

```go
func (c Chain) Then(h http.Handler) http.Handler
```
经过中间之后，所需要进入的处理句柄，其中`New(m1,m2,m3).Then(h)`等价于`m1(m2(m3(h)))`

```go
func (c Chain) ThenFunc(fn http.HandlerFunc) http.Handler
```

```go
type Constructor func(http.Handler)http.Handler
```