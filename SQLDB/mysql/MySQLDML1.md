### [MySQL DML语句](#)
`包括了正删改查，数据操作语言！`

-----

- [ ] [`1. 数据库案例`](#1-数据库案例)
- [ ] [`2. 增删改查语句`](#2-增删改查语句)
- [ ] [`3. 单表查询语句`](#3-单表查询语句)
- [ ] [`4. 链接查询`](#4-链接查询)
- [ ] [`5. 子查询`](#5-子查询)
- [ ] [`6. 集合操作`](#6-集合操作)
- [ ] [`7. 别名 as`](#7-别名-as)
- [ ] [`8. 使用正则表达式查询`](#8-使用正则表达式查询)
- [ ] [`9. explain 执行计划`](#9-explain-执行计划)

----

#### [1. 数据库案例](#)
`首先我们创建一个数据库， sakila数据集是MySQL官方提供的一个学习MySQL的很好的素材，sakila数据集包含以下23个表。` 
* `actor：演员表`
* `address：地址表，表示客户（customer），员工（staff），商店（store）的地址。`
* `category：影片的分类。`
* `city：城市信息`
* `country：国家信息`
* `customer：观看影片的用户信息`
* `film：影片信息`
* `film_actor：影片演员关联表。`
* `film_category：影片分类关联表。`
* `film_text：影片的文本信息，更新film的时候通过触发器保持film_text的同步。`
* `inventory：库存信息（某个影片在某个商店的情况）。`
* `language：影片的语言。`
* `payment：租赁付款信息。`
* `rental：某个影片库存的租赁信息。`
* `staff：商店员工信息。`
* `store：影片的商店信息。`

##### 1.1 学生选课数据库案例

```sql
create dataBase if Not exists Remix charset utf8mb4 collate utf8mb4_bin; 
use Remix;

create table Student(
	Sno char(10) primary key,
	Sname char(20) not NULL,
	Sage smallint,
	Ssex char(2),
	Sdept char(20)
);

create table Course(
	Cno char(10),
	primary key(cno),
	Cname varchar(20),
	Cpno char(10),
	Ccredit smallint
);

create table SC(
	Sno char(10),
	Cno char(10),
	grade smallint,
	primary key(Sno,Cno)
);

-- 插入数据
insert into Student values('001','李强',18,'M','CS');
insert into Student values('002','张三',18,'m','E');
insert into Student values('003','李四',19,'m','E');
insert into Student values('004','刘泽须',20,'f','CS');
insert into Student values('005','王五',18,'m','CS');
insert into Student values('006','王还',19,'m','C');
insert into Student values('007','陶子用',18,'f','CS');
insert into Student values('008','杜海',20,'m','E');
insert into Student values('009','陶磊',18,'f','C');
insert into Student values('010','诸葛亮',17,'m','CS');
insert into Student values('011','周瑜',19,'m','CS');


insert into Course (Cno,Cname,Ccredit) values('C01','数据库',4);
insert into Course (Cno,Cname,Ccredit) values('C02','C语言',4);
insert into Course (Cno,Cname,Ccredit) values('C03','数据结构',5);
insert into Course (Cno,Cname,Ccredit) values('C04','C++',3);
insert into Course (Cno,Cname,Ccredit) values('C05','操作系统',5);
insert into Course (Cno,Cname,Ccredit) values('C06','Java',5);
insert into Course (Cno,Cname,Ccredit) values('C07','英语',5);
insert into Course (Cno,Cname,Ccredit)values('C08','高数',5);
insert into Course (Cno,Cname,Ccredit) values('C09','线性_代数',5);

insert into SC values('001','C01',78);
insert into SC values('001','C02',34);
insert into SC values('001','C03',56);
insert into SC values('001','C04',69);
insert into SC values('001','C05',55);
insert into SC values('001','C06',33);
insert into SC values('001','C07',56);
insert into SC values('001','C08',35);
insert into SC values('001','C09',85);
insert into SC values('002','C02',55);
insert into SC values('002','C03',65);
insert into SC values('002','C05',88);
insert into SC values('002','C06',93);
insert into SC values('002','C07',74);
insert into SC values('003','C01',78);
insert into SC values('003','C04',45);
insert into SC values('003','C05',77);
insert into SC values('003','C06',23);
insert into SC values('003','C07',80);
insert into SC values('004','C02',96);
insert into SC values('004','C03',74);
insert into SC values('004','C05',72);
insert into SC values('004','C07',90);
insert into SC values('005','C08',65);
insert into SC values('005','C09',65);
insert into SC values('005','C05',65);
insert into SC values('005','C06',65);
insert into SC values('005','C07',65);
```

#### [2. 增删改查语句](#)
`首先创建一个表！然后插入数据！`
```sql
create table Student(
  SID varchar(18) primary key,
  Name varchar(20) not null,
  Age smallint,
  AID varchar(20) not null unique,
  sex enum('男', '女') default '男'
);
```

##### [2.1 insert](#)
`insert语句可以插入一条，也可以插入多条，可以指定列名，也可以额不指定列名！` `还可以将查询结构插入表中`

`实际开发中，写在程序中的sql, 最好写上列名，不然表结构改变后。sql语句可能出错！`
```sql
insert into tableName(columnList) values(valueList);
```

`插入一条数据 全表插入`
```sql
insert into Student values('2022110401', '李志明', 23, '522649988105061599', '男');
```

`指定列，sex采用默认值`
```sql
insert into Student(SID, Name, Age, AID) values('2022110402', '李航勇', 24, '655199504081599');;
```

`插入多条数据, 只需要中间用逗号隔开括号就行！ easy!`
```sql
insert into Student(SID, Name, Age, AID, sex) 
values('2022110403', '杨紫', 22, '454489434081599', '女'),('2022110404', '迪丽热巴', 30, '246582465784855', '女'),
('2022110405', '刘亦菲', 34, '546582785851112', '女'),('2022110406', '蒋星', 23, '711631199807091542', '男');
```

##### [2.2 Insert Select](#)
`将insert语句和select结合起来实现查询插入！`
```sql
create table person(
  PID varchar(20) primary key,
  Name varchar(20) not null,
  Age smallint,
  sex enum('男', '女') default '男'
);

insert into person(PID, Name, Age, sex) 
values('52206440917554', '高天亮', 22,  '男'),('52206440917555', 'uzi', 25, '男'),
('52206440917556', '宁王', 23, '男'),('52206440917557', 'xiaohu', 24, '男');
```
`使用一下吧`

```sql
set @sidStart = 2022110407;
set @rownum = 0;
insert into student (SID, Name, Age, AID, sex)
select  @sidStart + (@rownum:=@rownum+1) AS rownum,Name,Age, PID, sex from person;
```

##### [2.2 update](#)
`使用update语句更新表中的记录。不加where 就会改变整个表格！`

```sql
update person set Name = 'forever god' where PID = '52206440917555';
```

##### [2.3 delete truncat drop](#)
`truncate与drop,delete的对比`

* `truncate与drop是 数据库模式定义语言DDL(Data Definition Language) 语句，执行后无法回滚；delete是DML语句，可回滚。`
* `truncate只能作用于表；delete，drop可作用于表、视图等。`
* `truncate会清空表中的所有行，但表结构及其约束、索引等保持不变；drop会删除表的结构及其所依赖的约束、索引等。`
* `truncate会重置表的自增值；delete不会。`
* `truncate不会激活与表有关的删除触发器；delete可以。`
* `truncate后会使表和索引所占用的空间会恢复到初始大小；delete操作不会减少表或索引所占用的空间，drop语句将表所占用的空间全释放掉。`

```sql
delete from person; -- 删除全部数据

delete from person where PID = '52206440917555';
```

`MYSQL 事务处理主要有两种方法：`  `用 BEGIN, ROLLBACK, COMMIT来实现`
* `BEGIN 开始一个事务`
* `ROLLBACK 事务回滚`
* `COMMIT 事务确认`

```sql
begin; 
/*开启事务*/

update person  set Age = 25 where PID = '52206440917557';

select * from person;

rollback;  -- 改回去

commit;
```

#### [3. 单表查询语句](#)
`结构化查询语言（Structured Query Language）简称SQL！算是数据库核心了！ 基本查询结构！`

```sql
select /* 字段列表 */ 
from /* 表名1, 表名2*/ 
where /* 查询条件 */
group by /*分组列名*/
having /* 组条件 */
order by /* 列名 */
limit offset rouCount
```

##### 3.1 查询语法
`查询所有列`
```sql
use sakila;
select * from  actor;
```
`查询特定列`
```sql
select actor_id , last_update from  actor;
```
`带着查询条件`
```sql
select actor_id , last_update 
from  actor
where actor_id <= 25;
```

`查询行号`
```sql
select @rowNumber:=@rowNumber+1 AS rownum from (SELECT @rowNumber:=0) as r, actor;
```

##### 3.1.1 where 逻辑运算符
`下面表格的运算符 where基本上都用得到`


|`运算符`|`说明`|
|:-----|:------|
|`=`|`等于`|
|`!= <>`|`不等于`|
|`<=>`|`null 安全的等于`|
|`<`|`小于`|
|`<=`|`小于等于`|
|`>`|`大于`|
|`>=`|`大于等于`|
|`between val1 and val2`|`范围`:`select * from income where (amount + target_amount) between 100 and 500`|
|`in`|`存在于指定的集合 常用语子查询。下面有实例！`|
|`is null`|`为null`|
|`is not null`|`不为null`|
|`like`|`和通配符匹配` `select * from user where username like '%新%'; ` |
|`regexp 和 Rlike`|`正则表达式匹配！`|
|`not 或 !`|`逻辑非 取反`|
|`and 或 &&`|`逻辑与`|
|`or 或 \|`|`逻辑或`|
|`xor`|`异或`|

`多条 where 用and 或者 or 链接`
```sql
select * from city 
where country_id  > 100 && country_id < 110 
and last_update > cast('2006-02-14 04:45:25' as datetime) ;
```

##### 3.1.1 where in 和 not int 关键字
`使用IN 操作符将所有检索条件用括号括起来，检索条件之间用逗号分离，只要满足条件范围内的一个值就匹配成功` 

```sql
select * from student where Sname in ('李强', '张三', '李四', '王五');
```

`查询选择了数据库这门课并且成绩大于60分的同学，也可以可以使用子查询！`
```mysql
select * from student where Sno in 
  (select Sno from sc where grade > 60 and Cno in (select Cno from course where Cname = '数据库'));
```

`选择所有不满足匹配条件的`
```sql
select * from student where Sname not in ('李强', '张三', '李四', '王五');
```

##### 3.1.2 where between and
`查询某个范围的值，需要两个参数`

```sql
select * from SC where grade between 80 and 95;
```

##### [3.1.3 where like 模糊查询](#)
`模糊查询，like一起使用的通配符 % 和 _ ;`

`%`:`匹配任何长度的字符，甚至包括零字符`  
`_`:`一次只能匹配一个字符`

```sql
select * from Student where Sname like '李%';
```

##### 3.1.4 null 和 not null 
`查询空值, 需要使用is 和 is not 配合！`
```sql
insert into Student values('012', '蒋天元', null, null , null);

select * from Student where Sage is null;

select * from Student where Sage is not null;
```

##### 3.1.5 多条件查询 and or 
`很简单的哈`
```sql
select * from Student where Ssex = 'm' and Sdept = 'E';
```

```sql
select * from Student where Ssex = 'm' or Sdept = 'E';
```

##### [3.1.6 distinct](#)
`使得查询结果不重复  select distinct 字段 from Student;`

```sql
select distinct Sage from Student;
```

##### 3.1.7 order 排序
`排序可以分为单列查询和多列查询`
* `DESC 降序`
* `ASC 升序排列`
```sql
select * from Student order by  Sage desc;

select * from Student order by  Ssex, Sage desc;
```

##### [3.1.8 group 分组查询](#)
`MySQL 使用GROUP BY 关键字对数据进行分组: 常常和聚合函数联合使用` `可以单个字段 也可以多个字段分组`

|`函数`|`功能`|
|:----|:----|
|`sum(列名)`|`加和 适合数值字段，用于查询 还可以用于统计 你看下面`|
|`avg(列名)`|`返回平均值`|
|`max`|`最大值`|
|`min`|`最小值`|
|`count()`|`计数器`|

```sql
select 分组字段,聚合函数 group by 分组字段 having <条件表达式：聚合函数>
<with rollup>;
```

```sql
select 
	case Ssex when 'm' then '女' else '男' end as '性别',
	count(*) as '总人数',
	avg(Sage) as '平均年龄' 
	from Student 
	group by Ssex 
	having count(*) > 1 ;
```
`使用了 with rollup 会在所有查询出的分组记录之后增加一条数据，该记录计算查询出所有记录之和`


```sql
delete from Student where Sno = '012';


select case when Ssex is null then '总计' else Ssex end  as '性别',count(*) as '人数' from Student group by Ssex 
with rollup;
```

|`性别`|`人数`|
|:----|:----|
|`M`|`1`|
|`f`|`3`|
|`m`|`7`|
|`总计`|`11`|

`先按第一个字段分组，然后再第一个字段值相同的记录中，再根据第二字段的值进行分组....`

```sql
select * from SC group by CNO, Sno;
```
`用得少一点！`
```sql
select * from SC group by Cno, Sno having Sno = '001';
```

##### [3.1.9 Limit 限制查询结果的数量](#)
`select 返回所有匹配的行， 默认位置偏移量是0;`

```sql
Limit 位置偏移量, 行数
```

`找到第10 - 20行数据！`
```sql
select * from SC Limit 10, 10;
```

##### [3.1.10 配合聚合函数](#)
`聚合函数常常使用`

```sql
select count(*) as '总数', max(grade) as '最大值', min(grade) as '最小值' from SC ;
```

#### [4. 链接查询](#)
`链接是关系数据库模型的主要特点。链接查询是关系数据库最主要的查询，主要包括内连接，外连接。可以实现对多个表的查询`

##### [4.1 内连接 inner join](#)
`内连接(Inner Join)使用比较运算符进行表间某列数据的比较操作`

`where 也可以定义链接，简单明了，但是inner join能确保不忘记链接条件，并且它是ANSI SQL标准，而且where会影响查询性能! 推荐使用 inner  join ;`   `WHERE无法得到左、右、全连接的效果。`
```sql
select * from SC, Course where SC.Cno = Course.Cno; 
```

`两个表的链接`
```sql
select * from SC inner join Course on SC.Cno = Course.Cno;
```

`三个表的链接`
```sql
select * from SC inner join Course on SC.Cno = Course.Cno inner join Student on SC.Sno = Student.Sno; 
```

`你会发现多个表连接后，查询结果有了重复的字段了，所以当我们使用链接的时候，最好指定字段`

```sql
select Student.Sno, Student.Sname, 
case Ssex when 'M' then '女' when 'm' then '女' when 'f' then '男' else '' end as '姓名',
SC.Cno, Course.Cname, SC.grade, Student.Sage
from SC 
inner join Course on SC.Cno = Course.Cno 
inner join Student on SC.Sno = Student.Sno; 
```

##### [4.2 外连接 outer join](#)
`外连接有两种 左连接和右链接!` `left join, right join，full join等很多形式。具体的原理如下图所示。但其中最常见的还是使用left join 。`

* `LEFT JOIN（左连接）∶返回包括左表中的所有记录和右表中连接字段相等的记录。没有就为null`
* `RIGHT JOIN（右连接）∶返回包括右表中的所有记录和左表中连接字段相等的记录。没有就为null`

`left join 左连接`
```sql
select * from SC left join Course on SC.Cno = Course.Cno;
```
`right 右链接`
```sql
select * from SC left join Course on SC.Cno = Course.Cno;
```

#### [5. 子查询](#)
`子查询指一个查询语句嵌套在另一个查询语句内部的查询。Select 子句中先计算子查询，子查询结果作为外层另一个查询的过滤条件。 常用的操作符有 ANY,SOME, ALL IN EXISTS。` `子查询可以添加到
SELECT、UPDATE和DELETE语句中。而且可以多层嵌套！ 还可以使用比较运算符`

```sql
select Sno, Sname, Sage, Ssex, Sdept
from Student where Sno in 
( select Sno from SC where Cno  in 
   (
     select Cno from Course where Cname = '数据库'
    )
)
```

`All`:`满足子查询的所有条件`
`Some|Any`：`满足子查询部分条件就可以`
`IN EXISTS`:`只要有就行`

`整点数据`
```sql
create table Num1 (credit int primary key);
create table Num2 (coin int primary key);

insert into Num1 values(546),(75),(785),(800),(5412),(715),(475);
insert into Num2 values(141),(15),(785),(800),(1412),(215),(421);
```

```sql
select * from Num1 where credit >  all(select coin from Num2 );
```

`EXISTS`
```sql
--  如果Num1 中的数值 在 Num2 中也有那么 查询出来
select credit from Num1 where exists(select coin from Num2  where coin = Num1.credit);
```

`总之 子查询有几种呢？ `  
* `some any some`
* `in 或者 not in`
* `exists`
* `比较运算符  > >= <= = !=` 

```sql
select * from fruits
where id =
(select s1.id from suppliers as s1 where s1.city = '天津')
```


```
实践经验： IN适合于外表大而内表小的情况；EXISTS适合于外表小而内表大的情况，这样效率会高的
```

#### [6. 集合操作](#)
`利用UNION 关键字可以将多个查询结果组合成一个结果集！`

`union all 不去除重复结果` `union 去除重复结果`
```sql
select credit as num from Num1
union All
select coin as num from Num2;
```

#### [7. 别名 as](#)
`可以给表,视图,字段名取一个别名`

```sql
select N.credit as num from Num1 as N;
```

#### [8. 使用正则表达式查询](#)
`正则表达式通常被用来检索或替换那些负荷某个模式的文本内容，根据指定的匹配模式匹配文本中负荷要求的特殊字符串。` `适合非常复杂的查询` `mysql使用` `REGEXP 关键字指定正则表达式的字符匹配模式。 常用字符匹配列表！`

|`选项`|`说明`|`例子`|`匹配值示例`|
|:----|:----|:----|:----|
|`^`|`匹配输入字符串的开始位置。`|`^b`|`book, big, banan`|
|`$`|`匹配输入字符串的结束位置。`|`st$`|`test, rest`|
|`.`|`匹配除 "\n" 之外的任何单个字符`|`b.t`|`bat bet bnt`|
|`*`|`匹配前面的子表达式零次或多次。`|`zo*`|`zoo zoooooo`|
|`+`|`匹配前面的子表达式一次或多次`|`ba+`|`ba, baaaaaaaaa, baaa`|
|`字符串`|`正常匹配`|`fa`|`fa`|
|`[]`|`n 是一个非负整数。匹配确定的 n 次。`|`字符集合`|`。例如， '[abc]' 可以匹配 "plain" 中的 'a'。`|
|`[^]`|`负值字符集合。匹配未包含的任意字符。`|`例如， '[^abc]' `|`可以匹配 "plain" 中的'p'`|
|`{n,}`|`n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。`|``|``|
|`{n,m}`|`m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。`|``|``|
|`p1\|p2\|p3`|`匹配 p1 或 p2 或 p3。`|`(z|f)ood'`|`匹配 "zood" 或 "food"`|

```sql
select * from Student  where Sname regexp '^李';
```

`查找name字段中以'ok'为结尾的所有数据：`
```sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP 'ok$';
```

`查找name字段中包含'mar'字符串的所有数据：`
```sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP 'mar';
```

`查找name字段中以元音字符开头或以'ok'字符串结尾的所有数据：`
```sql
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^[aeiou]|ok$';
```

#### [9. explain 执行计划](#)
`explain（执行计划），使用explain关键字可以模拟优化器执行sql查询语句，从而知道MySQL是如何处理sql语句。explain主要用于分析查询语句或表结构的性能瓶颈。`

`在日常工作中，我们会有时会开慢查询去记录一些执行时间比较久的SQL语句，找出这些SQL语句并不意味着完事了，些时我们常常用到explain这个命令来查看一个这些
SQL语句的执行计划，查看该SQL语句有没有使用上了索引，有没有做全表扫描，这都可以通过explain命令来查看。所以我们深入了解MySQL的基于开销的优化器，还可以
获得很多可能被优化器考虑到的访问策略的细节，以及当运行SQL语句时哪种策略预计会被优化器采用。`

```sql
-- 实际SQL，查找用户名为Jefabc的员工
select * from emp where name = 'Jefabc';

-- 查看SQL是否使用索引，前面加上explain即可
explain select * from emp where name = 'Jefabc';
```

`通过explain+sql语句可以知道如下内容：`
* `1. 表的读取顺序。（对应id）`  
* `2. 数据读取操作的操作类型。（对应select_type）`  
* `3. 哪些索引可以使用。（对应possible_keys）`  
* `4. 哪些索引被实际使用。（对应key）`  
* `5. 表直接的引用。（对应ref）`  
* `6. 每张表有多少行被优化器查询。（对应rows）`  

```sql
explain select * from actor where actor_id = 15 ;

-- id, select_type, table, partitions, type, possible_keys, key, key_len, ref, rows, filtered, Extra
-- 1	SIMPLE	actor		const	PRIMARY	PRIMARY	2	const	1	100.00	
```

```
id:选择标识符
select_type:表示查询的类型。
table:输出结果集的表
partitions:匹配的分区
type:表示表的连接类型
possible_keys:表示查询时，可能使用的索引
key:表示实际使用的索引
key_len:索引字段的长度
ref:列与索引的比较
rows:扫描出的行数(估算的行数)
filtered:按表条件过滤的行百分比
Extra:执行情况的描述和说明
```


##### explain 说明
* `EXPLAIN不会告诉你关于触发器、存储过程的信息或用户自定义函数对查询的影响情况`
* `EXPLAIN不考虑各种Cache`
* `EXPLAIN不能显示MySQL在执行查询时所作的优化工作`
* `部分统计信息是估算的，并非精确值`
* `EXPALIN只能解释SELECT操作，其他操作要重写为SELECT后查看执行计划。`

###### [参考资料 https://www.cnblogs.com/tufujie/p/9413852.html](https://www.cnblogs.com/tufujie/p/9413852.html)