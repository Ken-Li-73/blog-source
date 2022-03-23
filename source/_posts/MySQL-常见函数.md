---
title: 'MySQL:常见函数'
tags:
  - MySQL
  - 常见函数
abbrlink: cc918917
date: 2022-03-12 14:58:37
---

  学习笔记：介绍MySQL中常见函数的基本使用。

<!--more-->

概念:类似于java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名  

```
好处:
​        1、隐藏了实现细节
​        2、提高代码的重用性  
​    调用:select 函数名(实参列表) 【from 表】;  
​    特点:     
​        ①叫什么(函数名)   
​        ②千什么(函数功能)  
​    分类:      
​        1、单行函数      
​            字符函数
​            数学函数
​            日期函数
​            其他函数【补充】
​            流程控制函数【补充】      
​        2、分组函数      
​            功能:做统计使用，又称为统计函数、聚合函数、组函数     
```

### 1. 字符函数

```sql
-- length获取参数值的字节个数
SELECT LENGTH( 'john' ) ; -- 4
SELECT LENGTH('张三丰' ) ;-- 9
SHOW VARIABLES LIKE '%char%' ;-- 查看字符集（utf8汉字占3个字节，GBK汉字占2个字节)
```

```sql
-- concat 拼接字符串
SELECT CONCAT (last_name,'_',first_name) FROM employees;
```

```sql
-- upper、 lower 大小写
SELECT UPPER('john');-- JOHN
SELECT LOWER('John');-- john

-- 示例:将姓变大写，名变小写，然后拼接
SELECT 
    CONCAT(UPPER(last_name),' ',LOWER(first_name)) AS 姓名
FROM 
    employees;
```

```sql
-- substr、 substring 获取子串(注意:索引从1开始 )
-- 截取从指定索引处后面所有字符      
SELECT SUBSTR('李莫愁爱上了陆展元',7) out_put;
-- 截取从指定索引处指定字符长度的字符
SELECT SUBSTR('李莫愁爱上了陆展元',1,3) out_put;
-- 案例:姓名中首字符大写，其他字符小写然后用_拼接，显示出来
SELECT 
    CONCAT(UPPER (SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) 姓
FROM 
    employees;
```

```sql
-- instr 获取字符串在所匹配的字符串首次出现的索引位置，如果没有则返回0
SELECT INSTR('李莫愁爱上了陆展元','陆展元') out_put;
```

```sql
-- trim 去除前后的字符，默认为空格
SELECT TRIM('    李   莫   愁    ') out_put; -- 李   莫   愁
SELECT TRIM('a' FROM 'aaaaa陆aaaa展aaa元aaaaa') out_put; -- 陆aaaa展aaa元
SELECT TRIM('aa' FROM 'aaaaa陆aaaa展aaa元aaaaa') out_put; -- a陆aaaa展aaa元a
```

```sql
-- lpad 用指定的字符实现左填充
SELECT LPAD('李莫愁',10,'*') out_put; -- *******李莫愁
SELECT LPAD('李莫愁',2,'*') out_put; -- 李莫
```

```sql
-- rpad 用指定的字符实现左填充
SELECT RPAD('李莫愁',10,'ab') out_put; -- 李莫愁abababa
```

```sql
-- replace 用指定的字符替换所有匹配字符
SELECT 
    replace('张无忌爱上了周芷若周芷若周芷若周芷若','周芷若','赵敏') out_put; 
    -- 张无忌爱上了赵敏赵敏赵敏赵敏
```

### 2. 数学函数

```sql
-- round四舍五入
-- round(要运算的数,【保留的小数位数】);
SELECT ROUND(-1.55); -- -2
SELECT ROUND(1.567,2) ;-- 1.57

-- ceil 向上取整,返回>=该参数的最小整数
SELECT CEIL(-1.02) ;

-- floor 向下取整,返回<=该参数的最大整数
SELECT FLOOR (1.02); -- 1

-- mod  取余
-- mod (a, b) : a-a/b*b（余数正负只与被余数有关）
SELECT MOD (-10,-3) ; -- -1
SELECT MOD( 10,-3 ) ;
SELECT 10%3;
```

### 3. 日期函数

```sql
-- now 返回当前系统日期+时间
SELECT NOW();

-- curdate 返回当前系统日期
SELECT CURDATE();

-- curtime 返回当前系统时间
SELECT CURTIME();

-- 获取指定的部分 年，月，日，时，分，秒
SELECT
    YEAR(NOW()) 年,
    MONTH(NOW()) 月,
    DAY(NOW()) 日,
    HOUR(NOW()) 时,
    MINUTE(NOW()) 分,
    SECOND(NOW()) 秒;

SELECT
    MONTHNAME(NOW()) 月分,
    DAYNAME(NOW()) 星期;

-- str_to_date 将字符串转换成日期
-- STR_TO_DATE(日期，解析格式);
SELECT 
    STR_TO_DATE('12-31-2020','%m-%d-%Y');-- 2020-12-31

-- date_format 将字符串转换成日期
-- date_format(日期，解析格式);
SELECT
    DATE_FORMAT('2020-12-31','%y年%m月%d日');-- 20年12月31日
-- 有
%Y
```

| 序号 | 格式符 |     功能     |
| :--: | :----: | :----------: |
|  1   |   %Y   | 四位数的年份 |
|  2   |   %y   | 两位数的年份 |
|  3   |   %m   |     月份     |
|  3   |   %d   |     日期     |
|  3   |   %H   |   24小时制   |
|  3   |   %h   |   12小时制   |
|  3   |   %i   |     分钟     |
|  3   |   %s   |      秒      |

```sql
-- 查询入职时间为4月/3日/1992年的员工信息
SELECT *
FROM employees
WHERE hiredate = STR_TO_DATE('4月/3日/1992年','%m月/%d日/%Y年');
```

### 4.  其他函数

```sql
SELECT VERSION(); -- 获取版本号
SELECT DATABASE();-- 获取数据库
SELECT USER();-- 获取用户
```

### 5. 流程控制函数

```sql
-- if函数:if else 的效果
SELECT IF(10<5,'大·，'小' ) ;

SELECT 
    last_name ,
    commission_pct,
    IF(commission_pct IS NUL,'没奖金，呵呵','有奖金，嘻嘻')备注 
FROM employees;

```

```sql
-- case 函数
/* 
    case 要判新的字段或表达式
    when 常量1 then 要显示的值1或语句1
    when 常量2 then 要显示的值2或语句2
    ...
    else 要显示的值n或语句n
    end
    /******类似switch******/
*/
-- 查询工资，要求
-- 部门号=30，显示的工资为1.1倍
-- 部门号=40，显示的工资为1.2 倍
-- 部门号=50，显示的工资为1.3倍
SELECT 
    salary,
    department_id,
    CASE department_id
    WHEN 30 THEN salary*1.1
    WHEN 40 THEN salary*1.2
    WHEN 50 THEN salary*1.3
    ELSE salary 
    END AS 工资
FROM 
    employees;
```

```sql
/* 
    case 
    when 条件1 then 要显示的值1或语句1
    when 条件2 then 要显示的值2或语句2
    ...
    else 要显示的值n或语句n
    end
    /******类似if...elseif...else******/
*/

-- 案例:查询员工的工资的情况
-- 如果工资>20000,显示A级别,
-- 如果工资>15000,显示B级别,
-- 如果工资>10000，显示c级别,
-- 否则，显示D级别。

SELECT 
    salary,
    CASE 
    WHEN salary>20000 THEN 'A'
    WHEN salary>15000 THEN 'B'
    WHEN salary>10000 THEN 'C'
    ELSE 'D'
    END AS 级别
FROM employees;
```

```sql
-- 练习
-- 1.显示系统时间(注:日期+时间)
SELECT NOW();
-- 2.查询员工号，姓名，工资，以及工资提高百分之20%后的结果(new salary)
SELECT employee_id,last_name,salary,salary*(1+0.2) `new salary`
FROM employees;

-- 3.将员工的姓名仅按首字母排序，并写出姓名的长度(LENGTH)
SELECT last_name,LENGTH(last_name) 长度
FROM employees
ORDER BY SUBSTR(last_name,1,1) ASC;

-- 4.做一个查询，产生下面的结果
-- <last_name> earns <salary> monthly but wants <salary*3>
-- Dream salary
-- King earns 24000 monthly but wants 72000
SELECT CONCAT(last_name,' earns ',salary,' monthly but wants ',salary*3) AS `Dream salary`
FROM employees;

-- 5.使用CASE--WHEN，按照下面的条件:
-- job  grade
-- AD_PRES  A
-- ST_MAN   B
-- IT_PROG  C
SELECT  job_id ,
    CASE job_id
    WHEN 'AD_PRES' THEN 'A'
    WHEN 'ST_MAN' THEN 'B'
    WHEN 'IT_PROG' THEN 'C'
    -- else ''
    END AS grade
FROM employees;
```

### 6. 分组函数

|      函数       |    支持类型    |
| :-------------: | :------------: |
|    sum 、avg    |     数值型     |
| max、min、count | 数值型、字符型 |

**运算过程中，以上分组函数均忽略null**
**可以和distinct搭配**

```sql
-- 举例
SELECT 
    SUM(salary) 总和,
    AVG(salary) 平均值,
    MIN(salary) 最小值,
    MAX(salary) 最大值,
    COUNT(salary) 总个数
    -- count(10000,salary) 
FROM employees;
```

```sql
-- count函数详细介绍
COUNT(salary) FROM employees;-- 统计salary非null的行数
COUNT (*) FROM employees;-- 统计行数
COUNT (常量值) FROM employees;--（相当于加了一列常量数据） 统计行数
-- 和分组函数一同查询的字段要求是group by后的字段
-- group by 后面会涉及

-- 查询部门编号为90的员工个数
SELECT COUNT(*) 个数
FROM employees
WHERE department_id =90 ; 
```

