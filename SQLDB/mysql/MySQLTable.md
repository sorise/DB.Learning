### [MySQL 数据表的基本操作](#)
`创建Mysql 数据库看似是一件很轻松的事情,可能仅仅是一条语句罢了,但是里面却又很多的学问，由于MySQL的图形化界面，支持性并不类似于SQL SERVER一般好。所以学习怎么使用命令去
操作数据库是十分必要的！ MySQL是没有 Schema的，也可以说 Schema等价于 数据库。 这里我们将学习 DDL语言`

----

- [ ]  [`1. SQL 语言的分类`](#1-SQL-语言的分类)
- [ ]  [`2. 创建表的语法`](#2-创建表的语法)
    - [`2.1 查看表结构`](#21-查看表结构)
    - [`2.2 删除表`](#22-删除表)
- [ ] [`3. Constraint MySQL约束`](#3-constraint-mysql约束)
    - [`3.1 主键约束`](#31-主键约束)
    - [`3.2 外键约束`](#32-外键约束)
    * [`3.3 非空约束`](#33-非空约束)
    * [`3.4 唯一性约束`](#34-唯一性约束)
    * [`3.5 默认约束`](#35-默认约束)
    * [`3.6 自动增长`](#36-自动增长)
    * [`3.7 check 约束`](#37-check-约束)
- [ ] [`4. 修改表结构`](#4-修改表结构)
    * [`4.1 修改表名称`](#41-修改表名称)
    * [`4.2 修改字段数据类型`](#42-修改字段数据类型)
    * [`4.3 修改字段名称`](#43-修改字段名称)
    * [`4.4 添加字段`](#44-添加字段)
    * [`4.5 删除字段`](#45-删除字段)
    * [`4.6 修改字段排列顺序`](#46-修改字段排列顺序)
- [ ]  [`5.表的存储引擎`](#5-表的存储引擎)


---


#### [1. SQL 语言的分类](#)
`SQL[结构化查询语言 Structure Query Lnanguage]语言，主要被分为三类.`

`1`. `DDL [Data Definition Lnanguage]语句，数据定义语言，这些语言定义不同的数据段，数据库，表，列，索引等数据库对象。常用的语句关键是包括， create，drop,alter。`  
`2`. `DML [Data Manipulation Lnanguage]语句，数据操纵语言。用于添加，删除，更新和查询数据库记录，并检查数据完整性。常用的语句关键字包括，insert，update，delete，select。 `  
`3`. `DCL [Data Control Lnanguage]语句, 数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库，表，字段，用户的访问权限和安全级别。常用的语句关键字主要包括grant，revoke等。`  



#### [2. 创建表的语法](#)
`语法规则 很简单.`  `名称在前 类型随后 约束在右 [名类约 跟括号]`  `注意创建表之前要先选中数据库.` `show create table tableName` `可以查看创建表时候使用的语句。`

```sql
use Remix;

create table user(
	Uid int primary key auto_increment, -- 主键约束 自增长
    Name varchar(50),   -- 无约束
    Age int default 18  -- 默认值 18
)engine=InnoDB;


INSERT into user (Name, Age)  values('Jx', 18);
INSERT into user (Name, Age)  values('jx', 28);
INSERT into user (Name, Age)  values('Ljx', 38);
INSERT into user (Name, Age)  values('lJx', 21);


select * from user where Name ='Jx';

show create table user;
```

##### [2.1 查看表结构](#)
`查看基本表结构：` `Describe 表名` `简写:DESC 表名`  `查看表的详细结构`： `Show Create table table_name`

```sql
Desc Teacher;
/*
Field, Type, Null, Key, Default, Extra
'TeaherID', 'int(11)', 'NO', 'PRI', NULL, 'auto_increment'
'CollegeID_', 'int(11)', 'YES', 'MUL', NULL, ''
'TeacherName', 'varchar(100)', 'NO', '', NULL, ''
'TeaherAge', 'datetime', 'NO', '', NULL, ''
*/
```
`查看表的详细结构`：`Show Create table table_name`

```sql
show create table Teacher;
/*
CREATE TABLE `teacher` 
(`TeaherID` int(11) NOT NULL AUTO_INCREMENT,
`CollegeID_` int(11) DEFAULT NULL,
`TeacherName` varchar(100) NOT NULL,
`TeaherAge` datetime NOT NULL,
PRIMARY KEY (`TeaherID`),
KEY `fk_teacher_to_colleage` (`CollegeID_`),
CONSTRAINT `fk_teacher_to_colleage` FOREIGN KEY (`CollegeID_`) 
REFERENCES `college` (`collegeid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
*/
```
##### [2.2 删除表](#)
`删除表的语法很简单哦！`

```sql
drop table tableName;

-- 例子删除多个表
drop table Student, t3, t5;
```


#### [3. Constraint MySQL约束](#)
`按道理来说，MySQL应该是有七种约束的。主键、外键、非空约束、唯一性约束、默认约束、自增约束、check约束。` `并且给一个表附加约束有多种方法，一个是在创建表的时候。他又分为在两种。一种是在某个字段后面给这个字段添加约束。
或者在所有字段的最后添加约束。又或者说在创建表格以后再添加约束。添加约束后再修改，删除。这是一个坑底的过程，当然通过图形化访问去操作约束当然是更好的。通过命令有些难啊！`

##### [3.1 主键约束](#)
`表示每一行都应该有可以唯一标识自己的一列。例如学号，表单号等等...` `按道理说每一个表都应该有一个主键。` `主键可以由一列来确认。也可以由几列来确认` `但是在实践中我们推荐只由一列来确认。` 

`1`.`主键是唯一的，任何两行不具有相同的主键值。`  
`2`.`主键是非空的，他不能为null。 也就是说主键本身也有非空约束。主键其实是包含非空约束，所以想要非空 ，不必要添加 not null 约束了`  
`3`.`主键一旦建立，就会同时为主键列建立索引`  
`实践的好习惯：` `1. 不推荐更新主键列中的值` `2. 不在主键中使用可能会更改的值`

`创建表的时候添加主键约束`:`如果主键只有一个字段` `直接 Primary Key`
```sql
-- 住宅表
create table house(
	HouseId int primary key,
    Name varchar(100),
    RealAddress nvarchar(200)
)engine=InnoDB;
```
`创建表的时候添加约束`:`如果主键有多个约束` `[CONSTRAINT <约束名>] PRIMARY KEY [字段名]`
```sql
-- 学生选课表
create table CS(
   courseId int,
   studentId varchar(32),
   state int,
   constraint pk primary key(courseId,studentId)  -- 等价于 primary key(courseId,studentId) [推荐这个]
)
```
`创建表删除主键约束`
```sql
alter table house drop primary key;
```

`创建表后再增加主键约束！`:`alter table tableName add primary key(column_name,column_name...);`
```sql
alter table house add primary key(HouseId);
```

##### [3.2 外键约束](#)
`外键用来在两个表的数据之间建立链接。他可以是一列或者多列。一个表可以有多个外键，但是名称不可以相同，外键属于参照完整性。保证数据的一致性、完整性。 ` 

`1`.`一个表的外键可以为空值 null。`  
`2`.`一个表的外键值必须等于另一个表中主键的某个值。`

`外键 一定要求两个表的字段 类型相同 链接出去的外键必须是主键  例如下面 CollegeID 是主键` `子表的外键必须关联父表的主键`

`语法`
```sql
constraint foreign_key_name foreign key(column_name) references table_Name(column_name)
```

```sql
create table College(
	CollegeID int primary key,
    Name varchar(50) 
);

create table Student(
   StudentID varchar(30) primary key,
   Name varchar(30),
   Age  int default 18,
   Sex boolean,
   CollegeID int,
   constraint fk_student_to_colleage foreign key(CollegeID)  references College(CollegeID) 
);
```
`创建表之后添加 外键约束 `
```sql
ALTER TABLE Teacher ADD CONSTRAINT fk_student_to_colleage FOREIGN KEY(CollegeID) REFERENCES College(`CollegeID`); 
```

`删除外键`
```sql
alter table tableName drop foreign key foreign_key_name;
```


##### [3.3 非空约束](#)
`非空约束指使得某个字段不能为空，对于使用了非空约束的字段，如果用户在添加数据的时没有指定值，数据库就会报错。 对非空约束的修改需要借助 modify。 modify操作可以更改字段的数据类型。
我们是在修改数据类型的同时修改非空约束的。`

`在创建表的时候添加`
```sql
create table College(
    CollegeID int primary key unique auto_increment,
    Name varchar(100) not null,
    Status int 
);
```
`创建后添加`
```sql
alter TABLE College modify Name varchar(100) not null;

insert into college values(2, null); --会报错
```

`删除非空约束`
```sql
alter TABLE College modify Name varchar(100);
```

##### [3.4 唯一性约束](#)
`唯一性约束 Unique Constrain 要求该列唯一，允许为空，但只能出现一个空值。唯一约束可以确保一列或者几列不出现重复值。` `唯一约束也是索引. 你给一个唯一约束，DB就是给建立一个索引。所以你可能很奇怪
为啥它是使用删除索引的防止，删除唯一约束的。`  `auto_increment 自动增长列 必须要求瞒住 unique 约束`

`unique 和 主键的区别 在与主键 不允许空值 unique  允许一个空值  主键一个表只有一个  而unique 可以有多个`  
`主键本身 unique 所以你懂的 你给主键 添加unique 没有用`  

```sql
column_name data_type UNIQUE;
```

```sql
create table College(
    CollegeID int primary key unique auto_increment,
    Name varchar(100) unique not null,
    Status int 
);
```
`在创建表的时候最后添加唯一约束`

```sql
create table College(
    CollegeID int primary key unique auto_increment,
    Name varchar(100) unique not null,
    Status int,
    constraint unique_only_name Unique(Name)
);
```
`建表之后`:`还是使用 modify`
```sql
alter table College modify  Name varchar(100) unique not null,;
```
`创建表之后删除唯一约束`
```sql
alter table College drop index unique_only_name;
```


##### [3.5 默认约束](#)
`默认约束就是指定某列的默认值。例如设置性别默认为男，如果插入一条新的记录没有为这个字段复制，那么系统就会自动为这个字段赋值为男。` `你这个默认值啊，要和数据类型一致。`

`语法`
```
字段名称 数据类型 Default 默认值
```

```sql
create table lessons(
    lessonID varchar(40) primary key,
    Name varchar(200) default '系统未添加 占位',
    status int default 3
);
```
`创建后设置默认值`:`还是要用modify 这个东西。`

```sql
alter table lessons modify status int  default 3;
```

`取消默认值`
```sql
alter table lessons modify status int;
```

##### [3.6 自动增长](#)
`数据库应用中，自增长是非常常用的。通常我们只能给主键、unique约束的字段添加 自动增长约束。但是` `AUTO_INCREMENT` `约束的字段可以是任何整数类型 (tinyint, smallin, int, bigint等)。`
`默认自增字段从1 开始 每次添加一条记录 该值自动加 1。`

```sql
teaherID int primary key auto_increment;
```

`建表之后增加`:`modify 和 change 都可以使用。但是我们还是推荐modify`
```sql 
alter table Teacher modify teaherID INT(10) AUTO_INCREMENT; -- modify

alter table Teacher change teaherID teaherID INT(10) AUTO_INCREMENT;
```

`删除自增长`
```sql
alter table t_user modify user_id INT(10);

alter table t_user change user_id user_id INT(10);
```

##### [3.7 check 约束](#)
`这个玩意儿啊，是一个检查约束，要求数据需要满足一定的要求。`  `CHECK(<检查约束>)`

```sql
create table Person(
  PID int primary key,
  Sex char(2) check(Sex in ('男','女')),
  Age tinyint(4) NOT NULL DEFAULT '20' CHECK (Age between 15 and 30)
);
```

`创建表的结尾添加`
```sql
create table Person(
  PID int primary key,
  Sex char(2),
  Age tinyint(4) NOT NULL DEFAULT '20',
  check(Sex in ('男', '女')),
  check(Age between 15 and 30)
);
```

`建表后`
```sql
alter table tableName add constraint CK_name_constraint check (条件表达式) --条件表达式中的条件用关系运算符连接
```

`删除约束`
```sql
alter table tableName drop constraint CK_name_constraint;
```

#### [4. 修改表结构](#)
`留出返回空间，和后期对于前期数据库设计不足的弥补。我们应该拥有对数据表结构修改的能力。 常用的操作有 修改表的名称(危害小，不改变表结构)、 修改字段名称(危害也小)、修改字段类型(如果
表中已经存在数据，那么新的类型需要兼容过去的数据类型，例如 tinyint 到 int)、增加字段、删除字段。 修改字段的排列位置，表的存储引擎。修改表的外键约束等等` 

##### [4.1 修改表名称](#)
`修改表名称`:`alter table 旧表名 rename to 新名称 `

```sql
Create table Lessons(
	lessonID int primary key auto_increment,
    lessonName varchar(100) not null,
    lessonStatus int default 40
);

alter table Lessons rename to Lesson;
```

##### [4.2 修改字段数据类型](#)
`修改字段数据类型`:`alter table 表名 modify 字段名称 数据类型` `这也可以用change 把名字写两遍。但是这太恶心了。还是用modify。`

```sql
alter table Lesson modify lessonStatus tinyint;
```



##### [4.3 修改字段名称](#)
`alter table <表名> change 旧字段名称 新字段名称 <新数据类型>` `change 可以一键修改两个内容 字段名称 数据类型`

```sql
alter table Lesson change  lessonStatus status tinyint(4) not null;
```


##### [4.4 添加字段](#) 
`alter table <表名> add 新字段名称 数据类型 [not null / default value]`

```sql
-- 放在第一列
alter table Lesson add column lessonAdministrator int not null first;

-- 放在 列lessonName 之后
alter table Lesson add column lessonAdministrator int not null after lessonName;
```
##### [4.5 删除字段](#)  
`alter table <表名> drop 字段名称`

```sql
alter table Lesson drop lessonAdministrator;
```

##### [4.6 修改字段排列顺序](#)   
`alter table <表名> modify 字段名称 数据类型 first|after 字段 ` `把某一个字段 放在什么字段之前之后，我感觉这好像就没什么意义。 就是在修改的时候要把约束和数据类型再写一遍。
我强烈不推荐去修改字段的排列顺序。万一数据类型，约束写错了，后果不堪设想。`

* `first`:`放在第一个`
* `after 字段`:`放在字段之后`

```sql
alter table student modify CollegeID int  after StudentID;

alter table lesson modify lessonAdministrator  int not null  after lessonStatus;
```

##### [5. 表的存储引擎](#)
`修改表的存储引擎 可以在创建表的时候指定存储引擎`
* `show engines; 查看存储引擎`
* `select version(); 查看系统版本`

```sql
create table Student(
	StudentID varchar(40) primary key,
	StudentName varchar(60) ,
	StudentPassword varchar(200),
	constraint unique_key_name UNIQUE(StudentID)
)engine = Innodb;

alter table  table_name Engine = MyisAM 
```
