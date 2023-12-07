### [MySQL 存储过程和存储函数](#)
`存储过程是一条或者多条SQL语句的集合，可视为批处理文件，它是旧时代的产物！ 可以在存储过程/函数中定义变量使用游标，封装业务逻辑。`

-----
- [x] [1. 存储过程、存储函数概述](#1-存储过程概述)
- [x] [2. 创建存储过程](#2-创建存储过程)
- [x] [3. 创建存储函数](#3-创建存储函数)
- [x] [4. SQL编程](#4-sql编程)

-----

### [1. 存储过程、存储函数概述](#)
存储程序可以分为存储过程和函数，MySOL 中创建存储过程和函数使用的语句分别是; `CREATE PROCEDURE` 和 `CREATE FUNCTION`。使用 `CALL` 语句来调用存储过程，只能用输出变量返回值。函数可以从语句外调用（即通过引用函数名），也能返回标量值。存储过程也可以调用其他存储过程。
MySQL创建存储过程需要 `delimiter $$` 的配合，因为 `MySQL` 默认是`;` 结尾的！ 

`按道理说存储过程是一个过时的技术了，但是对于一些安全要求极高的数据库，例如银行对所有普通操作使用存储程序。这提供一个坚固而安全的环境，程序可以确保每一个操作都被妥善记入日志。在这样一个设置中，应用程序和用户不可能直接访问数据库表，但是仅可以执行指定的存储程序。`

创建存储过程的基本语法格式：
```sql
delimiter //
CREATE PROCEDURE sp_customers(IN cusid INT)
BEGIN
SELECT * FROM customers WHERE`客户ID`=cusid;
end //
delimiter ;
```

* `sp_customers` 存储过程名字
* `IN` 输入参数  输出参数关键字 `OUT`，同时也可以不定义参数，直接让参数为空。
* `cusid INT` 参数名字 和类型 `int` 类型 
* `BEGIN ... END` ：这是存储过程过程体的固定语法，你需要执行的SQL功能就写在这中间。

下面我们来调用上面的存储过程sp_customers
```sql
CALL sp_customers(1);
```

创建存储函数的基本语法格式：
```sql
CREATE FUNCTION sp_name ([func_parameter[...]])
RETURNS type
[characteristic ...] routine_body
```
* `sp_name` 参数：表示存储函数的名称；
* `func_parameter`：表示存储函数的参数列表；
* `RETURNS type`：指定返回值的类型；
* `characteristic`: 参数：指定存储函数的特性，该参数的取值与存储过程是一样的；
* `routine_body`: 参数：表示 SQL 代码的内容，可以用 BEGIN...END 来标示 SQL 代码的开始和结束。

1. `IN` 输入参数：表示调用者向过程传入值（传入值可以是字面量或变量） 需要输入值时使用IN参数；
2. `OUT` 输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量） 需要返回值时使用OUT参数；
3. `INOUT` 输入输出参数：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量） INOUT参数尽量少用。

#### [1.1 存储过程、存储函数特性](#) 
1. `COMMENT 'string'`∶注释信息，可以用来描述存储过程或函数。
2. `LANGUAGE SQL`∶说明 `routine body` 部分是由 `SQL`语句组成的，当前系统支持的语言为 SQL，SQL 是 LANGUAGE特性的唯一值。
3. `[NOT]DETERMINISTIC`∶指明存储过程执行的结果是否确定。`DETERMINISTIC` 表示结果是确定的。每次执行存储过程时，相同的输入会得到相同的输出。`NOT DETERMINISTIC` 表示结果是不确定的，相同的输入可能得到不同的输出。如果没有指定任意一个值，默认为 `NOT DETERMINISTIC`。
4. `CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA`：指明子程序使用SQL语句的限制。
    4.1 `CONTAINS SQL` 表明子程序包含SQL语句，但是不包含读写数据的语句；
    4.2 `NO SQL`表明子程序不包含SQL语句；
    4.3 `READS SQL DATA`：说明子程序包含读数据的语句；
    4.4 `MODIFIES SQL DATA`表明子程序包含写数据的语句。   
    4.5 默认情况下，系统会指定为 `CONTAINS SQL`
5. `SQL SECURITY { DEFINER | INVOKER }` ：指明谁有权限来执行。DEFINER 表示只有定义者才能执行 `INVOKER` 表示拥有权限的调用者可以执行。默认情况下，系统指定为 `DEFINER`


### [2. 创建存储过程](#)
创建一个带输入，输出参数的存储过程！ 并且调用它

存储过程的参数有三种参数类型, 需要注意的地方是，你传递给存储过程的参数如果需要用到查询，请不要和查询表中的列名一样(忽略大小写)

* `IN` 输入参数：表示调用者向过程传入值（传入值可以是字面量或变量） 需要输入值时使用IN参数；
* `OUT` 输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量） 需要返回值时使用OUT参数；
* `INOUT` 输入输出参数：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量） INOUT参数尽量少用。

```sql
USE `Remix`;
DROP procedure IF EXISTS `serachStudentBySnameAndSage`;

DELIMITER //
USE `Remix`//
CREATE PROCEDURE `serachStudentBySnameAndSage`(in _name varchar(20), in _age smallint, out _count int)
COMMENT '根据学生姓名和年龄查询学生数量！检查存在性！'
SQL SECURITY DEFINER
MODIFIES SQL DATA
BEGIN
 select _name, _age;
 select count(*) into _count from Remix.Student where Sname = _name;
END //

DELIMITER ;
```

调用存储过程！
```sql
set @count = 0;
call Remix.serachStudentBySnameAndSage('李强', 18, @count);
select @count;
```


### [3. 创建存储函数](#)
存储函数和存储过程一样，都是在数据库中定义一些 SQL 语句的集合。存储函数可以通过 return 语句返回函数值，主要用于计算并返回一个值。而存储过程没有直接返回值，主要用于执行操作。

```sql
use Remix; 

drop function if exists getStudentCountByAge;

DELIMITER //
create function getStudentCountByAge( age int) 
RETURNS int
READS SQL DATA
BEGIN
set @mySum = 0;
select count(*) into @mySum from Student where Sage = age;
return(@mySum);
end //
DELIMITER ;
```

调用存储函数
```sql
select getStudentCountByAge(20) as count;
```

#### [3.1 查看修改删除存储函数](#)
查看存储函数的语法如下：
```sql
SHOW FUNCTION STATUS LIKE function_name;


SHOW CREATE FUNCTION function_name;


SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=function_name;
```

修改存储函数的语法如下：

```sql
ALTER FUNCTION function_name [ characteristic ... ]
```
删除存储过程的语法如下：

```sql
DROP FUNCTION [ IF EXISTS ] function_name
```