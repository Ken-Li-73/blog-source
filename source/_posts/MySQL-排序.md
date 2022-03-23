---
title: 'MySQL:排序'
tags:
  - Mysql
  - 排序
abbrlink: db4c4c
date: 2022-03-12 14:58:27
---

学习笔记：介绍MySQL中排序操作。

<!--more-->

### 	1语法格式

```mysql
SELECT 查询列表             	③
FROM 表                     	①
【 WHERE 语句 】                ②
ORDER BY 排序列表 【 asc|desc 】 ④
```

### 	2.特点

​	1.asc代表的是升序,desc代表的是降序如果不写，默认是升序
​	2.order by子句中可以支持单个字段、多个字段、表达式、函数、别名
​	3. order by子句一般是放在查询语句的最后面，limit子句除外

### 3.举例


```mysql
-- 查询员工信息，要求工资从高到低排序 
SELECT
	*
FROM
	employees
ORDER BY salary DESC; 
```

```mysql
-- 按入职时间显示员工信息
SELECT	
	*
FROM 
	employees
WHERE 
	department_id>=90
ORDER BY hiredate ASC;
```

```mysql
-- 按年薪的高低显示员工信息和年薪
SELECT	*,salary*12*(1+IFNULL(commission_pct,0))AS 年薪
FROM employees
ORDER BY 年薪 DESC;
```

```mysql
-- 按姓名的长度进行排序显示员工名和工资
SELECT  last_name,salary
FROM employees
ORDER BY LENGTH(last_name) DESC;
```

```mysql
-- 按工资升序，再按员工编号降序进行排序显示员工信息
SELECT  *
FROM employees
ORDER BY salary ASC, employee_id DESC;
```
