---
title: 'MySQL:view(视图)'
tags:
  - MySQL
  - 视图
abbrlink: e03471c6
date: 2022-03-12 15:33:02
---

学习笔记：介绍MySQL中视图的基本使用。

<!--more-->

### 1.含义

- mysql 15.1版本出现
- 虚拟表，和普通表一样使用，是通过表动态生成的数据。
- 只有sql的逻辑，没有具体数据。

### 2.应用场景

- 多个地方用到的查询结果
- 该查询结果使用的sql语句较复杂

### 3.优点

- 重用sql语句
- 简化复杂的sql操作，不必知道它的查询细节
- 保护数据，提高安全性

### 4.视图结构的修改语法

#### 4.1视图的创建

```sql
CREATE VIEW 视图名
AS
查询语句;
```

```sql
CREATE VIEW q1
AS
SELECT e.last_name ,d.department_name,j.job_title
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id
INNER JOIN jobs j
ON j.job_id = e.job_id;
```
#### 4.2视图的修改

```sql 
-- 语法一：
CREATE OR REPLACE VIEW 视图名
AS
查询语句;

-- 语法二：
ALTER VIEW 视图名
AS
查询语句;
```
#### 4.3视图的删除

```sql
DROP VIEW 视图名,视图名,……;
```

#### 4.4视图的查看

```sql
DESC q1; -- 查看结构
SHOW CREATE VIEW q1;--查看具体细节
```
### 5.视图数据的更新语法
#### 5.1插入

```mysql
insert into 视图名 values (属性1，属性2，属性3，……);
```
#### 5.2修改

```mysql
insert into 视图名 values (属性1，属性2，属性3，……);
```

视图的可更新性和视图中查询的定义有关系，以下类型的视图是不能更新的。

- 包含以下关键字的sql语句:分组函数、distinct、group by、 having、union或者union all
- 常量视图
- Select中包含子查询join
- from一个不能更新的视图
- where子句的子查询引用了from子句中的表

### 6.视图和表的异同
视图可以当做表进行查询，但不能修改原表数据
```sql 
-- 语法：可以当成表一样使用
SELECT 查询列表
FROM 视图名
WHERE 筛选条件
GROUP BY 分组列表
HAVING 筛选条件
ORDER BY 排序
LIMIT 起始索引 条目数;
```

|      | 创建语法的关键字 | 是否实际占用物理空间 |          使用          |
| :--: | :--------------: | :------------------: | :--------------------: |
| 视图 |   create view    |  只是保存了sql逻辑   | 可以但一般不能增删改查 |
|  表  |   create table   |      保存了数据      |        增删改查        |

