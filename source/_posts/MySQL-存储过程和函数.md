---
title: 'MySQL:存储过程和函数'
abbrlink: '60511917'
date: 2022-03-12 15:34:02
tags:
---

学习笔记：介绍MySQL中存储过程和函数的基本使用。

<!--more-->

存储过程和函数:类似于java中的方法好处:
1、提高代码的重用性
2、简化操作

## 1.存储过程

含义:一组预先编译好的sql语句的集合，理解成批处理语句
1、提高代码的重用性
2、简化操作
3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

### 1.1 创建和调用语法

```sql
-- 创建语法
create procedure 存储过程名(参数列表)
begin
	存储过程体（一组合法的SQL语句）
end
```

```sql
-- 调用语法
call 存储过程名(实参列表)
```

#### 1.1.1 注意

```sql
1.参数列表包含三部分：参数模式、参数名、参数类型
    例子：IN stuname varchar(20)
    参数模式有：
    IN:该参数可以作O为输入，也就是该参数需要调用方传入值
    OUT:该参数可以作为输出，也就是该参数可以作为返回值
	INOUT:该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值
2.如果存储过程体仅仅只有一句话，BEGIN END可以省略
3.存储过程体中的每条SQL语句的结尾要求必须加分号。
4.存储过程的结尾可以使用DELIMITER 重新设置
-- 语法:
DELIMITER 结束标记
-- 例子：
DELIMITER $
```


### 1.2举例

#### 1.2.1 空参列表

```sql
-- 创建
DELIMITER $
CREATE PROCEDURE myp1 () BEGIN
	INSERT INTO admin ( `username`, `password` )
	VALUES
		( 'tom', '1' ),
		( 'rose', '1' ),
		( 'jack', '1' ),
		( 'green', '1' ),
		( 'blue', '1' );
END $
```

```sql
-- 调用
CALL myp1 () ;
```

```cmd
# 调用   cmd使用需要结束符
CALL myp1 () $
```

#### 1.2.2 带IN模式列表

```sql
delimiter $
CREATE PROCEDURE myp2 ( IN girlname VARCHAR ( 50 ), OUT boyname VARCHAR ( 20 ), OUT userCP INT )
BEGIN
	SELECT
		boys.boyName,
		boys.userCP INTO boyname,
		userCP 
	FROM
		boys
		JOIN beauty ON beauty.boyfriend_id = boys.id 
	WHERE
		beauty.NAME = girlname;
	
END $ 
SET @boyname = '';

SET @userCP = 0;
CALL myp2 ( '小昭', @boyname, @userCP );
SELECT
	@boyname,
	@userCP;

```

#### 1.2.3 带OUT模式列表

#### 1.2.4 带INOUT模式列表

## 2 函数

### 2.1 含义

一组预先编译好的sQz语句的集合，理解成批处理语句

- 1、提高代码的重用性
- 2、简化操作
- 3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率
  区别:
  存储过程:可以有0个返回，也可以有多个返回，适合做批量插入、批量更新
  函数:有且仅有1个返回，适合做处理数据后返回一个结果

### 2.2 创建函数

```sql
-- 如果报错1418，在最前面添加 
-- 	select @@global.log_bin_trust_function_creators = 1 ;
CREATE FUNCTION 函数名(参数列表) returns 返回类型
BEGIN
	函数体
END
-- 注意
-- 参数列表包含：参数名 参数类型
-- 函数体：肯定会有return 语句，如果没有会报错，但如果return不放在最后，也不会报错
-- 只有一句话可以省略begin end
-- 使用delimiter 语句设置结束标记

```

### 2.3调用函数

```sql
select 函数名(参数列表)
```

### 2.4查看函数

```mysql
show create function 函数名;
```

### 2.5删除函数

```mysql
drop function 函数名;
```

### 2.6 举例

#### 2.6.1 无参有返回值

```sql
delimiter $
CREATE FUNCTION fv1 () RETURNS INT BEGIN
	DECLARE
		a INT DEFAULT 0;
	SELECT
		count(*) into a 
	FROM
		employees;
	RETURN a;
	
END $

select fv1()$
```

#### 2.6.2 有参有返回值

```sql
delimiter $
CREATE FUNCTION fv3 (
	deptName VARCHAR ( 20 )) RETURNS DOUBLE 
BEGIN
	DECLARE
		agv DOUBLE DEFAULT 0;
	SELECT
		AVG( salary ) INTO agv 
	FROM
		employees e
		JOIN departments d ON d.department_id = e.department_id 
	WHERE
		d.department_name = deptName 
	GROUP BY
		e.department_id;
	RETURN agv;
	
END $

SELECT
	fv3 ( 'Adm' )$
```

