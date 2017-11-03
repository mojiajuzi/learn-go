### 基础知识

#### 文件操作权限

对于文件的操作为：可读（Read）,可写(Write),可执行(Execute),其各自的权重为：

- Read(4)
- Write(2)
- Execute(1)

假如在linux系统中执行如下命令：

```ba
ls -l filename second.jpg
```

将会得到如下的信息:

```ba
-rw-rw-r-- 1 gru gru 46228 11月  2 10:05 second.jpg
```

分解可得：

- `-`:标识文件类型，`-`标识常规文件，　‘d’标识目录
- `rw-`:拥有者(owner)的权限
- `rw-`:拥有者所在用户组(group)所拥有的权限
- `r--`:所有其他人(other)所拥有的权限
- `1`标识这是硬链接
- `gru`　拥有者
- `gru` 用户组
- 46228 文件的大小，单位为byte
- `11月　2 10:05`　标识文件最后一次写入的时间

### os
> os包中的文件的相关操作

#### 文件操作

##### 打开

###### Open

```go
func Open(name string)(*File, error)
```

只读的形式打开一个文件，读取失败，将会返回一个路径错误的信息

###### OpenFile

```go
func OPenFile(name string, flag int, perm FileMode)(*FIle, error)
```

- name: 文件名称
- flag:读取模式，可以使用`|`分割的多种模式并用，`os.O_APPEND|os.O_CREATE`
- perm: 权限

###### Pipe

```go
func Pipe()(r *File, w *file, err error)
```

返回一个连接的文件，从`r`读取并写入`w`

##### 新增

###### create

```go
func Create(name string) (*File, error)
```

创建一个新的权限为0666的文件，如果文件存在，将会被替换，如果创建失败，将会返回一个路径错误的指针

###### NewFile

```go
func NewFile(fd uintptr, name string) *File
```

通过给定的文件名以及文件大小返回一个文件，如果返回的结果为空，那么表明`fd`是错误的

##### 修改

###### Chmod

```go
func (f *file)Chmod(mode FileModel) error
```

变更文件的权限

###### Chdir

```go
func (f *FIle) Chdir() error
```

变更工作目录到当前文件所在目录

###### Chown

```go
func (f *File) Chown(uid, gid int) error
```

变更文件的所有者和所在组,在windows系统下，将会永远返回错误

##### 读取

###### Fd

```go
func (f *File) Fd() uintptr
```

返回unix系统文件描述符的整数

###### Name

```go
func ( f *File)Name()string
```

返回打开文件的文件名称，包含文件类型

###### Read

```go
func (f *File) Read(b []byte)(n int, err error)
```

读取`len(b)`长度的字节，如果是在文件的末尾，返回的结果是`0, io.EOF`

###### ReadAt（尚未理解）

```go
func (f *File) Read(b []byte, off int64)(n int, error)
```

读取`[len(b), off]`区间范围的字节数,如果`n < len(b)`总是返回一个非空的错误，读取到文件末尾，讲会返回`EOF`

###### Readdir（尚未理解）

```go
func(f *File) Readdir(n int)([]FileInfo, error)
```

###### Readdirnames





##### 删除

##### 关闭

###### Close

```go
func (f *File) Close() error
```

关闭一个打开的文件



### 参考文档

- [Linux Files and File Permission](http://www.comptechdoc.org/os/linux/usersguide/linux_ugfilesp.html)