---
title: 'MySQL:DDL（数据定义语言）'
tags:
  - MySQL
  - 数据库操作
  - 表格操作
abbrlink: 9938db0d
date: 2022-03-12 15:31:46
---

学习笔记：介绍对MySQL中库和表的属性的操作。

<!--more-->

### 1.库和表的管理

​    一、库的管理
​        创建、修改、删除
​    二、表的管理
​        创建、修改、删除
​    	创建:create
​    	修改:alter
​    	删除:drop

#### 1.1.库管理

```sql
-- 创建一个库books
CREATE DATABASE books;
SHOW DATABASES;
-- 增加容错性
CREATE DATABASE IF NOT EXISTS books ;
-- 暂时不能修改库名，容易丢失数据，不够安全

-- 更改字符集
ALTER DATABASE books CHARACTER SET gbk;

-- 删除库
DROP DATABASE IF EXISTS books;
```

#### 1.2.表的管理

##### 1.2.1 表结构的创建

```sql
create table表名(
    列名 列的类型【(长度) 约束】
    列名 列的类型【(长度) 约束】
    列名 列的类型【(长度) 约束】
    列名 列的类型【(长度) 约束】
)
```

```sql
-- 在books库中创建book表
CREATE TABLE IF NOT EXISTS book(
    id INT,
    bName VARCHAR(20),
    price DOUBLE,
    authorId INT,
    publishDate DATETIME
    );
    
DESC book;

```

##### 1.2.2.表结构的修改

```sql
 alter table 表名 change|add|drop|modify column 列名 【类型 约束】;
```

```sql
-- 修改列名
ALTER TABLE book CHANGE COLUMN publishDate pubDate DATETIME;
```

```sql
-- 修改类型
ALTER TABLE book MODIFY COLUMN pubDate TIMESTAMP;
```

```sql
-- 添加新列
-- ALTER TABLE book ADD COLUMN 列名 类型 【first|after 字段名】
ALTER TABLE book ADD COLUMN annual DOUBLE first;
```

```sql
-- 删除列
ALTER TABLE book DROP COLUMN annual;
```

##### 1.2.3.表结构的删除

```sql
-- 修改表名
ALTER TABLE author RENAME TO book_author;
```

```sql
-- 删除表
DROP TABLE book_author;
SHOW TABLES FROM books;
```

```sql
-- 通用写法
-- 新建库
DROP DATABASE IF EXISTS 库名;
CREATE DATABASE 库名;
-- 新建表
DROP TABLE IF EXISTS 表名;
CREATE TABLE 表名 ();
```

##### 1.2.4.表的复制

```sql
-- 表的复制
-- 仅仅复制表的结构
CREATE TABLE 表名 LIKE 旧表

-- 复制表的结构和数据
CREATE TABLE 表名 
    SELECT 查询列表 FROM 表名
```

```sql
-- 仅仅复制表的结构
CREATE TABLE newcopy LIKE author;
```

```sql
-- 复制表的全部
CREATE TABLE newcopy2 
    SELECT * FROM author;
```

```sql
-- 复制表的部分数据
CREATE TABLE newcopy3 
    SELECT * FROM author
    WHERE nation = '中国';

```

```sql 
-- 复制表的列
CREATE TABLE newcopy4 
    SELECT `name` FROM author;
```

```sql  
-- 复制表的部分字段（丢弃所有数据）
CREATE TABLE newcopy5
    SELECT `name` ,id FROM author
    WHERE FALSE;
```

### 2.数据类型介绍

#### 2.1.整型

    ```sql
    -- 整型类型
        Tinyint(M,D)
        Smallint(M,D)
        Mediumint(M,D)
        Int(M,D) / integer(M,D)
        Bigint(M,D)
    1.都可以设置无符号和有符号，默认有符号，通过unsigned设置无符号
    2.如果超过了范围，会报 out or range 异常，插入临界值
    3.长度可以不指定，默认会有一个长度
      长度代表显示的最大宽度，如果不够左边可以用 0 代替，但需要搭配 zerofull ,并且变为无符号整型
    ```


```sql
-- 设置无符号和有符号位
CREATE TABLE test (
type1 INT,  -- 有符号
type2 INT UNSIGNED  -- 无符号
);
-- 超出范围则设置临界值
```

#### 2.2.小数

```sql
小数 
   浮点型 float(M,D)、 double(M,D)
   定点型 dec(M,D) / decimal(M,D)
   M指的是数据总长度，D指的是小数点后保留的位数
1.M,D都可以省略，
  如果是 float和 double，则会根据插入的数值的精度来决定
  如果是 decimal,则M默认为 10，D默认为 0
  定点数比 double精度要高，如货币运算等则考虑使用   
原则：sql
  所选择的类型越简单越好，能保存数值的类型越小越好
```

```sql
-- 例子
CREATE TABLE test (
type1 FLOAT(5,2) ,
type2 DOUBLE(5,2),
type3 DEC(5,2)
);
INSERT INTO test VALUES
(2.22233,2.22233,2.22233),
(2.23,2.23,2.23),
(2,2,2)
```

#### 2.3.字符型

```sql
较短的文本
    char(M)    固定长度的字符（只要填写就是固定字符长度。比较耗费空间）
    varchar(M) 不定长度的字符（字符长度随填写变化，效率较低）
    M指的是字符长度，char可以省略，默认为1；varchar不能省略
    其他类型： binary、 varbinary(二进制文本)
             enum (枚举，只能插入枚举里边的数据，不区分大小写)
                 例： CREATE TABLE test(type1 ENUM('a','b','c'));
                     INSERT INTO test VALUES('a'),('B'),('c');
                     SELECT * FROM test;
             set(集合，和 Enum类型类似，里面可以保存0·64个成员。区别是set类型一次可以选取多个成员，而enum只能选一个，储存字节数和集合长度有关)
                 例： CREATE TABLE test2(type1 SET('a','b','c'));
                      INSERT INTO test2 VALUES('a'),('B,c'),('c');
                      SELECT * FROM test2;
较长的文本
    text
    blob（较大的二进制）
```

#### 2.4.日期型

|   类型    | 字节 |     最小值      |      最大值      |
| :-------: | :--: | :-------------: | :--------------: |
|   date    |  4   |   1000-01-01    |    9999-12-31    |
| datetime  |  8   |   1000-01-01    |    9999-12-31    |
| timestamp |  4   | 197000101080001 | 2038年的某个时刻 |
|   time    |  3   |  -838：59：59   |   838：59：59    |
|   year    |  1   |      1901       |       2155       |


```sql
1、 timestamp时间范围较小
2、 timestamp和实际时区有关,更能反映实际的日期，而 datetime则只能反映出插入时的当地时区
3、 timestamp的属性变Mysql版本和SQLMode的影响很大
```

```sql
-- 举例
CREATE TABLE test(
    type1 DATETIME,
    type2 TIMESTAMP
);

INSERT INTO test VALUES (NOW(),NOW()); 
-- 显示时区
SHOW VARIABLES LIKE 'time_zone';

-- 修改时区(东九区）
SET time_zone = '+9:00';
-- 说明timestamp受时区影响
```

### 3.常见约束

```sql
含义：用来限制表中的数据，为了保证表中的数据的准确和可靠性
分类：六大约束 
    NOT NULL：非空，用于保证该字段的值不能为空
    DEFAULT:默认，用于保证该字段有默认值
    PRIMARY KRY:主键，用于保证该字段的值具有唯一性，并且非空
    UNIQUE：用于保证该字段的值具有唯一性，可以为空
    CHECK:检查约束【mysql中不支持】
    FOREIGN KEY:外键，用于限制两个表的关系，用于保证该字段的值必须来自与主表的关联列的值
时机：
    1.创建表时
    2.修改表时
分类：
    列级约束
        六大约束语法都支持，但外键约束没有效果
    表级约束
        除了非空、默认，其他都支持
```

#### 3.1 .创建表时添加约束

```sql
CREATE TABLE test(
    列名 类型 列级约束,
    列名 类型 列级约束,
    表级约束
);
```

```sql
/*
列级约束
    直接在字段名和类型后面追加约束类型即可
    只支持：默认、非空、主键、唯一
*/
-- 主表
CREATE TABLE major(
    id INT PRIMARY KEY,
    majorName VARCHAR(20) 
    );

-- 从表
CREATE TABLE stuinfo(
    id INT PRIMARY KEY,
    stuName VARCHAR(20) NOT NULL,
    gender CHAR(1) CHECK(gender = '男'OR gender='女'),
    seat INT UNIQUE,
    age INT DEFAULT 18,
    majorId INT ,
    CONSTRAINT foreignkey FOREIGN KEY(majorId) REFERENCES major(id)
    );
-- 查询表结构
DESC stuinfo;
-- 查询表索引
SHOW INDEX FROM stuinfo;
SHOW INDEX FROM major;
```

```sql
/*
表级约束
    在各字段的最下面
    【constraint 约束名】 约束类型（字段名）
*/
CREATE TABLE stuinfo(
    id INT ,
    stuName VARCHAR(20),
    gender CHAR(1) ,
    seat INT ,
    age INT ,
    majorId INT,
/*** constraint 约束名 约束类型 （字段名）
    CONSTRAINT pk PRIMARY KEY (id),
    CONSTRAINT ch CHECK (gender = '男'OR gender='女'),
    CONSTRAINT se UNIQUE (seat),
    CONSTRAINT foreignkey FOREIGN KEY(majorId) REFERENCES major(id)
);
```

    主键和唯一的对比
    主键：保证唯一性，不允许为空，一个表中只能有一个（可以是多个列组合）
    唯一：保证唯一性，允许为空，一个表中可以有多个（可以是多个列组合）
    外键：
        1.要求在从表设置外键关系
        2.从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无所谓
        3.主表的关联列必须是一个key(一般是主键或唯一)

#### 3.2.修改表时添加约束

```sql
-- 列级约束
ALTER TABLE 表名 MODIFY COLUMN 列名 类型 约束;
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
-- 表级约束
ALTER TABLE 表名 ADD 约束 (列名);
ALTER TABLE stuinfo ADD FOREIGN KEY (majorId) REFERENCES major(id);
```

####  3.3.修改表时删除约束

```sql
-- 列级约束
ALTER TABLE 表名 MODIFY COLUMN 列名 类型;
ALTER TABLE stuinfo MODIFY COLUMN id INT;
-- 表级约束
ALTER TABLE 表名 DROP INDEX 约束名;
ALTER TABLE stuinfo DROP PRIMARY KEY; -- 主键就一个，所以不用写名
ALTER TABLE stuinfo DROP FOREIGN KEY foreignkey;
```

#### 3.4.标识列(auto_increment)

    ```
    标识列又为自增长列.
    ​    含义：
    ​        可以不用手动的插入值，系统提供默认的序列值
    ​    特点：
    ​        1、标识列必须和主键搭配吗?
    ​            不一定，但要求是一个key
    ​        2、一个表可以有几个标识列?
    ​            至多一个!
    ​        3、标识列的类型只能是数值型
    ​        4、标识列可以通过auto_increment_increment设置步长，
    ​          初始值可以在插入值的时候定义
    ```

```sql
CREATE TABLE test (
    id INT PRIMARY KEY AUTO_INCREMENT,
    NAME VARCHAR(20)

);

INSERT INTO test VALUES(NULL,'john');
-- 查看自增长项
SHOW VARIABLES LIKE "%auto_increment%";
/*
    Variable_name             Value   
    ------------------------  --------
    auto_increment_increment  1       步长
    auto_increment_offset     1       初始值
*/
-- 修改步长
SET auto_increment_increment = 3;
/*
    Variable_name             Value   
    ------------------------  --------
    auto_increment_increment  3       
    auto_increment_offset     1 
*/
```

