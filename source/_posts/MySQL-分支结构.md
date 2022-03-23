---
title: 'MySQL:分支结构'
tags:
  - MySQL
  - 分支结构
abbrlink: 6577bcb2
date: 2022-03-12 15:34:44
---

学习笔记：介绍MySQL中分支结构的基本使用。

<!--more-->

### 流程控制结构

顺序结构:程序从上往下依次执行
分支结构:程序从两条或多条路径中选择一条去执行
循环结构:程序在满足一定条件的基础上，重复执行一段代码

### 1.分支结构

#### 1.1 if 函数

功能:实现简单的双分支语法:

```sql
if(表达式1,表达式2,表达式3)
```

执行顺序:
如果表达式1成立，则if函数返回表达式2的值，否则返回表达式3的值
应用:任何地方

#### 1.2.case 结构

情况1:类似于java中的switch语句，一般用于实现的等值判断

```sql
case 变量|表达式|字段
when 要判断的值 then 返回的值1或语句1;
when 要判断的值 then 返回的值2或语句2;
······
else 要判断的值n或语句1;
end case;
```

情况2:类似与java中的多重if语句,一般用于实现区间判断

```sql
case 
when 要判断的条件 then 返回的值1或语句1;
when 要判断的条件 then 返回的值2或语句2;
······
else 要判断的值n;
end case;
```

特点:

````sql
1.可以作为表达式，嵌套在其他语句中使用，可以放在任何地方， BEGIN END中或 BEGIN END的外面可以作为独立的语句去使用，只能放在 BEGIN END中.
2.如果 WHEN 中的值满足或条件成立，则执行对应的 THEN 后面的语句，并且结束 CASE 如果都不满足，则执行 ELSE 中的语句或值
3.ELSE 可以省略，如果 ELSE 省略了，并且所有 WHEN 条件都不满足，则返回 NULL
````

#### 1.3 if 结构

功能：实现多重分支

```sql
if 条件1 then 语句1;
elseif 条件1 then 语句1;
elseif 条件1 then 语句1;
······
else 语句n;
end if;
```

应用在begin end中

### 2.循环控制

循环有三个：while ,loop, repeat

iterate类似于continue，继续，结束本次循环，继续下一次

leave类似于break，跳出,结束当前所在的循环

#### 2.1 while 语法

```sql
[标签:] while 循环条件 do
	循环体;
end while [标签];
```

#### 2.2 loop 语法

```sql
[标签:] loop 
	循环体;
end loop [标签];
-- 可以用来模拟简单的死循环
```

#### 2.3 repeat 语法

```mysql
[标签:] repeat
	循环体;
until 结束循环条件
end repeat [标签];
```

### 3. 举例

```sql
delimiter $
CREATE PROCEDURE ddd2 ( IN number INT ) BEGIN
	DECLARE
		i INT DEFAULT 0;
	DECLARE
		max_insert_num INT DEFAULT 5;
	a :
	WHILE
			number >= i DO
			SET i = i + 1;
		-- 判断是否是偶数
		IF
			i % 2 = 1 THEN
				ITERATE a;
		END IF;
		-- 插入数据
		INSERT INTO admin ( username, `password` )
		VALUES
			( concat( 'LI', i ), '22222' );
		
	END WHILE a;
	
END $ CALL ddd2 (
10)
```
