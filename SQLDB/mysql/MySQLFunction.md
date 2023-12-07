### [MySQL 常用函数](#)
`这些啊，就像MySQL API一样。函数嘛！总不能自己从头开始造轮子吧！MySQL提供了众多功能强大、方便易用的函数。MySQL中的函数包括了，字符串函数，数学函数，日期和时间函数，条件判断函数，
系统信息函数，加密函数等等...`

-----

- [ ] [`1. 数学函数`](#1-数学函数)
- [ ] [`2. 字符串函数`](#2-字符串函数)
- [ ] [`3. 日期和时间函数`](#3-日期和时间函数)
- [ ] [`4. 条件判断函数`](#4-条件判断函数)
- [ ] [`5. 窗口函数`](#5-窗口函数)
- [ ] [`6. 系统信息与加密函数`](#6-系统信息函数)
- [ ] [`7. 聚合函数和常用工具函数`](#7-聚合函数和常用工具函数)
- [ ] [`8. 类型转换`](#8-类型转换)

----

#### [1. 数学函数](#)
`数学函数主要用来处理数值数据，你懂的！ 我们只说明常见的，如果需要，请查询官网^ [1]`

|`函数`|`功能`|
|:----|:----|
|`abs(value)`|`返回value的绝对值`|
|`pi()`|`返回圆周率，默认是小数位数是6位！`|
|`ceil(x)或 ceiling(x)`|`向上取整`|
|`floor(x)`|` 向下取整`|
|`sqrt(x)`|`平方根函数`|
|`mod(x,y)`|`x%y`|
|`rand()`|`返回0~1之间的随机浮点数。每次产生的随机数是不同的`|
|`round(x,y)`|`返回参数x的四舍五入的带有y位小数的值`|
|`truncate(x,y)`|`返回数字x截断y位小数的结果！`|
|`sign(x)`|`返回参数的符号，为负 -1 零返回0 正返回 1`|
|`pow(x,y) 或 power()`|`返回x的y次乘方的结果值`|
|`exp(x)`|`exp(3) = e^3`|
|`log(x) log10(x) `|`返回x的基数为10 的对数！`|

```sql
select abs(-35.25), pi();
-- 35.25	3.141593
```

```sql
select ceil(52.32), floor(52.15);
-- 53	52
```

```sql
select ceil(52.32), floor(52.15);
-- 53	52
```

```sql
select rand(), rand(),rand();
-- 0.6985001682289878	0.39245743154776974	0.8667866837855305

select rand(10), rand(10),rand(11);
-- 0.6570515219653505	0.6570515219653505	0.907234631392392
```

#### [2. 字符串函数](#)
`字符串函数主要用于来处理数据库中的字符串数据，内容有：计算机字符串长度、字符串合并函数、字符串替换函数、字符串比较函数、查找指定字符串位置函数等等。。。`

|`函数`|`功能`|
|:----|:----|
|`char_length`|`返回值为字符串str 的长度，长度的单位为字符。一个多字节字符算作一个单字符`|
|`length`|`返回值为字符串str 的长度，单位为字节。一个多字节字符算作多字节。这意味着 对于一个包含5个2字节字符的字符串， LENGTH() 的返回值为 10, 而 CHAR_LENGTH()的返回值则为5。`|

```sql
select length('18asd5'), length('蒋1'), char_length('蒋1'); 
-- 6	4	2
```

|`函数`|`功能`|
|:----|:----|
|`concat(s1,s2,s3...)`|`链接s1,s2,s3为一个字符串！`|
|`concat_ws(x, s1, s2...)`|`x是分隔符，利用分隔符x, 链接s1,s2,s3为一个字符串！`|

```sql
select concat('wo shi', ' shabi', ' o') as message , concat_ws(',', '红色', '白色', '蓝色') as colors ; 
-- wo shi shabi o	红色,白色,蓝色
```

|`函数`|`功能`|
|:----|:----|
|`insert(s1, x, len, s2)`|`将字符串s1从第x位置开始，y个字符串的子串替换为 s2 x从1开始计数`|

`它是从0开始计数的。唉我也不知道这玩意儿有啥用，你就存个数据，你处理数据干嘛啊！`
```sql
select insert('123456789', 1, 4, 'abcd') as result;
-- abcd56789
```


|`函数`|`功能`|
|:----|:----|
|`lower(x)`|`x转换为小写`|
|`lcase(x)`|`x转换为大写`|
|`UPPER(str) 或 UCASE(str)`|`返回字符串str， 以及根据最新字符集映射转化为大写字母的字符`|

```sql
select lower('AlIce'), upper('aLice');
-- alice	ALICE
```

|`函数`|`功能`|
|:----|:----|
|`left(s,n)`|`返回字符串n，最左边n个字符。`|
|`right(s, n)`|`返回字符串s, 最右边n个字符串。`|


|`函数`|`功能`|
|:----|:----|
|`trim(x)`|`删除字符串左右空格`|
|`ltrim(x)`|`删除字符串左边的空格`|
|`rtrim(x)`|`删除字符串右边的空格`|
|`substring(s, n, len)或 Mid(s, n, len)`|`返回字符串s,n位置起y个字符串长度的字符串。`|
|`reverse`|`字符串反转`|

#### [3. 日期和时间函数](#)
`日期函数嘛，总是需要的！`


|`函数`|`功能`|
|:----|:----|
|`curdate()`|`返回当前日期 请注意返回的日期格式哦 YYYY-MM-DD`|
|`curtime()`|`返回当前时间`|
|`now()`|`返回当前时间和日期`|

```sql
select curdate() as '当前日期', curtime() as '当前时间', now() as '现在日期和时间';
-- 2022-03-07	20:32:01	2022-03-07 20:32:01
```

|`函数`|`功能`|
|:----|:----|
|`unix_timespan()`|`返回unix时间戳`|
|`from_unixtime()`|`返回当前时间`|
|`unix_date()`|`返回当前UTC世界标准日期值，格式为 YYYY-MM-DD`|




|`函数`|`功能`|
|:----|:----|
|`week(date)`|`返回日期date为一年中的第几周`|
|`year(date)`|`返回日期date的年份`|
|`hour(time)`|`返回time的小时值`|
|`quarter()`|`返回指定日期对应的季度`|
|`minute(time)`|`返回月份值 1 -12`|
|`second(time)`|`返回time的秒数`|
|`monthName(date)`|`返回date的月份名 英语日期名称`|
|`date_format(date, fmt)`|`返回字符串fmt格式化日期date值`|
|`datediff(expr, expr2)`|`返回起始时间 expr和结束时间expr2之间的天数`|

```sql
select unix_timestamp() as unix; 
-- 1646725192  返回从 1970-01-01 00:00:00 到现在的毫秒数
```

|`函数`|`功能`|
|:----|:----|
|`DayName(d)`|`返回指定日期的工作日名称。sunday monday...`|
|`DayOfYear(d)`|`范围1 ~ 366`|
|`DayOfMonth(d)`|`函数返回d是一个月中的第几天，范围是从 1 ~ 31`|

```sql
select now(), day(now()), dayname(now()), dayofweek(now()), dayofmonth(now()), dayofyear(now());
-- 2022-03-08 15:53:32 8	Tuesday	3	8	67
```

|`函数`|`功能`|
|:----|:----|
|`time_to_sec(time)`|`返回已转换为秒的time参数。`|
|`sec_to_time(second)`|`秒转换为小时格式 MM-HH-SS`|


##### 3.1 date_format


```sql
select DATE_FORMAT(NOW(),'%Y-%m-%d %H-%i-%s') as mytime; -- 2022-03-08 16-07-59
```

|`格式`|`描述`|
|:----|:----|
|`%a`|`缩写星期名`|
|`%b`|`缩写月名`|
|`%c`|`月，数值`|
|`%D`|`带有英文前缀的月中的天`|
|`%d`|`月的天，数值(00-31)`|
|`%e`|`月的天，数值(0-31)`|
|`%f`|`微秒`|
|`%H`|`小时 (00-23)`|
|`%h`|`小时 (01-12)`|
|`%I`|`小时 (01-12)`|
|`%i`|`分钟，数值(00-59)`|
|`%j`|`年的天 (001-366)`|
|`%k`|`小时 (0-23)`|
|`%l`|`小时 (1-12)`|
|`%M`|`月名`|
|`%m`|`月，数值(00-12)`|
|`%p`|`AM 或 PM`|
|`%r`|`时间，12-小时（hh:mm:ss AM 或 PM）`|
|`%S`|`秒(00-59)`|
|`%s`|`秒(00-59)`|
|`%T`|`时间, 24-小时 (hh:mm:ss)`|
|`%U`|`周 (00-53) 星期日是一周的第一天`|
|`%u`|`周 (00-53) 星期一是一周的第一天`|
|`%V`|`周 (01-53) 星期日是一周的第一天，与 %X 使用`|
|`%v`|`周 (01-53) 星期一是一周的第一天，与 %x 使用`|
|`%W`|`星期名`|
|`%w`|`周的天 （0=星期日, 6=星期六）`|
|`%X`|`年，其中的星期日是周的第一天，4 位，与 %V 使用`|
|`%x`|`年，其中的星期一是周的第一天，4 位，与 %v 使用`|
|`%Y`|`年，4 位`|
|`%y`|`年，2 位`|

#### [4. 条件判断函数](#)
`流程控制函数 MySQL中进行条件判断的函数有IF IFNULL CASE` `都记住啊，很常用的啊！ 写复杂查询需要这个东西！`

|`格式`|`描述`|
|:----|:----|
|`IF(expr, v1, v2)`|`如果expr为真，返回v1，否则返回 v2`|
|`IFNULL(v1, v2)`|`如果v1不是null，返回v1，否则返回 v2`|
|`case函数`|``|

```sql
select IF(25 > 10, 25, 10), ifnull(null, 25) ; 
-- 25 25
```

`case when then else end函数， 好东西啊！复杂SQL常用函数` `这有两种用法， 1是不带判断条件 在when中带，2是带参数，变成 switch`

`1`
```sql
create table person(
  pid int primary key  auto_increment,
  name varchar(25),
  age SmallInt default 18
);


insert into person(name, age) values('tome', 25),
('kicker', 88), ('remix', 8),
('ga', 18), ('cmix', 35),
('asddd', 108), ('mike', 24);

select case when age < 18 then '未成年' when  18<= age and age < 55 then '成年人' else '老年人' end from person;
```

`2`
```sql
select case 2 when 1 then 'one' when 2 then 'two' else 'more' end;
```

#### [5. 窗口函数](#)
`用于分析的函数, 等等哈！ 以后看懂了分区 分表！`

|`函数`|`功能`|
|:----|:----|
|`row_number()`|`分区中的当前行号`|
|`rank()`|`当前行在分区中的排名，含序号间隙`|
|`dense_rank()`|`当前行在分区中的排名，没有序号间隙`|
|`percent_rank()`|`百分比等级值`|

#### [6. 系统信息与加密函数](#)
`返回系统信息，有版本号，当前用户名，连接数、系统字符集！`

|`函数`|`功能`|
|:----|:----|
|`version()`|`查看MySQL版本号`|
|`show  processlist`|`显示有哪些线程在运行。`|
|`database()`|`返回当前数据库名称`|
|`user()`|`返回当前用户`|
|`inet_aton(ip)`|`返回IP地址的数字表示 反正就是四个字节 用一个无符号整数 int 就可以表示是所有的IP地址`|
|`inet_nota(num)`|`返回数字代表的IP地址`|
|`password(str)`|`返回字符串str的加密版本 8.0版本已经移除了哦！ `|
|`md5(str)`|`返回字符串str的MD5值`|
|`encode(str, pswd_str)`|`将str用pswd_str作为密码加密`|
|`decode(crypt_str, pswd_str)`|`将密文用pswd_str作为密码解密`|

```sql
select version();

show processlist;
show full processlist;

select inet_aton('139.56.45.122') as ipNumber;  -- 2335714682
select inet_aton('0.0.0.1') as ipNumb; -- 1

select md5('jkicker152asd');
-- 87cd5357234038c9040bb81376813fc1

select charset('abc'), charset(convert('abs' using 'latin1'))
-- utf8mb4	latin1
```

#### [7. 聚合函数和常用工具函数](#)
`聚合函数和一些生成ID 还是很好用的`

|`函数`|`功能`|
|:----|:----|
|`uuid()`|`在MySQL中，可以用uuid()函数来生成一个UUID 默认生成的uuid含有'-'，我们可以使用replace函数替换掉'-'`|
|`UUID_SHORT()`|`请看下面详细介绍`|

`MySQL中的此函数用于返回“short”通用标识符，为64位无符号整数。如果满足以下条件，则保证UUID_SHORT()的值是唯一的：`
* `当前服务器的server_id值介于0到255之间，并且在我们的源服务器和副本服务器集合中是唯一的。`
* `在mysqld重新启动之间，我们不为服务器主机设置系统时间。`
* `在mysqld重新启动之间，我们平均每秒调用UUID_SHORT()少于1600万次。`

```sql
select replace(uuid(),"-","") as uuid, uuid_short() as uuid_short;
```

|`函数`|`功能`|
|:----|:----|
|`last_insert_id`|`返回最后一个Insert或update为auto_increment列设置的第一个发生的值`|
|`convert(x, type) 或者 cast(x as type)`|`类型转换函数 将x转换为type 类型`|
|`sum(列名)`|`加和 适合数值字段，用于查询 还可以用于统计 你看下面`|
|`avg(列名)`|`返回平均值`|
|`max`|`最大值`|
|`min`|`最小值`|
|`count()`|`计数器`|

```sql
select cast('2020-5-15 15:56:22' as time) as castTime ,convert('2020-5-15 15:56:22', time) as time;
-- 15:56:22	15:56:22
```
`我们可以用来统计列数`
```sql
select count(*) as totalLine from user;


select sum(case when age >= 18 then 1 else 0 end) as 成年人 ,
sum(case when age < 18 then 0 else 0 end) as 未成年人 from user;
```

#### [8. 类型转换](#)
`非常常用！`

|`函数`|`功能`|
|:----|:----|
|`convert(x, type) 或者 cast(x as type)`|`类型转换函数 将x转换为type 类型`|

----
```
时间: 2022-03-07 16:35    [1] 中文文档: [https://www.mysqlzh.com/]
```
