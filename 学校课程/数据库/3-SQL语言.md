# 概述
集三种语言于一身：
- 数据定义语言（DDL）：`CREATE`、`ALTER`、`DROP`
- 数据操纵语言（DML）：`SELECT`、`INSERT`、`UPDATE`、`DELETE`
- 数据控制语言（DCL）：`GRANT`、`REVOKE`

<br><br>

# DDL

## 创建数据库
```sql
CREATE DATABASE <数据库名>;
```

## 创建表
```sql
CREATE TABLE <表名> (
    <列名1> <数据类型> [列级完整性约束条件],
    <列名2> <数据类型> [列级完整性约束条件],
    ...
    [表级完整性约束条件]
);
```

## 创建视图
```sql
CREATE VIEW <视图名> AS <查询语句>;
```

## 删除数据库
```sql
DROP DATABASE <数据库名>;
```

## 删除表
```sql
DROP TABLE <表名>;
```

## 删除视图
```sql
DROP VIEW <视图名>;
```

## 修改表
```sql
ALTER TABLE <表名>
[ADD <列名> <数据类型> [完整性约束条件] |
DROP <约束> |
MODIFY <列名> <数据类型> [完整性约束条件]];
```

## 指定数据库
```sql
USE <数据库名>;
```

## 关闭数据库
```sql
CLOSE <数据库名>;
```

<br><br>

# DML

## 插入数据
```sql
INSERT INTO <表名>[(列名1, 列名2, ...)]
VALUES (值1, 值2, ...);
```

## 查询数据
使用`SELECT`语句进行查询

### 基本语法
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1>, <表名2>, ...
[WHERE <条件>];
```

### 别名
```sql
SELECT <列名1> AS <别名1>, <列名2> AS <别名2>, ...
```

### WHERE
条件可以由多个子条件组成，子条件之间可以使用`AND`、`OR`进行连接，或是用`NOT`做否定修饰。还可以使用`IN`、`BETWEEN`、`LIKE`、`IS NULL`等关键字，其中`LIKE`的规则为：
- `%`：任意长度的任意字符
- `_`：任意单个字符
- `\`：转义字符

### 结果排序
```sql
ORDER BY <列名1> [ASC|DESC], <列名2> [ASC|DESC], ...
```

### 结果去重
```sql
SELECT DISTINCT <列名1>, <列名2>, ...
```

### $\theta$ 连接
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1>, <表名2>
WHERE <表名1>.<列名1> <比较符> <表名2>.<列名2>;
```

### 自然连接
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1>
NATURAL JOIN <表名2>;
```

### 内连接
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1> 
INNER JOIN <表名2>
ON <表名1>.<列名1> <比较符> <表名2>.<列名2>;
```


### 外连接
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1>
[LEFT|RIGHT|FULL] JOIN <表名2>
ON <表名1>.<列名1> <比较符> <表名2>.<列名2>;
```
也可以使用如下方式指定连接的属性，类似于自然连接，连接的属性组只出现一次（上同）：
```sql
USING (<列名1>, <列名2>, ...)
```


### 子查询
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1>
WHERE <列名1> <运算符> <修饰语> (SELECT <列名2> FROM <表名2> WHERE <条件>);
```
其中，运算符可以是：
- 比较运算符
- `IN`：在集合中
- `EXISTS`：子查询结果非空
- 等等
而修饰语可以是：
- `ALL`：所有
- `SOME`：至少一个

### 聚集函数
- `COUNT`：计数
- `SUM`：求和
- `AVG`：求平均值
- `MAX`：求最大值
- `MIN`：求最小值

### 分组
可以结合聚集函数使用
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名>
GROUP BY <列名1>, <列名2>, ...
[HAVING <条件>];
```

### 集合运算
```sql
SELECT <列名1>, <列名2>, ...
FROM <表名1>
[UNION|INTERSECT|EXCEPT]
SELECT <列名1>, <列名2>, ...
FROM <表名2>;
```

### 空值处理
空值不会满足除了`IS NULL`之外的任何条件。


## 更新数据
主要使用三种方式：
- **INSERT**：
    ```sql
    INSERT INTO <表名>[(列名1, 列名2, ...)]
    VALUES (值1, 值2, ...);
    ```
    VALUES也可以用子查询代替
- **UPDATE**：
    ```sql
    UPDATE <表名>
    SET <列名1> = <值1>, <列名2> = <值2>, ...
    [WHERE <条件>];
    ```
- **DELETE**：
    ```sql
    DELETE FROM <表名>
    [WHERE <条件>];
    ```

### 视图更新
以下情况不能进行视图的更新：
- 包含聚集函数生成的列
- 使用了`UNIQUE`、`DISTINCT`
- 使用了`GROUP BY`
- 包含算数表达式计算的列
- 没有表的主键