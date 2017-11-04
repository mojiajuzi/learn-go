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

