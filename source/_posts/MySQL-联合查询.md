---
title: 'MySQL:联合查询'
tags:
  - MySQL
  - 查询
abbrlink: 2080b578
date: 2022-03-12 15:29:29
---

学习笔记：介绍MySQL中联合查询的基本使用。

<!--more-->

### 1. 语法

```
union 联合合并：将多条查询语句的结果合并成一个结果
    语法：
        查询语句一
        union
        查询语句二
        union
        查询语句三
        ……
    应用场景:
        要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时
    特点:
        1、要求多条查询语句的查询列数是一致的!
        2、要求多条查询语句的查询的每一列的类型和顺序最好一致
        3、union关键字默认去重，如果使用union all 可以包含重复项
```

### 2. 用例


```sql 
SELECT *
FROM employees
WHERE email LIKE '%a%'
OR last_name LIKE '%a%';
/****等价于***/
SELECT *
FROM employees
WHERE email LIKE '%a%'
UNION
SELECT *
FROM employees
WHERE last_name LIKE '%a%';
```

```sql
-- 查询girl中的所有人名
SELECT  `name`
FROM beauty
UNION
SELECT boyName
FROM boys;
```

### 3. 查询总结

```sql
SELECT 查询列表
FROM 表名 别名
连接类型（left|right|inner）JOIN 表名 别名
ON 连接条件
WHERE 筛选条件
GROUP BY 分组列表
HAVING 筛选条件
ORDER BY 排序列表 DESC|ASC
LIMIT 起始条目索引，条目数
```

