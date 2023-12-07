### [MySQL 索引](#)
`索引是数据库表中一列或多列进行排序的一种结构，使用索引可以提高数据库中特定数据的查询效率。要掌握索引的含义和特点、索引的分类、索引的设计原则以及如何创建和删除索引`

-----
- [x] [`1. 概述`](#1-索引概述)
- [x] [`2. 索引的分类`](#2-索引的分类)
- [x] [`3. 设计原则`](#3-设计原则)
- [x] [`4. 创建表的时候创建索引`](#4-创建表的时候创建索引)
- [x] [`5. 在已经存在的表上创建索引`](#5-在已经存在的表上创建索引)
- [x] [`6. 删除索引`](#6-删除索引)
-----

#### [1. 索引概述](#)
`如果你对数据结构和操作系统、计算机网络一点也不了解！这学习有些困难！` **索引的基础是B-数和哈希！** `只有充分理解它们才能充分理解索引！` 

**索引是一个单独的、存储在磁盘上的数据库结构，它们包含着对数据表里所有记录的引用指针。使用索引用于快速找出在某个或多个列中有一特定值的行，所有MySQL 列类型都可以被索引，对相关列使用索引是提高查询操作速度的最佳途径。**

`索引用于快速找出在某个列中有一特定值的行。不使用索引，MySQL 必须从第1条记录开始读完整个表，直到找出相关的行。表越大，查询数据所花费的时间越
多。如果表中查询的列有一个索引，MySOL 能快速到达某个位置去搜寻数据文件，而不必查看所有数据。`

**索引是在存储引擎中实现的，因此，每种存储引擎的索引都不一定完全相同，并且每种存储引擎也不一定支持所有索引类型。**`根据存储引擎定义每个表的最大索引数
和最大索引长度。所有存储引擎支持每个表至少16个索引，总索引长度至少为256字节。大多数存储引擎有更高的限制。MySQL 中索引的存储类型有两种∶BTREE 和 HASH，具
体和表的存储引擎相关;` **MyISAM和InnoDB存储引擎只支持BTREE索引;MEMORY/HEAP存储引擎可以支持HASH 和BTREE 索引。**

##### [1.1 索引的好处](#)
1. `通过创建唯一索引，可以保证数据库表中每一行数据的唯一性。` **给主键加索引，给唯一列加索引**
2. `可以大大加快数据的查询速度，这也是创建索引的最主要的原因。` **给经常查询的列加索引**
3. `在实现数据的参考完整性方面，可以加速表和表之间的连接。` **给外键加索引**
4. `在使用分组和排序子句进行数据查询时，也可以显著减少查询中分组和排序的时间。` **分组，排序的列加索引**

##### [1.2 索引的坏处](#)
`增加索引也有许多不利，主要表现在如下几个方面∶`
1. `创建索引和维护索引要耗费时间，并目随着数据量的增加所耗费的时间也会增加。`
2. **索引需要占磁盘空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果有大量的索引，索引文件可能比数据文件更快达到最大文件尺寸。**
3. **当对表中的数据进行增加、删除和修改的时候，索引也要动态地维护** `，这样就降低了数据的维护速度。`



#### [2. 索引的分类](#) 
`MySQL支持四类索引`  `1.普通索引和唯一索引` `2.单列索引和组合索引` `3. 全文索引`  `4. 空间索引。`

##### 2.1 普通索引
`普通索引是 MySQL 中的基本索引类型，允许在定义索引的列中插入重复值和空值。`

`唯一索引，索引列的值必须唯一，但允许有空值。 如果是组合索引，则列值的组合必须唯一。主键索引是一种特殊的唯一索引，不允许有空值。`

##### 2.2 单列索引和组合索引
`单列索引即一个索引只包含单个列，一个表可以有多个单列索引。组合索引指在表的多个字段组合上创建的索引，只有在查询条件中使用了这些字段的左边字段时，索引才会被使用。使用组合索引时遵循最左前缀集合。`

##### 2.3 全文索引
`全文索引为FULLTEXT，在定义索引的列上支持值的全文查找，运行在这些索引列中引入重复值和空值。全文索引可以在CHAR, VARCHAR, TEXT类型的列上创建。只有MyISAM存储引擎支持全文索引！`

##### 2.4 空间索引
`对空间数据类型字段建立的索引，MYSQL支持的空间数据类型由四种:` `GEOMETRY、POINT、LINESTRING、POLYGON。` `MySQL 使用SPATIAL关键字`

#### [3. 设计原则](#)
* `索引并非越多越好，大量的索引会占用磁盘空间，会影响Insert、Delete、Update等语句的性能！因为当表中数据修改的时候，索引也会进行调整和更新！`
* `避免对经常更新的表进行过多的索引，并且索引中的列尽可能的少。对于经常查询的字段创建索引`
* `表中数据量少的表最好不要用索引，由于数据量少，查询花费可能比遍历索引还要短，索引可能不会产生优化效果` 
* `在条件表达式中经常用到的不同值较多的列上建立索引，在不同值很少的列上不要建立索引。比如在学生表的"性别"字段上只有"男"与"女"两个不同值，因此就无须建立索引。如果建立索引不但不会提高查询效率，反而会严重降低数据更新速度。`
* `唯一列建立唯一索引`
* `频繁排序或分组的列建立索引`

#### [4. 创建表的时候创建索引](#) 
`索引可以在创建表的时候一起创建(create table 中指定索引)，也可以之后再创建！(create index)`
`对于主键,具有唯一约束的列，MySQL会自动创建为它创建一个唯一索引！`

##### 4.1 基本语法
`easy! 基本语法`
```sql
Create Table table_name(
    [column_name datatype constraint]
    [UNIQUE | FULLTEXT | SPATIAL] [INDEX | KEY] [index_name] (column_name [length]) [ASC|DESC]
) 
```
`UNIQUE | FULLTEXT | SPATIAL`:`可选参数。分别用来指定唯一索引、全文索引、空间索引`  
`INDEX 和 KEY 是同义词！一个意思都是创建索引`  
`index_name`:`索引名字`  
`column_name`：`在那些字段上创建索引`  
`length`:`可选，指定索引的长度，只有字符串类型的字段才能指定索引长度`  
`[ASC|DESC]`:`指定升序或者降序的索引值存储！`  

##### 4.1.1 创建普通索引
`最基本的所有类型，没有唯一性之类的限制，其作用只是加快对数据的访问速度！`

```sql
create table Student(
	SID varchar(20) primary key,
    Name varchar(50) not null,
    age smallint unsigned,
    sex boolean,
    collegeID int,
    ID varchar(40),
    INDEX  collegeIDIndex(collegeID)  -- 可以简写为: INDEX(collegeID)
)
```

##### 4.1.2 创建一个唯一索引
```sql
create table Student(
	SID varchar(20) primary key,
    Name varchar(50) not null,
    age smallint unsigned,
    sex boolean,
    collegeID int,
    ID varchar(40) unique,
    unique INDEX(ID) -- 可以给索引起名字UniqueID： unique INDEX  UniqueID(ID) 
)
```

##### [4.1.3 创建组合索引](#)
`组合索引可以起到几个索引的作用，但是使用时并不是随便查询哪个字段都可以使用索引，而是遵从 “最左前缀”： 利用索引中最左边的列集来匹配行，这样的列集称为最左前缀。`
```sql
create table t3(
	id int not null,
    name char(30) not null,
    age int not null,
    info varchar(255),
    Index MultiIndex(id, name, age)
);
```

##### [4.1.4 创建全文索引](#)
`FULLTEXT（全文索引）可以用于全文搜索。只有MyISAM存储引擎支持FULLTEXT索引,并且CHAR,VARCHAR和TEXT列创建索引。 索引总是对整个列进行，不支持局部(前缀)索引。`

```sql
create table t4(
	id int not null,
    name char(30) not null,
    age int not null,
    info varchar(255),
    FULLTEXT INDEX FullTextIdxInfo(info)
) engine=MyISAM;
```

##### [4.1.5 创建空间索引](#)
`用得较少啊！`
```sql
create table t5(
	G geometry NOT NULL,
    spatial index spatialIdx(g)
)engine=MyISAM;

```

#### [5. 在已经存在的表上创建索引](#) 
`这就很常见了，一般来说很少在创建表的时候就创建索引！`


##### 5.1 使用 alter table 语句
`首先我们先用alter table创建索引！`
```sql
Alter Table table_name add [UNIQUE | FULLTEXT | SPATIAL] [INDEX | KEY] [index_name] (column_name [length]) [ASC|DESC];
```

`创建一个唯一索引！`
```sql
create table Student(
	SID varchar(20) primary key,
    Name varchar(50) not null,
    age smallint unsigned,
    sex boolean,
    collegeID int,
    ID varchar(40)
);

alter table Student Add unique index IDIndex(ID);
```

##### 5.2 使用 create index 语句
`创建语法！`
```sql
create [Unique | FULLTEXT | SPATIAL] INDEX index_name
on table_name(col_name[length],...) [ASC | DESC]
```

```sql
create table Student(
	SID varchar(20) primary key,
    Name varchar(50) not null,
    age smallint unsigned,
    sex boolean,
    collegeID int,
    ID varchar(40) unique
);

create unique index id_idx on Student(ID);
```

#### [6. 删除索引](#)
`删除索引有两种方法：Alter table 和 Drop Index`

```sql
alter table Student drop index id_idx;
```

`下面的好记一点！`
```sql
drop index id_idx on Student;
```

#### [7. 查看索引](#)
`查看索引非常简单！`

```sql
show index from Student;
```

-----
`时间`: `[2022/3/29]` 
