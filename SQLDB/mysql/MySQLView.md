### [MySQL 视图](#)
`MySQL 视图，视图就很常用了！ 需要了解视图的含义、视图的作用、创建视图、查看视图、修改视图、更新视图和删除视图等MySQL的数据库知识。`

-----
- [x] [`1. 视图概述`](#1-视图概述)
- [x] [`2. 创建视图`](#2-创建视图)
- [x] [`3. 查看视图`](#3-查看视图)
- [x] [`4. 修改视图`](#4-修改视图)
- [x] [`5. 更新视图`](#5-更新视图)
- [x] [`6. 删除视图`](#6-删除视图)

-----

##### [1. 视图概述](#)
`数据库中的视图是一个虚拟表, 是从数据库中一个或多个表中导出来的表。视图还可以从已经存在的视图的基础上定义。 同真实的表一样，视图包含一系列带有名称的行和列数据。行和列数据来自由定义视图查询所引用的表，并且在引用视图时动态生成。`
`视图一经定义便存储在数据库中，与其相对应的数据并没有相对应的数据。对视图也可以进行查询，修改和删除。但是通过视图去修改表中数据，属事实厕所里打灯笼---找死！` `谨记，视图是用来查询的！`

* `视图可以使得一些查询简单化`
* `逻辑数据独立性`
* `安全性`

##### [2. 创建视图](#) 
`创建视图使用Create View语句。创建语法为：`

```sql
CREATE [OR REPLACE] [ALGORITHM={UNDEFIEND | MERGE | TEMPTABLE}]
VIEW view_name [(column_list)]  -- 视图名 列名称列表
AS SELECT_statement
[WITH [CASCADED | LOCAL] CHECK OPTION]
```

* `ALGORITHM 表示视图选择的算法`: `UNDEFIEND表示MySQL将自动选择算法` 
    * `MERGE，表示将使用视图的语句，与视图定义合并起来，使得视图定义的某一部分，取代语句的对应部分`
    * `TEMPTABLE，表示将视图的结果存入临时表，然后使用临时表执行语句`
* `[WITH [CASCADED | LOCAL] CHECK OPTION]`:`表示视图在更新时保证在视图的权限范围之内。`

```sql
create or replace View GradeView(Cno, Sno, Cname, Ccredit, grade)
as
select SC.Cno, Sno, Cname, Ccredit, grade from Course inner join SC on Course.Cno = SC.Cno;
```

##### [3. 查看视图](#) 
`查看视图的定义`
```sql
describe 视图名
```

`使用Show table status 语句查看视图基本信息`

```sql
show table status like '视图名';
```

`查看视图详细信息`

```sql
show create view 视图
```

`在MySQL中，information_shema数据库下的views表中存储了所有视图的定义。通过对views表的查询,可以查看数据库中所有视图的详细信息`

```sql
select * from information_schema.views;
```

##### [4. 修改视图](#) 
`alter 语句是MySQL提供的另外一种修改视图的方法`

```sql
alter [ALGORITHM={UNDEFIEND | MERGE | TEMPTABLE}]
VIEW view_name [(column_list)]  -- 视图名 列名称列表
AS 
SELECT_statement
[WITH [CASCADED | LOCAL] CHECK OPTION]
```

```sql
alter view view_t as select quantity from t;
```

##### [5. 更新视图](#)
`你真是厕所里撑杆跳 --- 过分了！ 坚决不支持更新视图，更新表，一定只能通过表来更新。`  `但是吧。你要是真要这样吧，也可以，你直接像更新表一样一样更新视图就可以了`

```sql
update view_t set quantity = 5;

insert into t values(3,5);

delete from view_t2 where price = 5;
```

##### [6. 删除视图](#)
`当视图不需要的时候，可以将其删除，删除一个或多个视图可以使用 Drop View语句。`

```sql
drop view if exists
    view_name, [view_name] ...
    restrict | cascade
```
* `restrict: 确保只有不存在相关视图和 完整性约束的表才能删除`
* `cascade: 任何相关视图和完整性约束一并被删除`

-----
`时间`: `[2022/4/1]` 