---
title: 'MySQL:条件查询'
tags:
  - MySQL
  - 查询
abbrlink: a8bccd5a
date: 2022-03-12 14:57:47
---

学习笔记：介绍MySQL中条件查询的基本使用。

<!--more-->

### 1 .语法

```mysql
select
	查询列表
from
	表名
where
	筛选条件;		
```

### 2 .表达式分类

```mysql
一、按条件表达式筛选
	条件运算符:> <= != <>>=<=

二、按逻辑表达式筛选（用于连接条件表达式）
	逻辑运算符:  &&  ||  !
			   and or not

三、模糊查询
	like
	between and 
	in
	is null
```

#### 2.1 按条件表达式筛选

```mysql
-- 查询工资>12000的员工信息
SELECT
	*
FROM
	employees
WHERE
	salary>12000;
```

```mysql
-- 查询部门编号不为90的员工名和部门信息	
SELECT
	,`last_name`,`department_id`
FROM
	employees
WHERE
	department_id <>90;
```

#### 2.2 逻辑查询

```mysql
-- 查询工资在10000到20000之间的9员工名、工资和奖金信息
SELECT
	last_name,
	salary,
	commission_pct
FROM
	employees
WHERE
	salary>10000 AND salary<20000;
```

```mysql
-- 查询部门编号不在90和110之间或者工资大于15000的员工信息	
SELECT 
	*
FROM 
	employees
WHERE
	NOT(department_id>90 AND department_id<110) OR salary>15000;
```

#### 2.3 模糊查询

```mysql
-- 查询名字中含'a'的员工信息
SELECT
	*
FROM 
	employees
WHERE 
	last_name LIKE '%a%'; -- %是通配符，表示任意个任意字符
```

```mysql
	-- 查询名字中第三个含n第五个含l的员工名和工资
	SELECT
		last_name
		salary
	FROM 
		employees
	WHERE 
		last_name LIKE '__n_l%';-- _表示单个任意字符
```

```mysql
--查询名字中含有特殊通配符的员工名
SELECT
	last_name
FROM 
	employees
WHERE 
	last_name LIKE '%$_%' ESCAPE '$'; #ESCAPE 是指转义字符
```

```mysql
-- 查询员工编号在100和120之间的员工信息（包含临界值）
SELECT
	*
FROM 
	employees
WHERE
	employee_id BETWEEN 100 AND 120 ;
	-- 完全等价于 employee_id >= 100 AND employee_id <=120 ;
```

```mysql
-- 查询工种符合IT_PROT、AD_VP、AD_PRES的一种的员工信息
SELECT 
	last_name
FROM 
	employees
WHERE
	job_id IN ('IT_PROT','AD_VP','AD_PRES');
	/*完全等价于 job_id = 'IT_PROT' OR job_id = 'AD_VP' OR job_id = 'AD_PRES';
```

```mysql
-- 查询没有奖金的员工名和奖金率
SELECT 
	last_name,
	commission_pct
FROM 
	employees
WHERE
	commission_pct IS NULL;

```

```mysql
-- 查询有奖金的员工名和奖金率
SELECT 
	last_name,
	commission_pct
FROM 
	employees
WHERE
	commission_pct IS NOT NULL;
```

特殊：安全等于 <=> （相当于 = 和 IS 的功能) 

```mysql
IS NULL 和 <=>区别
IS NULL:仅仅可以判断 NULL值,可读性较高，建议使用
<=>:既可以判断 NULL值，又可以判断普通的数值，可读性较低
```

## 
