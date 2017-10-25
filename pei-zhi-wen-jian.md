### 配置文件读取

#### ioutil包

##### ReadAll

```go
func ReadAll(r io.Reader)([]byte, error)
```

从`r`中读取数据直到文件末尾，并返回读取到的数据，如果读取成功，则`err == nil`，否则返回`err == EOF`



##### ReadDir

```go
func ReadDir(dirname string)([]os.FileInfo, error)
```

读取指定目录下的文件和目录，并返回文件和目录的列表



##### ReadFile

```go
func ReadFile(filename string)([]byte, error)
```

读取指定文件的全部内容

##### TempDir

```go
func TempDir(dir, prefix string)(name string, err error)
```

在指定的`dir`目录下以`prefix`为前缀加上一串随机字符串生成一个目录，如果`dir`为空，则会在默认的临时目录中创建新的目录





#### json

```go
	f, err := ioutil.ReadFile("data.json")
	if err != nil {
		fmt.Println("load config gile error")
	}
	var config map[string]map[string]interface{}
	err = json.Unmarshal(f, &config)
	if err != nil {
		fmt.Println(err.Error())
	}
	fmt.Println(config["default"]["between"])
```

