

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
    
`Productions`指定了
