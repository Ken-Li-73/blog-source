---
title: 'MySQL:连接查询'
tags:
  - MySQL
  - 查询
abbrlink: b368022b
date: 2022-03-12 14:59:09
---

学习笔记：介绍MySQL中连接查询的基本使用。

<!--more-->

连接查询又称多表查询，当查询的字段来自于多个表时，就会用到连接查询。

```sql
    笛卡尔乘积现象:表1有m行，表2有n行，结果=m*n行
    发生原因:没有有效的连接条件
    如何避免:添加有效的连按条件
    
    MySQL分类:
        按年代分类:
            sq192标准：仅仅支持内连接
            sql99标准 【推荐】 支持内连接+外连接（左外连接和右外连接）+交叉连接
        按功能分类:
            内连接:
                等值连接
                非等值连接
                自连接
            外连接:
                左外连接
                右外连接
                全外连接
            交叉连接
```

```mysql
-- 笛卡尔乘积现象
SELECT COUNT(*) FROM beauty;
-- 假设输出12行
SELECT COUNT(*)FROM boys;
-- 假设输出4行
SELECT `name`,boyName 
FROM beauty,boys;
-- 最终结果12*4=48行
-- 发生原因：没有有效的连接条件

-- 如何避免： 添加有效的连接条件
SELECT `name`,boyName 
FROM beauty,boys
WHERE beauty.boyfriend_id = boys.id ;
```

### 1.sql92标准

#### 1.1.等值连接

①多表等值连接的结果为多表的交集部分  
②n表连接，至少需要n-1个连接条件  
③多表的顺序没有要求  
④一般需要为表起别名  
⑤可以搭配前面介绍的所有子句使用，比如排序、分组、筛选 

```mysql
-- 查询员工名和对应的部门名
SELECT `last_name`,`department_name`
FROM `employees`,`departments`
WHERE `employees`.`department_id` =`departments`.`department_id` ;
```

```mysql
-- 为表起别名
SELECT `last_name`,e.`job_id`,`job_title`
FROM `employees` e,`jobs` j
WHERE e.`job_id` =j.`job_id` ;
-- 注意:如果为表起了别名，则查询的字段就不能使用原来的表名去限定
```

```mysql
-- 查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT `department_name`,d.`manager_id`,MIN(`salary`)
FROM `departments` d,`employees` e
WHERE e.`commission_pct` IS NOT NULL
AND d.`department_id` = e.`department_id`
GROUP BY e.`department_id`,`department_name`;-- 若一一对应可以不加
```

```mysql
-- 查询每个工种的工种名和员工的个数，并且按员工个数降序
SELECT job_title,COUNT(*) 个数
FROM jobs j,employees e
WHERE j.job_id = e.job_id
GROUP BY job_title
ORDER BY 个数 DESC;
```

```mysql
-- 实现三表连接
-- 查询员工名、部门名和所在的城市
SELECT last_name,department_name,city
FROM departments d,employees e,locations l
WHERE d.`department_id` = e.`department_id`
AND l.`location_id` = d.`location_id`;
```

#### 6.1.2.非等值连接

```mysql
-- 查询工资和级别
SELECT salary,`grade_level`
FROM `job_grades`,`employees`
WHERE salary BETWEEN  `lowest_sal`AND `highest_sal`;
```

#### 6.1.3.子连接

```mysql
-- 查询员工名和上级的名称
SELECT e.last_name 员工名,m.last_name 上级名
FROM employees e,employees m
WHERE e.manager_id = m.employee_id
AND e.manager_id IS NOT NULL;
```


### 2.sql99标准

```sql
--  语法:    
    SELECT 查询列表    
    FROM 表1 别名     
    【连接类型】 JOIN 表2 别名    ON 连接条件    
    【WHERE筛选条件】    
    【GROUP BY分组】    
    【HAVING筛选条件】    
    【ORDER BY排序列表】
-- 分类:    
	内连接(★) : INNER    
	外连接        
		左外(★) : LEFT 【 OUTER 】 
    	右外(★) :RIGHT 【 OUTER 】        
    	全外: FULL 【 OUTER 】    
    	交叉连接: CROSS
```

#### 2.1.内连接

##### 2.1.1.等值连接

```sql
-- 语法:    
SELECT 查询列表    
FROM 表1 别名     
INNER JOIN 表2 别名  ON 连接条件;
```

```sql
-- 1.查询员工名、部门名(调换位置)
SELECT `last_name`,`department_name`
FROM employees eINNER 
JOIN `departments` d ON e.`department_id` = d.`department_id`;
```

```sql
-- 2.查询名字中包含e的员工名和工种名(筛选>
SELECT last_name,`job_title`
FROM `employees` e 
INNER JOIN `jobs` j ON e.`job_id` = j.`job_id`
WHERE last_name LIKE '%e%';
```

```sql
-- 3．查询部门个数>3的城市名和部门个数，(分组+筛选）
SELECT COUNT(*),`city`
FROM `departments` d 
INNER JOIN `locations` l ON d.`location_id`=l.`location_id`
GROUP BY `city`
HAVING COUNT(*)>3;
```

```sql
-- 4.查询哪个部门的部门员工个数>3的部门名和员工个数，并按个数降序（排序）
SELECT COUNT(*),`department_name`
FROM `departments` d
INNER JOIN `employees` e ON d.`department_id`= e.`department_id`
GROUP BY d.`department_id`
HAVING COUNT(*)>3
ORDER BY COUNT(*) DESC;
```

```sql
-- 5.查询员工名、部门名、工种名，并按部门名降序
SELECT `last_name`,`department_name`,`job_title`
FROM `employees` e
INNER JOIN `departments` d ON e.`job_id` = j.`job_id`
INNER JOIN `jobs` j ON e.`department_id` = d.`department_id`
ORDER BY `department_name` DESC;
```

##### 2.1.2.非等值连接

```sql
-- 查询员工的名字和工资级别
SELECT last_name,grade_level
FROM employees e
INNER JOIN job_gradesON salary BETWEEN lowest_sal AND highest_sal;
```

##### 2.1.3.自连接

```sql
-- 查询员工姓名中包含字符k的员工的名字、上级的名字
SELECT e.last_name 员工名字,m.last_name 上级名字
FROM employees e
INNER JOIN employees m ON e.manager_id = m.employee_id
WHERE e.`last_name` LIKE '%k%';
```

#### 2.2.外连接

应用场景:用于查询一个表中有,另一个表没有的记录
特点:

- 外连接的查询结果为主表中的所有记录,如果从表中有和它匹配的,则显示匹配的值,如果从表中没有和它匹配的,则显示null
- 外连接查询结果=内连接结果+主表中有而从表没有匹配结果的记录.换言之，外连接结果必须包含主表所有记录，相配的从表记录可以null
- 左外连接,left join左边的是主表;右外连接,right join右边的是主表
- 左外和右外交换两个表的顺序，可以实现同样的效果
- 全外连接=内连接的结果+表1中有但表2没有的+表2中有但表1没有的(MySQL不支持)

##### 2.2.1.外连接

```sql
-- 左外连接
-- 查询男朋友不在男神表的女神名
SELECT `name` ,`boyName`
FROM `beauty` be
LEFT JOIN `boys` bo ON be.`boyfriend_id` = bo.`id`
WHERE bo.`id` IS NULL; -- 一般主键不为空
```

```sql
-- 右外连接
-- 查询男朋友不在男神表的女神名
SELECT `name` ,`boyName`
FROM `boys` bo
RIGHT JOIN  `beauty` be ON be.`boyfriend_id` = bo.`id`
WHERE bo.`id` IS NULL;
```

##### 2.2.2.交叉连接

笛卡尔乘积（没有主次之分）

```sql
-- 交叉连接
SELECT bo.* ,be.*
FROM `boys` bo
CROSS JOIN  `beauty` be;
```

