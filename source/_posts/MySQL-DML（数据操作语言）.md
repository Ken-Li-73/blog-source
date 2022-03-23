---
title: 'MySQL:DML（数据操作语言）'
tags:
  - MySQL
abbrlink: 9e77971c
date: 2022-03-12 15:30:55
---

学习笔记：介绍MySQL中对数据的增删改操作。

<!--more-->DML语言

```mysql
数据操作语言:
    插入: insert
    修改: update
    删除: delete
```
### 1. 插入语句
#### 1. 1 语法
```mysql
-- 语法一:
insert into 表名(列名，... ) values (值1,...);
```

```mysql
-- 语法二:
insert into 表名 set 列名 = 值,列名 = 值,....;
```
#### 1. 2 用例
```sql
-- 1.插入的值的类型要与列的类型一致或兼容
INSERT INTO boys(id,boyName,userCP)
VALUES(20,'小懒虫',99);
```

```sql
-- 2.不可以为null的列必须插入值。可以为null的列如何插入值?
      要么列名和值都省略，要么列名写着，值为null
INSERT INTO boys(id,boyName)
```

```sql
VALUES(21,'大懒虫');
-- 3.列的顺序可以调换
INSERT INTO beauty(NAME,sex,id, phone)
VALUES('蒋欣','女',16,'10');
```

```sql
-- 4.列数和值的个数必须一致
INSERT INTO beauty(NAME,sex, id, phone, boyfriend_id)
VALUES('关晓彤','女',17,'110' );
```

```sql
-- 5.可以省略列名,默认所有列,而且列的顺序和表中列的顺序一致
INSERT INTO boys
VALUES(22,'小懒虫',99);
```

```sql
INSERT INTO boys
set id=24,boyName='小懒虫';
```

#### 1.3. 方式比较

```sql
-- 1.方式一支持插入多行,方式二不支持
INSERT INTO beauty
VALUES
(23,'唐艺昕','女'，'1990-4-23','1898888' ,NULL,2),
(24,'唐艺昕2','女','1990-4-21','1898888888', NULL, 2),
(25,'唐艺昕3','女','1990-4-20' ,'1898888888',NULL,2) ;
```

```sql
-- 2、方式—支持子查询,方式二不支持
INSERT INTO beauty (id,NANE, phone)
    SELECT 26,'宋茜','1889866';
```


### 2.修改语句
#### 2. 1 语法
```sql
1.修改单表的记录★

    语法:
    update 表名
    set 列=新值,列=新值,...
    where 筛选条件;
2.修改多表的记录【补充】

    语法:
    sql92语法:
        update 表1 别名,表2 别名
        set 列=值,...
        where 连接条件
        and 筛选条件;
    sql99语法:
        update 表1 别名
        inner|left|right join 表2 别名 
        on 连接条件
        set 列=值,...
        where 筛选条件;
```
#### 2. 2 用例
```sql
-- 修改没有男朋友的女神的男朋友编号都为2号
UPDATE beauty
SET boyfriend_id=2
WHERE boyfriend_id NOT IN(
    SELECT id
    FROM boys)
AND boyfriend_id IS NULL;

UPDATE beauty be
LEFT JOIN boys bo
ON be.boyfriend_id = bo.id
SET boyfriend_id = 2
WHERE bo.id IS NULL;
```

### 3. 删除语句
#### 3. 1 语法
```mysql
方式一: delete
​    语法:
​    1、单表的删除【★】
​        delete from 表名 where 筛选条件 【limit 条目数】
​    2、多表的删除
​        sql92语法:
​            delete 表1的别名,表2的别名
​            from 表1 别名,表2 别名
​            where 连接条件
​            and 筛选条件;
​        sql99语法:
​            delete 表1的别名,表2的别名
​            from 表1 别名
​            inner | left | right join 表2 别名 
​            on 连接条件
​            where 筛选条件
```

```mysql
方式二: truncate
语法:
    truncate table 表名;
    -- truncate语句,不允许删除添加条件，表示清空删除
```
#### 3. 2 用例
```sql
-- 删除手机号以9结尾的女神信息
DELETE FROM beauty 
WHERE phone LIKE '%9';
```

```sql
-- 删除张无忌的女朋友的信息
DELETE be
FROM beauty be
INNER JOIN boys bo
ON be.boyfriend_id = bo.id
WHERE bo.boyName = '张无忌';
```
#### 3.3 delete和truncate比较
	1.delete可以加where条件，truncate不能加。
	2.truncate删除，效率高一丢丢。
	3.假如要删除的表中有自增长列，如果用delete删除后，再插入数据，自增长列的值从断点开始,而truncate删除后，再插入数据，自增长列的值从1开始。   
	4.truncate删除没有返回值，delete删除有返回值。
	5.truncate删除不能回滚，delete删除可以回滚。

