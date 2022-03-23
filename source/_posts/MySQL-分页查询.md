---
title: 'MySQL:分页查询'
tags:
  - MySQL
  - 查询
abbrlink: 80debfa3
date: 2022-03-12 15:29:15
---

学习笔记：介绍MySQL中分页查询的基本使用。

<!--more-->
应用场景:当要显示的数据，一页显示不全，需要分页提交sql请求

```mysql
语法:
     select 查询列表
     from 表
     【[JOIN TYPE] join 表2
     on 连接条件
     WHERE 筛选条件 
     group by 分组字段 
     having 分组后的筛选
     order by 排序的字段】
     limit offset, size;
    /***offset 要显示条目的起始索引(起始索引从0开始)***/
特点:
     limit 语句放在查询语句的最后
公式:
     select 查询列表
     from 表
     limit (page-1)*size,size;
     /***要显示的页数page，每页的条目数size***/
```


```sql
-- 查询前五条员工信息
SELECT *
FROM employees
LIMIT 0,5  ;/***从第0个开始，一共显示5条***/
```

```sql
-- 查询第11条到第25条员工信息
SELECT *
FROM employees
LIMIT 10,15;
```

```sql
-- 有奖金的员工信息，并且工资较高的前10名显示出来
SELECT *
FROM employees
WHERE commission_pct IS NOT NULL
ORDER BY salary DESC
LIMIT 10;
```

```sql
-- 1.查询工资最低的员工信息:1ast_name,salary
SELECT last_name,salary
FROM employees
WHERE salary = (
    SELECT MIN(salary)
    FROM employees
);
```

```sql
-- 2-查询平均工资最低的部门信息
SELECT *
FROM `departments`
WHERE department_id = (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    ORDER BY AVG(salary) ASC
    LIMIT 1
);
```

```sql
-- 3．查询平均工资最低的部门信息和该部门的平均工资
SELECT *,avg_data.avg_salary
FROM `departments` d
INNER JOIN (
    SELECT department_id,AVG(salary) avg_salary
    FROM employees
    GROUP BY department_id
    ORDER BY AVG(salary) ASC
    LIMIT 1) avg_data
ON d.department_id = avg_data.department_id;
```

```sql
-- 4.查询平均工资最高的jobs信息
SELECT *
FROM jobs
WHERE job_id = (
    SELECT job_id 
    FROM employees
    GROUP BY job_id 
    ORDER BY AVG(salary) DESC
    LIMIT 1
);
```

```sql
-- 5．查询平均工资高于公司平均工资的部门有哪些?
SELECT department_name
FROM departments d
INNER JOIN (
    SELECT AVG(salary) avg_salary,department_id
    FROM employees
    GROUP BY department_id
    HAVING avg_salary>(
        SELECT AVG(salary)
        FROM employees)
    ) em_sa
ON em_sa.department_id  = d.department_id;
```

```sql
-- 6。查询出公司中所有manager的详细信息-
SELECT *
FROM employees
WHERE employee_id IN (
    SELECT DISTINCT manager_id
    FROM employees
    WHERE manager_id IS NOT NULL
);
```

```sql
-- 7．各个部门中最高工资中最低的那个部门的最低工资是多少
SELECT MIN(salary)
FROM employees
WHERE department_id = (
    SELECT department_id
    FROM employees
    GROUP BY department_id 
    ORDER BY MAX(salary)
    LIMIT 1
);
```

```sql  
-- 8，查询平均工资最高的部门的manager的详细信息: last_name，department_id, email, salary
SELECT last_name,e.department_id, email, salary
FROM employees e
INNER JOIN departments d
ON e.employee_id = d.manager_id
WHERE d.department_id = (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    ORDER BY AVG(salary) DESC
    LIMIT 1
);
```

