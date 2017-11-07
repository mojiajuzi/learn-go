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


### 目录的相关操作

#### Chdir
```go
func Chdir(dir string) error
```
将当前的工作目录变更到给定的目录中，如果失败，将会返回无法找到路径的问题

#### Chmod
```go
func Chmod(name string, mode FileMode) error
```
变更给定文件的模式，如果给定的`name`是一个软链接，将会变更该链接所指向的文件的模式,如果失败，将会返回路径错误

#### Chown
```go
func Chown(name string,　uid, gid int) error
```
变更文件的归属，同时变更文件的用户以及用户组,如果是软链接，则直接作用于其目标文件

#### Chtimes
```go
//atime: access time  mtime:modification time
func Chtimes(name string, atime, mtime time.Time) error
```
变更文件的访问时间和修改时间，类似于Unix中的`utime, utimes`两个函数

#### Executable
```go
func Executable()(string, error)
```
执行文件并返回当前所执行文件的绝对路径，对于返回的路径并不能保证一定指向执行的文件，如果使用的是符号链接启动文件的话
对于不同的操作系统，其返回的结果是不一样的，可能返回符号链接或者指向符号链接的路径，如果想要获取精确的返回值，需要使用
`filepaht`包的`EvalSymlinks`

#### Mkdir
```go
func Mkdir(name string, per FileMode) error
```
创建一个目录,如果目录存在，则会创建失败

#### ＭkdirAll
```go
func MkdirAll(path string, perm FileMode) error
```
循环创建多层次的目录，并且所创建的目录都应用相同的权限，如果目录存在，则不执行操作

#### Remove
```go
func Remove(name string) error
```
删除给定的文件或者目录，文件不存在，文件夹不为空，则会删除失败

#### RemoveAll
```go
func RemoveAll(path string) error
```
移除给定的文件，包括文件下面包含的子文件，如果遇到文件错误，该函数会继续执行之后的文件，直到所有的文件都执行过一次
并且返回遇到的第一个文件错误，如果给定的文件不存在，那么将会返回nil,而不是错误

#### Rename
```go
func Rename(oldpath, newpath string) error
```
重命名/移动文件，如果给定的`newpath`已经存在，并且不是一个目录，那么将会被替换
对于有些操作系统而言，如果`oldpath`和`newpath`不在同一个目录里面，将会被禁止操作

#### SameFile
```go
func SameFile(f1,f2 FileInfo) bool
```
检查两个文件的描述符是否一样

### 参考文档

* [Linux Files and File Permission](http://www.comptechdoc.org/os/linux/usersguide/linux_ugfilesp.html)



