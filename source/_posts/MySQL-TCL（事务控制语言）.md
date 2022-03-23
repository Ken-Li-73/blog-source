---
title: 'MySQL:TCL（事务控制语言）'
tags:
  - MySQL
  - 事务
abbrlink: 3112a4fe
date: 2022-03-12 15:32:19
---

学习笔记：介绍MySQL中事务的基本使用。

<!--more-->

### 1.概念
​    事务：         
​        事务由单独单元的一个或多个sQL语句组成，在这个单元中﹐每个MySQL语句是相互侬赖的。而整个单独单元作为一个不可分割的整体﹐如果单元中某条SQL语句一旦执行失败或产生错误，整个单元将会回滚。所有受到影响的数据将返回到事物开始以前的状态;如果单元中的所有sQL语句均执行成功﹐则事物被顺利执行。     
​    存储引擎：           
​        1、概念:在mysql中的数据用各种不同的技术存储在文件（或内存）中。     
​        2、通过show engines;来查看mysql支持的存储引擎。    
​        3、在mysql中用的最多的存储引擎有: innodb,myisam,memory等。     
​            其中innodb支持事务，而myisam、memory等不支持事务       
### 2.ACID属性
​    1.原子性（Atomicity)
​        原子性是指事务是一个个可刀P一lr十都发生，要么都不发生。
​    2.一致性（Consistency)
​        事务必须使数据库从一个一致性状态变换到另外一个一致性状态
​    3.隔离性(Isolation)
​        事务的隔离性是指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相千扰。
​    4.持久性（Durability)
​        持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响
### 3.事务的创建
​    隐式事务:事务没有明显的开启和结束的标记比如insert、 update、 delete语句
​    显式事务:事务具有明显的开启和结束的标记前提:必须先设置自动提交功能为禁用
​    

    设置自动提交功能关闭 set autocommit = 0;
    步骤:
        1.开始事务
        set autocommit = 0;
        start transaction;（可选的）
        2.编写事务中sql语句(select insert update delete)
        执行语句一;
        执行语句二;
        ……;
        3.结束事务(此时事务处理完成，储存在内存中)
        commit;提交事务（更新到磁盘）
        rollback;回滚事务（撤销事务的指令）

### 4.事务的使用步骤

```sql
/*
    id  username   balance  
------  ---------  ---------
     1  张无忌              1000
     2  赵敏              1000
*/
-- 1.开始事务
SET autocommit = 0;
START TRANSACTION;
-- 2.编写事务
UPDATE account SET balance = 500 WHERE username = '张无忌';
UPDATE account SET balance = 1500 WHERE username = '赵敏';
-- 3.结束事务
COMMIT;
```

##### delete和truncate在事务使用时的区别

delete支持回滚，truncate不支持回滚

```sql
-- 使用delete 删除表格
set autocommit = 0;
start transaction;
delete from 表1;
rollback;
select * from 表1; -- 此时还能查询到表1
-- 使用delete 删除表格
set autocommit = 0;
start transaction;
truncate table 表1;
rollback;
select * from 表1; -- 此时不能查询到表1
```



### 5.数据的隔离级别
#### 5.1.并发问题
    对于同时运行的多个事务,当这些事务访问数据库中相同的数据时,如果没有采取必要的隔离机制,就会导致各种并发问题:
    脏读:
        对于两个事务T1,T2,
        T1读取了已经被T2更新但还没有被提交的字段之后,
        若T2回滚,T1读取的内容就是临时且无效的。
    不可重复读:
        对于两个事务T1,T2,
        T1读取了一个字段,然后T2更新了该字段之后,
        T1再次读取同一个字段,值就不同了。
    幻读:
        对于两个事务T1,T2,
        T1从一个表中读取了一个字段,然后T2在该表中插入了一些新的行之后,
        如果T1再次读取同一个表,就会多出几行.

#### 5.22.隔离级别
    数据库事务的隔离性:
        数据库系统必须具有隔离并发运行各个事务的能力，使它们不会相互影响,避免各种并发问题。
    一个事务与其他事务隔离的程度称为隔离级别.数据库规定了多种事务隔离级别,不同隔离级别对应不同的干扰程度,隔离级别越高,数据一致性就越好,但并发性越弱.

|             隔离级别              |                             描述                             |
| :-------------------------------: | :----------------------------------------------------------: |
| READ UNCOMMITTED   (读未提交数据) | 允许事务读取未被其他事物提交的变更.脏读,不可重复读和幻读的问题都会出现 |
|   READ COMMITED  (读已提交数据)   | 只允许事务读取已经被其它事务提交的变更.可以避免脏读,但不可重复读和幻读问题仍然可能出现 |
|   REPEATABLE READ    (可重复读)   | 确保事务可以多次从一个字段中读取相同的值.在这个事务持续期问,禁止其他事物对这个字段进行更新.可以避免脏读和不可重复读.但幻读的问题仍然存在. |
|      SERIALIZABLE   (串行化)      | 确保事务可以从一个表中读取相同的行.在这个事务持续期间,禁止其他事务对该表执行插入.更新和删除操作.所有并发问题都可以避免,但性能十分低下 |

    Oracle支持的2种事务隔离级别:READ COMMITED,SERIALIZABLE。Oracle 默认的事务隔离级洲为:READ COMMITED
    Mysql支持4种事务隔离级别.Mysql默认的事务隔离级别为: REPEATABLE READ
    
    通过命令查看当前隔离级别
    select @@tx_isolation  （Mysql5）
    select @@transaction_isolation  （Mysql8）
    设置最低的隔离级别
    set session transaction isolation level read uncommitted

```cmd
#数据
select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |     500 |
|  2 | 赵敏      |    1500 |
+----+-----------+---------+
```

######2.1隔离级别为READ UNCOMMITTED

```cmd
#窗口一：修改隔离级别，并查看隔离级别
mysql> set session transaction isolation level read uncommitted;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| READ-UNCOMMITTED        |
+-------------------------+
```

```cmd
#窗口二：修改隔离级别，并查看隔离级别和表数据
mysql> set session transaction isolation level read uncommitted;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| READ-UNCOMMITTED        |
+-------------------------+
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张无忌   |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
```

```cmd
#窗口一：开启事务，修改数据，但不结束事务
mysql> set autocommit = 0;
mysql> update account set username =  '张飞' where id = 1;
```

```cmd
#窗口二：读数数据，
mysql> set autocommit = 0;
mysql> update account set username =  '张飞' where id = 1;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张飞     |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
#数据发生改变，读取了窗口一未提交的数据，产生脏读
```

```cmd
#窗口一：结束事务，回滚撤销
mysql> rollback;
```

```cmd
#窗口二：重新读取数据，数据变回原来样子
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张无忌   |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
#同一事务，查询前后不一致，产生不可重复读
mysql> commit;
```

######2.2隔离级别为READ COMMITTED

```cmd
#窗口一：修改隔离级别，并查看隔离级别
mysql> set session transaction isolation level read committed;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| READ-COMMITTED          |
+-------------------------+
```

```cmd
#窗口二：修改隔离级别，并查看隔离级别和表数据
mysql> set session transaction isolation level read committed;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| READ-COMMITTED          |
+-------------------------+
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张无忌   |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
```

```cmd
#窗口一：开启事务，修改数据，但不结束事务
mysql> set autocommit = 0;
mysql> update account set username =  '张飞' where id = 1;
```

```cmd
#窗口二：开启事务，读取数据，数据没有发生改变，没有产生脏读现象
mysql> set autocommit = 0;
mysql> select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |     500 |
|  2 | 赵敏      |    1500 |
+----+-----------+---------+
```

```cmd
#窗口一：结束事务，提交
mysql> commit;
```

```cmd
#窗口二：读取数据，数据发生改变，说明读取了窗口一提交后的数据
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张飞     |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
#但同一数据，前后查询现象不一样，产生不可重复读
mysql> commit;
```

######2.3隔离级别为REPEATABLE READ

```cmd
#窗口一：修改隔离级别，并查看隔离级别
mysql> set session transaction isolation level repeatable read;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
```

```cmd
#窗口二：修改隔离级别，并查看隔离级别和表数据
mysql> set session transaction isolation level repeatable read;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张飞     |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
```

```cmd
#窗口一：开启事务，修改数据，但不结束事务
mysql> set autocommit = 0;
mysql> update account set username =  '张飞' where id = 1;
```

```cmd
#窗口二：开启事务，读取数据，数据没有发生改变，没有产生脏读现象
mysql> set autocommit = 0;
mysql> select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |     500 |
|  2 | 赵敏      |    1500 |
+----+-----------+---------+
```

```cmd
#窗口一：结束事务，提交
mysql> commit;
```

```cmd
#窗口二：读取数据，数据仍然没发生改变
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张飞     |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
#同一事务，没有脏读，可重复读

#新开一个事务，更新数据
mysql> commit;
mysql> select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |     500 |
|  2 | 赵敏      |    1500 |
+----+-----------+---------+
mysql> commit;
```

######2.4产生幻读

```cmd
#窗口一：修改隔离级别，并查看隔离级别
mysql> set session transaction isolation level repeatable read;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
mysql> set autocommit = 0;
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张无忌   |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
```

```cmd
#窗口二：修改隔离级别，并查看隔离级别和表数据
mysql> set session transaction isolation level repeatable read;
mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张无忌   |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
```

```cmd
#窗口一：开启事务
mysql> set autocommit = 0;
```

```cmd
#窗口二：开启事务，增加数据，
mysql> set autocommit = 0;
mysql> insert into account values(3,'小昭',1000);
```

```cmd
#窗口一：修改数据，但不结束事务
mysql> update account set balance =  1000;
#理论上修改两行数据
mysql> select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |    1000 |
|  2 | 赵敏      |    1000 |
+----+-----------+---------+
```

```cmd
#窗口二：结束事务
mysql> commit;
```

```cmd
#窗口一：修改了三行数据
mysql> select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |    1000 |
|  2 | 赵敏      |    1000 |
|  3 | 小昭      |    1000 |
+----+-----------+---------+
#产生幻读
mysql> commit;
```

```cmd
#窗口二：读取数据，数据仍然没发生改变
mysql> select * from account;
+----+----------+---------+
| id | username | balance |
+----+----------+---------+
|  1 | 张飞     |     500 |
|  2 | 赵敏     |    1500 |
+----+----------+---------+
#同一事务，没有脏读，可重复读

#新开一个事务，更新数据
mysql> commit;
mysql> select * from account;
+----+-----------+---------+
| id | username  | balance |
+----+-----------+---------+
|  1 | 张无忌    |     500 |
|  2 | 赵敏      |    1500 |
+----+-----------+---------+
mysql> commit;
```


######2.5隔离级别为SERIALIZABLE
    将其他事务处于阻塞状态，且有限时间等待，效率较低

    每启动一个mysql程序,就会获得一个单独的数据库连接.
    每个数据库连接都有一个全局变量@@tx_isolation (@@transaction_isolation), 表示当前的事务隔离级别.
    查看当前的隔离级别: SELECT @@tx_isolation(@@transaction_isolation); 设置当前mysQL连接的隔离级别:
    set transaction isolation level read committed;
    设置薮据库系统的全局的隔离级别:
    set global transaction isolation level read committed;

#####3.保存点（savepoint)
    格式：
        savepoint 节点名;

```sql
SET autocommit = 0;
START TRANSACTION;
DELETE FROM account WHERE id = 2;
SAVEPOINT a; #设置保存点
DELETE FROM account WHERE id = 2;
ROLLBACK TO a;#回滚到保存点
```


####3.视图
    MySQL从5.0.1版本开始提供视图功能。一种虚拟存在的表，行和列的数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的，只保存了sql逻辑，不保存查询结果
    应用场景:
        多个地方用到同样的查询结果
        该查询结果使用的sql语句较复杂
#####3.1视图的创建

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

#####3.2视图的使用

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

```sql
SELECT * 
FROM q1
WHERE e.email LIKE '%a%';
```

#####3.3视图的好处
    重用sql语句
    简化复杂的sql操作，不必知道它的查询细节
    保护数据，提高安全性
#####3.4视图的修改

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

#####3.5视图的删除

```sql
DROP VIEW 视图名,视图名,……;
```

#####3.6视图的查看

```sql
DESC q1; -- 查看结构
SHOW CREATE VIEW q1;--查看具体细节
```

#####3.7视图的更新
    视图的可更新性和视图中查询的定义有关系，以下类型的视图是不能更新的。
    包含以下关键字的sql语句:
        分组函数、distinct、group by、 having、union或者union all
        常量视图
        select中包含子查询
        join
        from一个不能更新的视图
        where子句的子查询引用了from子句中的表
#####3.8视图和表的比较

|      | 创建语法的关键字 | 是否实际占用物理空间 |          使用          |
| :--: | :--------------: | :------------------: | :--------------------: |
| 视图 |   create view    |  只是保存了sql逻辑   | 可以但一般不能增删改查 |
|  表  |   create table   |      保存了数据      |        增删改查        |

