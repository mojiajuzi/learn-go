

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

#### 注释(Comments)
1. 行注释，以`//`开头，到行尾结束
1. 块注释, 已`/*`开头，　以`*/`结束

#### 符号(Tokens)
符号有以下四种类型：
- 标识符(identifiers)
- 关键字(keywords)
- 操作符(operators)
- 标点符号(punctuation)


