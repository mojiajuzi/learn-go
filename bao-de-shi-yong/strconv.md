#### strconv

> 该包实现了字符串与其他基础类型之间的相互装换

##### 数字类型转换

1. 最常用的类型装换是`Atoi`（字符转数字）和`Itoa`(数字转字符串),这两种装换是按照Go中的整型以及十进制

   ```go
   i, err := strconv.Atoi("42")
   s := strconv.Itoa(-42)
   ```

2. 将字符串转换成某种特定类型的值,`Parse`系列函数将会返回较大类型的结果(`float64`, `int64`, `uint64`),而且并没有数据的缺失

   ```go
   b, err := strconv.ParseBool("true")
   f, err := strconv.ParseFloat("3.1245", 64)
   i, err := strconv.ParseInt("-42", 10, 64)
   u, err := strconv.ParseUint("42", 10, 64)
   ```

3. 将特定的值转换为字符串

   ```go
   s := strconv.FormatBool(true)
   s := strconv.FormatFloat(3.1415, 'E', -1, 64)
   s := strconv.FormatInt(-42, 16)
   s := strconv.FormatUint(42, 16)
   ```



##### 字符串转换