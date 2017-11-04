### 基础知识

#### 文件操作权限

对于文件的操作为：可读（Read）,可写\(Write\),可执行\(Execute\),其各自的权重为：

* Read\(4\)
* Write\(2\)
* Execute\(1\)

假如在linux系统中执行如下命令：

```ba
ls -l filename second.jpg
```

将会得到如下的信息:

```ba
-rw-rw-r-- 1 gru gru 46228 11月  2 10:05 second.jpg
```

分解可得：

* `-`:标识文件类型，`-`标识常规文件，　‘d’标识目录
* `rw-`:拥有者\(owner\)的权限
* `rw-`:拥有者所在用户组\(group\)所拥有的权限
* `r--`:所有其他人\(other\)所拥有的权限
* `1`标识这是硬链接
* `gru`　拥有者
* `gru` 用户组
* 46228 文件的大小，单位为byte
* `11月　2 10:05`　标识文件最后一次写入的时间

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

* name: 文件名称
* flag:读取模式，可以使用`|`分割的多种模式并用，`os.O_APPEND|os.O_CREATE`
* perm: 权限

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

###### Write

```go
func (f *File) Write(b []byte)(n int, err error)
```

将长度为`len(b)`字节的数据写入文件，并返回写入字节的大小，如果返回的长度与写入的长度不一致，将会返回一个非空的值

###### WriteAt

```go
func (f *File) WriteAt(b []byte, off int64)(n int, err error)
```

将指定长度的内容从偏移量`off`处开始，写入文件，返回结果与`Write`方法一致

###### WriteString

```go
func (f *File) WriteString(s string)(n int, err error)
```

将字符串写入到文件中

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

从`off`位置读取`len(b)`长度的字节数,并将读取到的结果存储到`b`切片中，如果`n < len(b)`总是返回一个非空的错误，读取到文件末尾，将会返回`EOF`

###### Readdir（尚未理解）

```go
func(f *File) Readdir(n int)([]FileInfo, error)
```

###### Readdirnames

###### seek

```go
func(f *File) Seek(offset int64, whence int)(ret int64, err error)
```

设置文件指针到指定的位置，只对下一次文件读取和写入操作有效，并返回当前文件的偏移量，该方法对于以`O_APPEND`模式打开的文件无效

whence参数取值范围如下：

* `0`:相对于文件头部
* `1`:相对于文件当前读取位置
* `2`:相对于文件末尾

如果offset所在的位置没有内容的话，创建的文件将会造成编码错误，导致无法打开

###### Stat

```go
func (f *File) Stat() (FileInfo, error)
```

返回用户描述结构的结构体`FileInfo`

###### Sync

```go
func (f *File) Sync() error
```

###### Truncate

```go
func (f *File) Truncate(size int64) error
```

截取文件内容大小到指定长度，但是并不改变当前指针的迁移量,如果发生错误，将会返回路径错误，如果截取的长度超过文件本身的大小，将会导致文件编码错误

##### 删除

##### 关闭

###### Close

```go
func (f *File) Close() error
```

关闭一个打开的文件

### 参考文档

* [Linux Files and File Permission](http://www.comptechdoc.org/os/linux/usersguide/linux_ugfilesp.html)



