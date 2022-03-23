---
title: 'MySQL:分组查询'
tags:
  - MySQL
  - 查询
abbrlink: ab73e734
date: 2022-03-12 14:58:54
---

学习笔记：介绍MySQL中分组查询的基本使用。

<!--more-->

### 1.语法

```sql
    select 分组函数 【,列(要求出现在 group by的后面)】
    from 表
    【 where 筛选条件 】
    group by 分组列表
    【 order by子句 】
```

```sql
-- 查询每个部门的最高工资（以“部门号”分出的每组，分别进行“工资求最高”操作）
SELECT MAX(salary),department_id 部门
FROM employees
GROUP BY department_id ;
```

```sql
-- 查询每个位置上的部门个数
SELECT COUNT(*) , location_id
FROM departments
GROUP BY location_id;
```

```sql
-- 查询邮箱中包含a字符的，每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
WHERE email LIKE ('%a%') 
GROUP BY department_id;
```

```sql
-- 查询有奖金的每个领导手下员工的最高工资
SELECT MAX(salary),manager_id
FROM employees
WHERE salary IS NOT NULL
GROUP BY manager_id;
```

```sql
-- 查询哪个都门的员工个数>2
SELECT COUNT(*) 员工个数,department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*)>2;
```

```sql
-- 查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
SELECT job_id,MAX(salary)
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary)>12000;
```

```sql
-- 查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个，以及其最低工资
SELECT manager_id,MIN(salary)
FROM employees
WHERE manager_id>102
GROUP BY manager_id
HAVING MIN(salary)>5000;
```

### 2.分组前后的筛选条件对比

|            |     数据源     |        位置        | 关键字 |
| :--------: | :------------: | :----------------: | :----: |
| 分组前筛选 |     原始表     | group by子句的前面 | where  |
| 分组后筛选 | 分组后的结果集 | group by子句的后面 | having |

**1.分组函数做条件肯定是放在having子句中**

**2.尽量分组前筛选**
