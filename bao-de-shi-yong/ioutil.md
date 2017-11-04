### ioutil

> ioutil包实现了I/O的一些通用功能

#### 变量

##### Discard

```go
var Discard io.Writer = devNull(0)
```

该变量是一个`io.Writer`, 当所有的写入成功，没有出现错误



#### 方法

##### NopCloser

```go
func NopCloser(r io.Reader) io.ReadCloser
```

返回一个`ReadCloser`，关闭`r`



##### ReadAll

```go
func ReadAll(r io.Reader)([]byte, error)
```

读取所有的数据，直到出现错误或者读取到末尾，成功返回`nil`，失败返回`EOF`



##### ReadDir

```go
func ReadDir(dirname string)([]byte, error)
```

通过给定的路径名称，读取该目录下面的所有文件，并返回按照文件名称排序的列表



##### ReadFile

```go
func ReadFile(filename string)([]byte, error)
```

按照给定的文件的名称，返回文件的内容，读取成功返回`nil`,失败返回`EOF`



##### TempDir

```go
func TempDir(dir, prefix string) (name string, err error)
```

在指定的`dir`目录下面，以给定的`prefix`作为前缀，后面跟上随机生成的字符串，作为目录名称，返回新创建的目录的路径名称，如果没有指定目录，将会使用系统默认的临时目录



##### TempFile

```go
func TempFIle(dir, prefix string)(f *os.File, err error)
```

在指定的`dir`目录下面，以给定的`prefix`作为前缀，后面跟上随机生成的字符串，作为文件名称，返回新创建的文件的路径名称，如果没有指定目录，将会使用系统默认的临时目录

##### WriteFile

```go
func writeFile(filename string, data []byte, perm os.FileMode) error
```

将数据写入文件，并覆盖原先的内容，如果文件不存在，将会创建新的文件