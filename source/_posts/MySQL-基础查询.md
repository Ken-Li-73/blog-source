---
title: 'MySQL:基础查询'
abbrlink: 47455d3a
date: 2022-03-12 14:56:07
tags:
---

学习笔记：介绍MySQL中基础查询语法。

<!--more-->

**语法:**

```mysql
select  查询列表 
from 表名;
```

类似于: system.out.println (打印东西);
特点:
1、查询列表可以是:表中的字段、常量值、表达式、函数
2、查询的结果是一个虚拟的表格

### 1.1 查询表中的单个字段

```sql
USE employees; -- 进入数据库
SELECT last_name FROM employees;
```

### 1.2 查询表中的多个字段

```mysql
SELECT 
	last_name , 
	salary,
	 email 
FROM 
	employees;

SELECT 
	`last_name`,/***着重号，可以用来区分关键字和字段***/
	`salary`,
	`email`
FROM 
	employees;
```

### 1.3 查询表中的所有字段

```mysql
SELECT * FROM employees;
```

### 1.4 查询常量值

```mysql
SELECT 100;
SELECT 'John';
```

### 1.5 查询表达式

```mysql
SELECT 100%94;
```

### 1.6 查询函数

```mysql
SELECT VERSION(); #查询版本
```

### 1.7 起别名

①便于理解
②如果要查询的字段有重名的情况，使用别名可以区分开来

```mysql
#方式一:使用as
SELECT 100%98 AS 结果;
SELECT last_name AS 姓, first_name AS 名 FROM employees;

#方式二:使用空格
SELECT last_name 姓 , first_name 名 FROM employees;

-- 例子
SELECT salary AS "out put" FROM employees;
-- 如果别名中含有空格，井号，关键字等特殊字符，需要用单引号或者双引号括起来。
```

### 1.8.去重

```mysql
-- 例子:获取所有部门的编号
SELECT DISTINCT department_id FROM employee;；
```

### 1.9.mysql中的+号

```mysql
SELECT 100+90;     -- 两个操作数都为数值型，则做加法运算
SELECT '123'+90;   -- 只要其中一方为字符型，试图将字符型数值转换成数值型
                   -- 如果转换成功,则继续做加法运算
SELECT 'john'+90;  -- 如果转换失败，则将字符型数值转换成0
SELECT NULL+10;    -- 只要其中一方为null，则结果肯定为null
	               
```

### 1.10 拼接

```mysql
SELECT CONCAT(last_name,first_name) FROM employees;
#在拼接过程中，如果出现null，则返回的值就是null,可以事现进行判断
```

### 1.11 IFNULL函数

```mysql
IFNULL(字段，替代值); -- 如果是null返回替代值，否则返回原来值
```

### 1.12 ISNULL函数

```mysql
ISNULL(字段); -- 如果是null返回0，否则返回1.
```
