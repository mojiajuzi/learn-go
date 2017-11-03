### rand

> "crypto/rand"实现了伪随机数生成

#### 变量

`Reader`是一个全局共享的强伪随机数生成，

在Linux系统中，如果`/dev/urandom`可用，那么将会采用`getrandom(2)`的方式实现`Reader`，

在OpenBSD系统中，采用的是`getentropy`方式实现`Reader`

在类unix系统中，采用的是`/dev/urandom`方式实现`Reader`

在Windows系统中，是通过调用`CryGenRandom`API实现的

```go
var Reader io.Reader
```

#### 函数

`Int`限定上限，返回一个随机数

```go
func Int(rand io.Reader, max *big,Int)(n *big.Int, err error)
```

```go
func main(){
	b := big.NewInt(10)
	a, err := rand.Int(rand.Reader, b)

	if err != nil {
		fmt.Println(err.Error())
	}
	fmt.Println(a)
}
```

如上只会在[0,10]之间产生随机数，如果`n < 0`，将会产生一个`panic`

`Prime`返回一个数字

```go
func Prime(rand io.Reader, bits int)(p *big.Int, err error)
```

`Read`是一个辅助函数，能够使得使用`Reader.Read`的时候使用的是`io.ReadFUll`,对于返回结果而言，只有当`err != nil`的时候，返回的是`n ==len(b)`

```go
func main(){
	c := 10
	b := make([]byte, c)
	_, err := rand.Read(b)
	if err != nil {
		fmt.Println("error:", err)
		return
	}
	fmt.Println(bytes.Equal(b, make([]byte, c))) //false
}
```







