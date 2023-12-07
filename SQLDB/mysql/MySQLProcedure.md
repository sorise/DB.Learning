### [MySQL 存储过程](#)
`存储过程是一条或者多条SQL语句的集合，可视为批处理文件，它是旧时代的产物！ 可以在存储过程中定义变量使用游标，封装业务逻辑。`

-----
- [x] [1. 存储过程概述](#1-存储过程概述)
- [x] [2. 创建存储过程](#2-创建存储过程)
- [x] [3. SQL编程](#3-sql编程)

-----

### [1. 存储过程概述](#)
存储程序可以分为存储过程和函数，MySOL 中创建存储过程和函数使用的语句分别是; `CREATE PROCEDURE` 和 `CREATE FUNCTION`。使用 `CALL` 语句来调用存储过程，只能用输出变量返回值。函数可以从语句外调用（即通过引用函数名），也能返回标量值。存储过程也可以调用其他存储过程。
MySQL创建存储过程需要 `delimiter $$` 的配合，因为MySQL默认是`;` 结尾的！

```sql
delimiter $$
CREATE PROCEDURE sp_customers(IN cusid INT)
BEGIN
SELECT * FROM customers WHERE`客户ID`=cusid;
end $$
delimiter ;
```
1. `sp_customers` 存储过程名字
2. `IN` 输入参数  输出参数关键字 `OUT`，同时也可以不定义参数，直接让参数为空。
3. `cusid INT` 参数名字 和类型 `int` 类型 
4. `BEGIN ... END` ：这是存储过程过程体的固定语法，你需要执行的SQL功能就写在这中间。

为什么会需要存储过程?
1. 数据库单机时代的产物，可以封装复制的操作，将业务逻辑卸载存储过程，减少与数据库的链接次数。
2. 提高性能，使用存储过程比多次使用单独的SQL语句更快，处理直接在数据库中。而不需要转到后端 `server` 处进行，减少了数据流量
3. 更加安全，限制了对基础数据的访问，减少了数据错误！

下面我们来调用上面的存储过程sp_customers
```sql
CALL sp_customers(1);
```

#### [1.1 查看存储过程](#)

1. 查看所有存储过程： 
```sql
SHOW PROCEDURE STATUS;
```
2. 查看指定数据库中的存储过程： 
```sql
SHOW PROCEDURE STATUS WHERE DB = 'publiccms';
```
3. 查看指定存储过程源码 ： 
```sql
SHOW CREATE PROCEDURE show_emp;
````
4. 删除指定存储过程/存储函数： 
```sql
DROP PROCEDURE show_emp;
DROP FUNCTION [ IF EXISTS ] FunctioName
```

### [2. 创建存储过程和函数](#)
`MySQL`使用关键字 `CREATE PROCEDURE` 创建存储过程。 在过程体里面写我们的SQL语句。 过程体包含DML、DDL语句，if-then-else和while-do语句、声明变量的declare语句等。 
使用关键字 `CREATE FUNCTION` 来创建关键字。

创建存储过程函数
```mysql
CREATE PROCEDURE 存储过程名字([存储过程参数])
[存储过程特性]
BEGIN
 /* 过程体 */
END;
```

创建存储函数的基本语法格式：
```mysql
CREATE FUNCTION sp_name ([func_parameter[...]])
RETURNS type
[characteristic ...] routine_body
```
* `sp_name` 参数：表示存储函数的名称；
* `func_parameter`：表示存储函数的参数列表；
* `RETURNS type`：指定返回值的类型；
* `characteristic`: 参数：指定存储函数的特性，该参数的取值与存储过程是一样的；
* `routine_body`: 参数：表示 SQL 代码的内容，可以用 BEGIN...END 来标示 SQL 代码的开始和结束。

查看存储函数的语法如下：

```sql
SHOW FUNCTION STATUS LIKE 存储函数名;
SHOW CREATE FUNCTION 存储函数名;
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=存储函数名;
```
可以发现，操作存储函数和操作存储过程不同的是将 PROCEDURE 替换成了 FUNCTION。同样，修改存储函数的语法如下：
```sql
ALTER FUNCTION 存储函数名 [ 特征 ... ]
```
#### [2.1 存储过程特性](#) 
1.  `COMMENT'string'`∶注释信息，可以用来描述存储过程或函数。
2. `LANGUAGE SQL`∶说明 `routine body` 部分是由 `SQL`语句组成的，当前系统支持的语言为 SQL，SQL 是 LANGUAGE特性的唯一值。
3. `[NOT]DETERMINISTIC`∶指明存储过程执行的结果是否确定。`DETERMINISTIC` 表示结果是确定的。每次执行存储过程时，相同的输入会得到相同的输出。`NOT DETERMINISTIC` 表示结果是不确定的，相同的输入可能得到不同的输出。如果没有指定任意一个值，默认为 `NOT DETERMINISTIC`。
4. `CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA`：指明子程序使用SQL语句的限制。
    4.1 `CONTAINS SQL` 表明子程序包含SQL语句，但是不包含读写数据的语句；
    4.2 `NO SQL`表明子程序不包含SQL语句；
    4.3 `READS SQL DATA`：说明子程序包含读数据的语句；
    4.4 `MODIFIES SQL DATA`表明子程序包含写数据的语句。   
    4.5 默认情况下，系统会指定为 `CONTAINS SQL`
5. `SQL SECURITY { DEFINER | INVOKER }` ：指明谁有权限来执行。DEFINER 表示只有定义者才能执行 `INVOKER` 表示拥有权限的调用者可以执行。默认情况下，系统指定为 `DEFINER`

#### [2.2 参数](#)
存储过程的参数有三种参数类型, 需要注意的地方是，你传递给存储过程的参数如果需要用到查询，请不要和查询表中的列名一样(忽略大小写)
 
1. `IN` 输入参数：表示调用者向过程传入值（传入值可以是字面量或变量） 需要输入值时使用IN参数；
2. `OUT` 输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量） 需要返回值时使用OUT参数；
3. `INOUT` 输入输出参数：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量） INOUT参数尽量少用。

```sql
delimiter $$
CREATE PROCEDURE `pc_serachStudentBySnameAndSage`(in _name varchar(20), in _age smallint, out _count int)
begin
 select _name, _age;
 select count(*) into _count from Remix.Student where Sname = _name;
end $$
delimiter ;



set @count = 0;
call `Remix`.`pc_serachStudentBySnameAndSage`('李强', 18, @count);
select @count;
```

#### [2.3 变量](#)
MySQL中主要有几种变量， `1. 系统变量 set @@...` 变量由系统提供的，不用自定义   `2.自定义变量 set @.. / declare[必须放在 begin end里面] `   作用域：针对于当前连接（会话）生效位置

##### [2.3.1 系统变量](#)

1. 查看系统变量
```sql
show [global|session] variables like '....';
```
如果没有显式声明global还是session，则默认是session

2. 查看指定的系统变量的值
```sql
select @@[global|session].变量名;
```
如果没有显式声明global还是session，则默认是session

3. 为系统变量赋值

方式一： `set [global|session]变量名 = 值;`

如果没有显式声明global还是session，则默认是session

方式二： `set @@global.变量名=值;或者set @@变量名=值；`

##### [2.2.2 自定义变量](#)
1. 用户变量声明并赋值：

```sql
set @变量名=值;

set @变量名:=值;

select @变量名:=值;
```

更新值方式一：

```sql
set @变量名=值;

set @变量名:=值;

select @变量名:=值;
```
更新值方式二：
```sql
select xx into @变量名 from 表;
```
查询使用

```sql
select @变量名;
```

2. `局部变量` 作用域：仅仅在定义它的begin end中有效  位置：只能放在begin end中，而且只能放在第一句

使用声明
```sql
declare 变量名 类型 [default 值];
```

赋值或更新方式一：
```sql
set 变量名=值;

set 变量名:=值;

select @变量名:=值;
```
赋值或更新方式二：

```sql
select 字段名 into 变量名 from 表;
```
使用
```sql
select 变量名;
```

### [3. SQL编程](#)
MySQL 中可以使用 DECLARE 关键字来定义条件。其基本语法如下：

```sql
DECLARE condition_name CONDITION FOR condition_value
condition value:
SQLSTATE [VALUE] sqlstate_value | mysql_error_code
```

* `condition_name` 参数表示条件的名称；
* `condition_value` 参数表示条件的类型；
* `sqlstate_value` 参数和 mysql_error_code 参数都可以表示 MySQL 的错误。sqlstate_value 表示长度为 5 的字符串类型错误代码，mysql_error_code 表示数值类型错误代码。例如 ERROR 1146(42S02) 中，sqlstate_value 值是 42S02，mysql_error_code 值是 1146。

下面定义“ERROR 1146 (42S02)”这个错误，名称为 can_not_find。 可以用两种不同的方法来定义，代码如下：

```sql
//方法一：使用sqlstate_value
DECLARE can_not_find CONDITION FOR SQLSTATE '42S02';

//方法二：使用 mysql_error_code
DECLARE can_not_find CONDITION FOR 1146;
```