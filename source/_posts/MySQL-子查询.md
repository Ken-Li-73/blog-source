---
title: 'MySQL:子查询'
tags:
  - MySQL
  - 查询
abbrlink: e46b90c0
date: 2022-03-12 15:28:57
---
学习笔记：介绍MySQL中子查询的基本使用。

<!--more-->

    含义: 
        出现在其他语句中的select语句，称为子查询或内查询 
        外部的查询语句，称为主查询或外查询   
    分类: 
        按子查询出现的位置:  
            select后面:   
                仅仅支持标量子查询   
            from后面: 
                支持表子查询  
            where或having后面:★    
                标量子查询(单行）   
                列子查询(多行）√   
                行子查询    
            exists后面(相关子查询) 
                表子查询    
        按结果集的行列数不同: 
            标量子查询(结果集只有一行一列)    
            列子查询(结果集只有一列多行） 
            行子查询(结果集有一行多列或多行多列)  
            表子查询(结果集一般为多行多列)    

### 1. 放在WHERE或者HAVING后面

#### 1.1. 标量子查询(一行一列）

操作符有：> < = <>

```sql
-- 谁的工资比Abel高
SELECT last_name
FROM employees
WHERE 
    salary>(
    SELECT salary
    FROM employees
    WHERE last_name = 'Abel');
```

```sql
-- 返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id 和工资
SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
    SELECT job_id
    FROM employees
    WHERE  employee_id = 141)
AND salary >(
    SELECT salary
    FROM employees
    WHERE employee_id = 143);
```

```sql
-- 查询最低工资大于50号部门最低工资的部门id和其最低工资
SELECT department_id ,MIN(salary)
FROM employees
GROUP BY department_id 
HAVING MIN(salary)>(
    SELECT MIN(salary)
    FROM employees
    WHERE department_id = 50
    );
```

#### 1.2. 列子查询

操作符有：   

> IN/NOT IN  等于列表中的任意一个     
> 例： a IN (10,20,30)      相当于 a = 10 OR a = 20 OR a = 30           
> ANY|SOME  和子查询返回的的某一个值比较  
> 例： a > ANY (10,20,30)   相当于 a> MIN(10,20,30)    
> 例： a < ANY (10,20,30)    相当于 a< MAX(10,20,30)     
> ALL 和子查询返回的所有值比较      
> 例： a > ALL (10,20,30)    相当于 a> MAX(10,20,30)     
> 例： a < ALL (10,20,30)    相当于 a< MIN(10,20,30)   

```sql
SELECT last_name
FROM employees e
LEFT JOIN departments d
ON d.department_id = e.department_id
WHERE location_id IN (1400,1700);

SELECT last_name
FROM employees 
WHERE department_id IN(
    SELECT department_id
    FROM departments
    WHERE location_id IN (1400,1700)
    );
```

```sql
-- 返回其它部门中比job_id为'IT_PROG'部门任一工资低的员工的员工号、姓名、job_id 以及salary
SELECT employee_id,last_name,job_id ,salary
FROM employees
WHERE salary < ANY (
    SELECT DISTINCT salary
    FROM employees
    WHERE job_id = 'IT_PROG'
)AND job_id <>'IT_PROG';;
```

#### 1.3. 行子查询

```sql
-- 查询员工编号最小并且工资最高的员工信息
SELECT *
FROM employees
WHERE (empioyee_id, salarly) = (
    SELECT MIN (employee_id),MAX(salary)
    FROM employees
);
```

### 2. 放在SELECT后面

```sql
-- 查询每个部门的员工个数
 /***计算每个部门的人数，没有则返回0***/
SELECT d.* ,
   
    SELECT COUNT(*)
    FROM employees e
    WHERE d.department_id = e.department_id -- 内层调用外层
FROM departments d;

/***通过外连，记录每个部门的人数***/
SELECT d.* ,COUNT(last_name)
FROM employees e
RIGHT JOIN departments d
ON d.`department_id` = e.`department_id`
GROUP BY d.department_id;
```

```sql
-- 查询员工号=102的部门名
SELECT `employee_id` 员工号,(
    SELECT `department_name`
    FROM `departments` d
    WHERE d.`department_id`= e.`department_id`
) 部门名
FROM `employees` e
WHERE e.`employee_id` = 102;
```

### 3. 放在FORM后面

<font color = 'red'>将子查询结果作为一张表，要求必须起别名</font>

```sql
-- 查询每个都门的平均工资的工资等级
ELECT department_id,grade_level
FROM (
    SELECT department_id,AVG(salary) avg_salary
    FROM employees
    GROUP BY department_id
) d
LEFT JOIN job_grades j
ON avg_salary BETWEEN lowest_sal AND highest_sal;
```

### 4. 放在exists后面(相关子查询)

    语法：
    exists(完整的查询语句)
    结果：1或0

```sql
-- 没有女神的男神名
SELECT boyName
FROM boys bo
WHERE NOT EXISTS(
    SELECT *
    FROM beauty be
    WHERE be.boyfriend_id = bo.id
    );
    
SELECT boyName
FROM boys 
WHERE id NOT IN (
    SELECT DISTINCT boyfriend_id
    FROM beauty 
);
```

```sql
-- 1.查询Zlotkey的部门
SELECT  department_name
FROM  departments
WHERE department_id = (SELECT department_id
    FROM employees
    WHERE last_name = 'Zlotkey'
);
```

```sql  
-- 2.查询工资比公司平均工资高的员工的员工号，姓名和工资。
SELECT `employee_id`,`last_name`,`salary`
FROM `employees`
WHERE
    salary>(
    SELECT AVG(salary)
    FROM employees
);
```

```sql    
-- 3.查询各部门中工资比本部门平均工资高的员工的员工号，姓名和工资
SELECT `employee_id`,`last_name`,`salary`
FROM `employees` e
INNER JOIN (
    SELECT AVG(salary) avg_salary,department_id
    FROM employees
    GROUP BY department_id
    ) s
ON e.`department_id` = s.department_id
WHERE e.`salary` > s.avg_salary;
```

```sql
-- 4.查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名
SELECT `employee_id`,`last_name`
FROM employees
WHERE department_id IN (
    SELECT DISTINCT `department_id`
    FROM employees
    WHERE last_name LIKE '%u%')
AND last_name NOT LIKE '%u%';
```

```sql
-- 5.查询在部门的location_id为1700的部门工作的员工的员工号
SELECT `employee_id`,`last_name`
FROM employees
WHERE department_id IN (
    SELECT DISTINCT `department_id`
    FROM `departments`
    WHERE location_id = 1700
);
```

```sql   
-- 6.查询管理者是K_ing的员工姓名和工资
SELECT last_name,salary,manager_id
FROM employees 
WHERE manager_id IN (
    SELECT employee_id
    FROM employees 
    WHERE last_name = 'K_ing');
```

```sql
-- 7.查询工资最高的员工的姓名，要求first_name和last_name显示为一列，列名为姓.名
SELECT CONCAT(first_name,'.',last_name) '姓.名' ,salary
FROM employees 
WHERE salary = (
    SELECT MAX(DISTINCT salary)
    FROM employees 
);
```

