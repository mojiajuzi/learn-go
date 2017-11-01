### sha1

> sha1实现了　SHA-1的计算规则(RFC 3174)，　SHA-1可破解，因此不应该用于安全要求高的应用

##### 静态变量

1. SHA-1的生成字符长度

   ```go
   const BlockSize = 64
   ```

2. SHA-1校验的大小

   ```go
   const Size = 20
   ```

##### 方法

返回一个`hash.Hash`散列校验和

```go
func New() hash.Hash
```

返回一个SHA-1校验的数据

```go
func Sum(data []byte) [Size]byte
```



例如：

```go
h := sha1.New()
io.WriteString(h, "His money is twice tainted:")
io.WriteString(h, " 'taint yours and 'taint mine.")
fmt.Printf("% x", h.Sum(nil))
```

返回字符串形式的hash值

```go
func main() {
	s := "sha1 this string"
	h := sha1.New()
	h.Write([]byte(s))
	bs := h.Sum(nil)
	fmt.Printf("%x\n", bs)
}
```
