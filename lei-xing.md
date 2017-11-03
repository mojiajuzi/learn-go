### 类型

#####  类型判断

```go
var a interface{}
a = "aaaa"
_, ok := a.(bool)
fmt.Println(ok)
```



```go
a := "abc"
re := reflect.TypeOf(a).Kind()
if re == reflect.Bool {
    //TODO
}else{
    //TODO
}
```