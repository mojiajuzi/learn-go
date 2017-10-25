

## 简介
----

### 符号
以下语法指定了使用EBNF用途

    ```
    Production = production_name "=" [Expression] ".".
    Expression = Alternative {"|" Alternative}.
    Alternative = Term {Term} .
    Term = production_name | token ["..." token] | Group | Option | Repetion .
    Group = "("Expression")".
    Option = "[" Expression "]" .
    Repetition = "{" Expression "}" .
    ```
    
### 源码
源码使用UTF-8编码


### 词法

#### 注释
1. 行注释，以`//`开头，到行尾结束

1. 块注释, 已`/*`开头，　以`*/`结束