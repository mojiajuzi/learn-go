### mime/multipart

> 该包实现了文件(基于RFC 2046标准)内容解析，主要用户HTTP请求以及浏览器初始化消息体的解析

#### 变量

```go
var ErrMessageTooLarge = errors.New("multipart:message too large")
```

##### 类型

##### File

`File`类型是一个接口，定义了用于访问文件相关信息的接口，对于存储在硬盘上的文件内容，那么其底层的实现将会是`*os.File`

```go
type FIle interface {
  io.Reader
  io.ReaderAt
  io.Seeker
  io.Closer
}
```

##### FileHeader

该结构体描述了请求中文件的头部信息

```go
type FileHeader struct {
  Filename string  //文件名
  Header textproto.MIMEHeader
  Size int64　//文件大小
}
```

###### Open

```go
func (fh *FileHeader)Open()(FIle, error)
```

返回文件头相关联的文件

##### Form

解析后的文件上传表单，上传的文件存储在内存或硬盘上，可通过`Open`方法获得

```go
type Form struct {
  Value map[string][]string
  File map[string][]*FileHeader
}
```

###### RemoveAll

```go
func (f *Form) RemoveAll()error
```

该方法移除所有与表单相关的临时文件

##### Part

`Part`代表的是消息体

```go
type Part struct {
  Header textproto.MIMEHeader
}
```

###### Close

```go
func (p *Part) Close() error
```

###### FileName

```go
func (p *Part) FileName() string
```

返回`Content-Disposition`头部所标识的文件名

###### FormName

```go
func (p *Part) FormName() string
```

如果`Content-Disposition`的类型为`form-data`则返回其名称，否则返回空字符串

###### Read

```go
func (p *Part) Read(d []byte)(n int, err error)
```

在头部之后以及下一个消息体之前读取消息体

－－－未完待续