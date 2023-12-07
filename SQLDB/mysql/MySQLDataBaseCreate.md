### [MySQL 数据库创建](#)
创建Mysql 数据库看似是一件很轻松的事情,可能仅仅是一条语句罢了,但是里面却又很多的学问

----


- [x] [1. 查看已有数据库](#1-查看已有数据库)
- [x] [2. 直接创建数据库](#2-直接创建数据库)
- [x] [3. 字符集问题](#3-字符集问题)
- [x] [4. 删除数据库](#4-删除数据库)
- [x] [5. 存储引擎](#5-存储引擎)


#### [1. 查看已有数据库](#) 

通过一条 命令就可以查看了 Mysql 默认带一些数据库 里面存储着数据库信息和 用户权限信息 <br/>

```mysql
show databases
```

```sql
show databases;

/*
information_schema
mysql
performance_schema
sys*/
```
information_schema:主要存储系统中的一些数据库对象信息，比如用户表信息、列信息、权限信息、字符集信息、分区信息等。每个用户都可以查看这个数据库，但根据权限的不同看到的内容不同。  
performance_schema:MYSQL5.5 引入的系统库，用于存储系统性能相关的动态参数表。  
sys:MySQL 5.7引入的系统库，本身记录系统数据，基于information_schema和performance_schema之上。封装了一层更加易于调优和诊断的系统视图。  
mysql:存储系统的用户权限信息。  

##### 1.1 查看数据库创建语句

```mysql
show Create database database_name
```

例子：
```mysql
show create database sakila;

'sakila', 'CREATE DATABASE sakila /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */'
```

##### 1.2 选择数据库
如果你要操作一个数据库，那么首先你要选择一个数据库。

```mysql
use database_name;
```

#### [2. 直接创建数据库](#)
创建一个数据库 并指定数据集为utf-8  如果不指定字符串,  那么结果就会使用意大利的字体,然后无法存储中文,会很麻烦  If 判断,方式数据库名称重复冲突 CREATE SCHEMA 是 create dataBase的代名词！ 

请替换 [ _your_dataBase_name ] 为你的数据库名称 推荐使用 utf8mb4 而不再是utf-8。 以下语句已经帮你解决很多问题了。如果不想自找麻烦。记住这个就行了。
```mysql
create dataBase if Not exists [ _your_dataBase_name ] charset utf8mb4 collate utf8mb4_bin; 

use _your_dataBase_name; 
SET SQL_SAFE_UPDATES = 0; #防止只能主键查询或更新
```
因为MySql运行在safe-updates模式下，该模式会导致非主键条件下无法执行update或者delete命令。show variables like ‘SQL_SAFE_UPDATES’;查看开关状态。执行命令SET SQL_SAFE_UPDATES = 0;修改下数据库模式

创建一个用户,把这个数据库的权限分配给他,以此作为数据库开发的指定使用用户  
为保护和减少对于root账户的使用,防止攻击  

```mysql
Create User '[ _your_user_name ]'@'%' Identified by 'root';
```
* host为可以登录的主机地址，如果任何主机都可以，设置为%
* CREATE USER 'username'@'host' IDENTIFIED BY 'password';

把这个数据库的所有权限分配给这个用户

```mysql
grant all on [ _your_dataBase_name ].* to '[ _your_user_name ]'@'%';
-- 你也可以分配部分的权限
```
##### [3. 字符集问题](#)
现在我们简单的了解一下。utf8 和 utf8mb4 charset 和 collation 有多个级别的设置：服务器级、数据库级、表级、列级和连接级 

查看字符集.下面两个方法都可以这样！
```mysql
show variables like 'collation_%';
show variables like'%character%';
```

##### 3.1 charset
charset 指定字符集,说明字符的编码格式。例如 utf8, ascii，unicode等,由于历史原因，mysql的utf8编码并不是标准的utf8，它只用3个字节表示一个Unicode字符，这不能表示表情图标。这样的emoj字符，之后又添加了一个utf8mb4作为标准的utf8。

##### 3.2 collation
[collation] 校对，核对；整理, 我们不但需要给每个字符分配编码，让它们能存储、能传输，还需要定义一套关系来组织它们，找到它们之间的联系。这套关系的定义，就是collation。
SQL中的Collation，描述了如何对查询出来的数据进行比较和排序，本质是定义了两个cell的数据进行比较的时候的compare算法。

collation定义了哪个字符和哪个字符是等价的，比如指定“大小写不敏感”，那么a和A就是等价的，这样查找时就会方便很多。世界上还有许多文字，有的有不同的音调，指定不区分音调
时，e、ē、é、ě、è就是等价的，我们去查找e，也会找到ē、é、ě、è。以utf8mb4_0900_ai_ci为例，utf8mb4指字符集编码格式，0900指的是Unicode的9.0版本，ai指accent insensitivity，即 
“不区分音调”，而ci表示case insensitivity，也就是“不区分大小写”。utf8mb4_unicode_ci类似。在MySQL8之后，默认的character和collation是utf8mb4和utf8mb4_0900_ai_ci。


##### 3.3 特别说明几个SQL Collation常见词汇： 
* ci ：case ignore/insensitive，比较的时候不区分大小写
* mb4:  multi-bytes-4，4字节字符集
* utf8mb4_unicode_ci： utf8mb4表示编码规则为支持4个字节字符的utf8，unicode表示采用UNICODE编码（UNICODE编码将几乎地球上所有字符都赋予一个数字编号）。


##### 3.4 修改字符集
一般来说我们在创建数据库时就应该指定它的characterset和collation

```mysql
CREATE DATABASE [_your_dataBase_name]  CHARACTER SET utf8mb4  COLLATE utf8mb4_bin 
```
修改字符集
```mysql
alter database [_your_dataBase_name] character set utf-8;
```

##### 3.5 为什么要使用utf8mb4字符集
既然utf8应付日常使用完全没有问题，那为什么还要使用utf8mb4呢? 低版本的MySQL支持的utf8编码，最大字符长度为 3 字节，如果遇到 4 字节  
的字符就会出现错误了。三个字节的 UTF-8 最大能编码的 Unicode 字符是 0xFFFF，也就是 Unicode 中的基本多文平面（BMP）。也就是说，任何  
不在基本多文平面的 Unicode字符，都无法使用MySQL原有的 utf8 字符集存储。这些不在BMP中的字符包括哪些呢？最常见的就是Emoji 表情（Emoji  
是一种特殊的 Unicode 编码，常见于 ios 和 android 手机上），和一些不常用的汉字，以及任何新增的 Unicode 字符等等。那么utf8mb4比utf8多  
了什么的呢?多了emoji编码支持.如果实际用途上来看,可以给要用到emoji的库或者说表,设置utf8mb4.比如评论要支持emoji可以用到。  

##### 3.6 一些 collation
* utf8mb4_bin：将字符串每个字符用二进制数据编译存储，区分大小写，而且可以存二进制的内容。
* utf8mb4_general_ci：ci即case insensitive，不区分大小写。没有实现Unicode排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致。但是，在绝大多数情况下，这些特殊字符的顺序并不需要那么精确。
* utf8mb4_unicode_ci：是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序，Unicode排序规则为了能够处理特殊字符的情况，实现了略微复杂的排序算法。0
* utf8mb4_general_ci:是一个遗留的 校对规则，不支持扩展，它仅能够在字符之间进行逐个比较。utf8_general_ci校对规则进行的比较速度很快，但是与使用 utf8mb4_unicode_ci的校对规则相比，比较正确性较差。


#### [4. 删除数据库](#)
```mysql
drop database database_name
```



####  [5. 存储引擎](#) 
MYSQL 具有存储引擎 但是不是为数据库这个单位制定 而是制定给表 例如为用户表制定 memory 存储引擎



```mysql
create table users
(
    id smallint unsigned not null auto_increment,
    username varchar(15) not null,
    pwd varchar(15) not null,
    index using btree (username),
    primary key (id)
)engine=memory;
```



#####  5.1 数据库存储引擎
```mysql
show engines
```

* 设置InnoDB为默认引擎：
  * 在配置文件my.cnf中的 [mysqld] 下面加入 default-storage-engine=INNODB 一句


------
