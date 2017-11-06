### path/filepath

> 该包实现了操作文件路径的实用方方法



#### 常量

```go
const (
  Separator = os.PathSeparator //操作系统路径分隔符，"/"
  ListSeparator = os.PathListSeparator //操作系统路径列表分隔符 ":"
)
```

#### 变量

1. 定义一个全局的路径错误信息

   ```go
   var ErrBadPattern = errors.New("syntax error in pattern")
   ```

2. 从`WalkFuncs`函数返回一个值表明目录被调用的时候被跳过了

   ```go
   var SkipDir = errors.New("skip this directory")
   ```

#### 函数

##### Abs

```go
func Abs(path string) (string,error)
```

返回给定路径的绝对路径，如果给定的`path`不存在，那么将会将该字符串作为当前路径的子目录进行处理



##### Base

```go
func Base(path string) string
```

返回路径元素的最后一个元素，

- 如果最后一个元素带有分隔符，分隔符将会被直接忽略，
- 给定的`path`为空，将会返回`.`
- 如果给定的`path`为分隔符，那么将会直接返回分隔符

##### Clean

```go
func Clean (path string) string
```

对路径进行过滤处理，其应用如下的规则

- 将多个连续的分隔符变成一个分隔符
- 直接去除路径中的`.`符号
- 去除路径中的`..`符号以及该符号前两个分隔符之间的字符

##### Dir

```go
func Dir(path string) string
```

去除最后一个分隔符及其之后的字符，

- 如果给定的路径为空，那么返回`.` 
- 如果给定的路径只有一个分隔符，那么则返回该分隔符

##### EvalSymlinks

```go
func EvalSymlinks(path string) (string, error)
```

返回过滤掉软链接之后的路径名称，如果给定的是一个相对路径，那么将会返回一个相对于当前目录的相对路径



##### Ext

```go
func Ext (path string) string
```

返回给定路径的文件的扩展名称，返回的标识符为以`.`进行分割，如果没有找到后缀，将会返回空

##### FromSlash

```go
func FromSlash(path string) string
```

使用分隔符替换二进制的分割符，多个将直接替换成多个

##### Match

```go
func Match(pattern, name string) (matched bool, err error)
```

给定的`name`是否和shell 文件的模式是否匹配，其语法如下:

```shell
pattern:
	{term}
term:
	'*' 匹配所有非分割字符
	'?' 匹配单个字符
	'[' ['^'] { 字符区间　}　']' 非空字符组
	c 匹配字符　c (c 不能为：　'*', '?','\\','[')
	'\\' c 匹配字符c
	
character-range:
	c 匹配字符c (c　不能为: '\\', '-', ']')
	'\\' c 匹配字符c
	lo '-' hi 匹配 lo <= c <= hi
```



##### Glob

```go
func Glob(pattern string)(matches []string, err error)
```

返回与匹配符所匹配的所有文件列表，匹配规则与`Match`函数一样，如果没有匹配，则返回空,

##### HasPrefix

```go
func HasPrefix(p, prefix string) bool
```

查看给定的地址字符串中是否包含指定的前缀

##### IsAbs

```go
func IsAbs(path string) bool
```

给定的地址是否是绝对地址

##### Join

```go
func Join(elem ...string) string
```

使用分隔符拼接给定的字符,如果给定的字符已经有分隔符，则无需添加，返回拼接后的完整字符串

##### Rel

```go
func Rel(basepath, targpath string) (string, error)
```

返回一个相对地址，将返回的路径用分隔符与`basepath`连接，等价于`targpath`,也就是说`Jion(basepath, targpath)`等价于`targpath`,如果成功，返回值总是相对于`basepath`的，也就是说`targpath`和`basepath`没有共享路径元素，如果两个参数，一个是绝对路径而另外一个是相对路径，或者`targpath`无法标识为相对于`basepath`的路径，将返回错误

##### Split

```go
func Split(path string)(dir, file string)
```

以最后一个分隔符作为基准，将给定的路径拆分为目录和文件两个部分，如果没有分隔符，那么将会返回一个空的目录，并将给定的路径作为文件名称，返回的结果总是符合如下`path=dir+file`

##### SplitList

```go
func SplitLIst(path string)[]string
```

依赖于系统列表分隔符(在Linux系统中为`:`)分割给定的路径，如果给定的路径为空，将会返回一个空的切片



##### ToSlash

```go
func ToSlash(path string) string
```

使用`/`符号，替换给定路径中的分隔符，如果给定的路径中包含多个连续的分隔符，那么将使用多个`/`替代

##### VolumeName

```go
func VolumeName(path string) string
```

返回给定路径的盘符号，比如给定`C:\foo\bar`在Windows系统中将会返回`C:`, 给定`\\host\share`将会返回`\\host\share`，在其他平台将会返回空字符串



##### Walk

```go
func Walk(root string, walkFn WalkFunc) error
```

通过调用给定的`walkFn`函数，遍历给定的`root`下的目录树(包括`root`)，　遍历过程中出现的任何错误，都将会被`walkFn`函数过滤掉，并且按照文件名进行排序，这使得输出的结果是可确定的，对于过深的目录效率会比较低。该方法对于软链接是无法进行遍历的



####WalkFunc

```go
type WalkFunc func(path string, info os.FileINfo, err error) error
```

该类型函数是`Walk`函数遍历时调用的，`path`参数会包含`Walk`函数的`root`参数作为前缀，比如说：`Walk`函数

调用时给定的`root`参数的值是`dir`，`dir`目录下面包含一个文件`a`,那么`Walk`函数调用的时候的参数值就是`dir/a`

如果遍历 `path` 指定的文件或目录时出现了问题，传入的参数 `err` 会描述该问题，`WalkFunc` 类型函数可以决定如何去处理该错误（`Walk` 函数将不会深入该目录）；如果该函数返回一个错误，`Walk`函数的执行会中止；只有一个例外，如果`Walk`的`walkFn` 返回值是 `SkipDir`，将会跳过该目录的内容而 `Walk`函数照常执行处理下一个文件。

如下，遍历指定目录的文件及其子文件
```go
func main() {
	p := "/home/gru/go/src/gru"
	err := filepath.Walk(p, func(path string, info os.FileInfo, err error) error {
		if !info.IsDir() {
			fmt.Println(info.Name())
		}
		return nil
	})
	fmt.Println(err)
}
```