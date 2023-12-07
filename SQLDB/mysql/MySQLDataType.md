### [MySQL 数据类型和运算符](#)
`MYSQL 支持很多的数据类型，但是不要慌,我们只需要完全掌握重要常用的，明白数据类型中的坑!本节数据结构采用MySQL5.7中拥有的。总体上来说MySQL有
三种数据类型，数值，字符串，日期时间。`

-----

- [ ] [`1. 数据类型概括`](#1-数据类型概括)
- [ ] [`2. 数值数据类型`](#2-数值数据类型)
- [ ] [`3. 日期与时间类型`](#3-日期与时间类型)
- [ ] [`4. 文本字符串类型`](#4-文本字符串类型)
- [ ] [`5. 运算符`](#5-运算符)

----

#### [1. 数据类型概括](#)
`MYSQL 支持的数据类型啊 大概分为三种 来概括现在的所有数据`

`1`. `数值数据类型`:`TinyInt SmallInt MediumInt Int BigInt Float Double Decimal 主要用于存储数字。 `  
`2`. `日期/时间类型`:`Year Time Date DateTime TimeStamp`  
`3`. `字符串类型`:`Char Varchar binary varbinary Blob Text Enum Set 字符串又分为二进制字符串 文本字符串 等等。`  


#### [2. 数值数据类型](#top)
`数值数据类型`:`TinyInt SmallInt MediumInt Int BigInt Float Double Decimal 主要用于存储数字。 `  

##### [2.1 MySQL 整型数据](#)

`来看看整型，MYSQL 提供了五种整型。`

|`类型名称`|`说明`|`存储需求`|`显示宽度`|`表示范围`|
|---------|:---|:----|:----|-----|
|`TinyInt` |`很小的整数 很少用` |`一个字节` | `默认显示宽度 4：tinyint(4) `| `2^8` |
|`SmallInt`|`几乎没用过 很少用`|`两个字节`|`默认显示宽度 6: smallint(6)`|`2^16`|
|`MediumInt`|`几乎没用过 很少用`|`三个字节`|`默认显示宽度 9: mediumint(9)`|`2^24`|
|`Int(Interger)`|`整数 很常用`|`四个字节`|`默认显示宽度 11: int(11)`|`2^32`|
|`BigInt`|`大整数 常用`|`八个字节`|`默认显示宽度 20: bigint(20)`|`2^64`|


`1. 注意: Int() 括号里面是可以跟位数的 表示的意思是整数想显示宽度， 默认值是11。 如果要使用宽度0填充。需要在创建数据库的时候添加约束 zerofill。但是 zerofill将要被废除。
所以我也不了解这个显示宽度有啥意义。估计是没有意义。因为你填写了就要配合 zerofill。不用就相当于没设置。但是zerofill又要被废弃，所以别设置就行。`
`Int(9)`:`数据的显示宽度是九位  如果数字宽度超过九位 那么将会原样显示。[已经废弃。不用再管宽度]`  
`2. 注意: 主要记住Int 和BigInt 就行了,其他的数据类型很少用` 

```sql
create table test1(
 c1 int(5) zerofill,
 c2 int 
)

insert into test1 values(25,1);

select * from test1;
/*
|c1| c2|
|00025|	1|
*/
```

#####  [2.2 MySQL浮点数和定点数数据](#) 
`记住浮点数,是不精确的小数 是有误差的 可能误差很小而已 可以忽略误差,定点数只有Decimal 它的精度很高 他们都有 (M,N) 表示小数 M[精度]：数字总的位数,N[标度]:表示小数部分的位数 例如 dicimal(8,2):862531.12  988812.00。`

|`类型名称`|`说明`|`存储需求`|`显示宽度`|`表示范围`|
|---------|:---|:----|:----|-----|
|`Float(M,D)`|`很常用 单精度浮点数`|`4个字节`|`显示宽度 通过MN 定义`|` float(8,3) 总共八位表示浮点数，三位小数，五位整数！`|
|`Double(M,D)`|`双精度浮点数 很常用`|`8个字节`|`MN 定义`|`自己查询`|
|`Decimal(M,D) DEC(M,D)`|`定点数 严格的定点数 有很高的精度`|`M+2 个字节 M：精度`|`默认精度 (10,0）`|`很高 自己查询`|


`1. 注意: Float 和Double 会自动进行四舍五入的 例如对于 Double(5, 1)  5.125 会被四舍五入为 5.1 这是默认的不发生任何警告 但是对于 Dicimal(5, 1)来说 定点小数 会发出警告 不允许 5.125 插入`
 `Decimal(5.2)`:`5.2602 会插入报错 小数位数最多只允许两位`

```sql
create table test2(
 c1 float(7,2) ,
 c2 double(10,4),
 c3 decimal(10,3)
);

insert into test2 values(525.2, 95.332151, 98564.5555);

--  98564.5555 小数是4位。
--  但是  c3 decimal(10,3) 只支持三位小数。 所以会警告哦 数据发生了截断。 
--  使用定点数尤其要注意这点。 浮点数就直接四舍五入。根本不管！
--  1 warning(s): 1265 Data truncated for column 'c3' at row 1	0.000 sec

```

##### [2.3 Bit 位类型](#)
`用于存放位字段值。BIT(M)可以用来存放多位二进制。M的范围 1~64` `需要配合 bin() hex() oct() 函数查询。默认是返回十进制的值。 你可以用函数，查询出二进制，十六进制 八进制的数据。`

|`类型名称`|`说明`|`存储需求`|`显示宽度`|`表示范围`|
|---------|:---|:----|:----|-----|
|`Bit(M)` |`存放位字段值` |`1~64位` | `默认1位 `| `1~64位` |


```sql
use Remix;

create table test3(
	uid bit(10),
    bytes bit(8)
);
insert into test3 values(1,1);
insert into test3 values(0x123,11);

select bin(uid), oct(uid), hex(bytes) from test3;
```

####  [3. 日期与时间类型](#) 
`主要用DateTime Date Time 啊, 其他各种类型的时间数据都可以通过时间函数去获取，就可以避免其他时间日期数据类型的坑了。`


|`类型名称`|`日期格式`|`存储`|`说明`|`表示范围`|
|:-----|:------|:-----|:-----|:------|
|`Year` |`YYYY` | `不要用它谢谢` `1个字节`|**`直接跳过它 直接不用`** `你看它表示的范围,有什么用？而且有一大堆坑 什么两位表示的转换 0的转换问题`  |`1901~2155` |
|`Time` |`HH:MM:SS`  |`3个字节` |`没啥用 说实话 也基本上不用这种类型` |`-838:59:59 ~ 838:59:59` |
|`Date` |`YYYY-MM-DD` |`3个字节` |`常用 用于记录日期` |`1000-01-01~9999-12-3`|
|`DateTime` |`YYYY-MM-DD HH:MM:SS` |`8个字节` |`其他的都不会都没关系 只要记住它就行了 非常常用` |`1000-01-01 00:00:00 ~9999-12-3 23:59:59` |
|`TimeStamp` |`YYYY-MM-DD HH:MM:SS` |`4个字节`|`我没用过 因为 时间表示范围太小了 跳过它`  |`1970-01-01 00:00:01 UTC ~ 2038-01-19 03:14:07 UTC`|

* `1.注意: Year 数据类型 只能表示 1901~2155 之间的年份  极其的捞`
* `2.Time类型的问题 当以 HHMMSS 格式存储时间的时候 '101112' 会被解析成 10:11:12 但是 '109712' 是非法的 会被解析为 00:00:00 `
* `3.插入时间的时候可以直接插入字符串` 


####  [4. 文本字符串类型](#)
`字符串类型用来存储字符串数据,除了可以存储字符串数据之外,还可以存储其他数据,比如图片和声音的二进制数据,MYSQL支持字符串数据;`

##### [4.1 文本字符串类型](#)
`就是非二进制的意思, 你看一眼就懂了: `

|`类型名称`|`存储`|`说明`|
|:-----|:-----|:-----|
|`char(M)`|`M个字符 M 在1~255 之间`|`固定长度非二进制字符串 不够长就会添加空格`|
|`varchar(M)`|`L+1 字节 L<=M和 1<= M <= 255` |`变长非二进制字符串, 长度不够，不会添加空格，常用至极。`|
|`TinyText`|`L+1字节 L<2^8`|`非常小的字符串`|
|`Text`|L+2  L < 2^16|`文本` |
|`MediumText`|L+3  L < 2^24|`文本` |
|`LongText`|L+4  L < 2^32|`文本` |
|`json`|`本质是存储的是json字符串！`|

`关于json的函数`
* `json_type() 返回json的类型，有String, Boolean, Null, Array, Object, number`
* `JSON_VALID() 判断一个JSON数据是否合法！`

`char 和 varchar的区别，首先是长度， varchar 能支持更长的字符串，并且是可变长度字符串。 char列删除了尾部的空格，而varchar则保留这些空格。
其中M代表该数据类型所允许保存的字符串的最大长度，只要长度小于该最大值的字符串都可以被保存在该数据类型中。 char是固定长度。`

```sql
use Remix;

create table Loser
(
    uid int primary key auto_increment ,
    name char(4),
    des varchar(4)
);

insert into Loser(name, des) values('jx', 'jx');

select length(name), length(des) from Loser; -- 2 2
```

##### [4.2 枚举类型](#)
`枚举类型,只能有一个枚举字符串值。 1或者2个字节。取决于枚举值的数目[最大值65535]。`

|`类型名称`|`格式`|`存储`|`说明`|`表示范围`|
|:-----|:------|:-----|:-----|:------|
|`Enum` |`ENUM(枚举值1,枚举值2,枚举值3, ...)` |`1~2个字节` | `好东西。做了check该做的工作。`| `1~ 65535个枚举值` |

```sql
 CREATE TABLE test4 (  
   id BIGINT UNSIGNED  PRIMARY KEY AUTO_INCREMENT,  
   brand VARCHAR(255) NOT NULL,  
   color ENUM('RED','GREEN','BLUE')
) ENGINE = InnoDB; 



insert into test4(brand, color) values('BYD', 'BLUE');
insert into test4(brand, color) values('DFDD导弹', 'RED');

select * from test4;
```

##### [4.3 集合类型](#)

|`类型名称`|`格式`|`存储`|`说明`|`表示范围`|
|:-----|:------|:-----|:-----|:------|
|`Set` |`Set(值1,值2,值3, ...)` |`1，2，3,4,8个字节` | `一个集合，字符串对象可以有零个或多个SET成员。最多64个成员`| `...` |

```sql
use Remix;

create table dictionary
(
    id int auto_increment primary key,
    collection set('a', 'c', 'f', 'd', 'e', 'b') not null
);

-- 搞个索引
create index dictionary_collection_index on dictionary (collection);

insert into dictionary (collection) values('a,c,d,e');


select * from dictionary;
```

##### [4.4 binary和 varbinary](#)
`类似于char和varchar，不同的是它们包含二进制字符串。 我也没咋用过，按道理说，二进制真应该存在数据库吗？ 怪哉 怪哉！;`

|`类型名称`|`存储`|`说明`|
|:-----|:------|:-----|
|`Bit(M)`|`1~64位` | `默认1位 `|
|`binary(M)`|`固定长度二进制字符串`|`M个字节`|
|`varbinary`|`可变长度二进制字符串`|`M+1个字节。`|
|`blob(M)`|`非常小的BLOB`|`L+1字节，大小 < 2^8`|
|`TinybBlob`|`小的BLOB`|`L+2 字节 大小< 2^16`|
|`MediumBlob`|`中等大小的BLOB`|`L+3 字节, 大小< 2^24`|
|`LongBlob`|`非常大的BLOB`|`L+4 字节, 大小 < 2^32`|

#### [5. 运算符](#)
`MySQL包括算术运算符，比较运算符，逻辑运算符，位运算。.....`

##### [5.1 算术运算符](#)
`MySQL 支持的算术运算符包括+ - * / %, 这是非常简单的。` 

`3%2`:`也可以函数代替， mod(3,2);`
```sql
select 1 - 23 , 0.2+6.3 , 0.1*5, 11%3 , 65.2/5, 1/0;
-- -22	6.5	0.5	2	13.04000  null
```

##### [5.2 比较运算符](#)
`为真返回1，为假返回0。 结果不确定直接返回null。 看一眼就会了！`

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

```sql
select * from student where sid in 
( select sid from courceStudent where cname = 'Mysql数据库' );
```

##### [5.3 逻辑运算符](#)
`每门语言都有。真的很简单....`

|`运算符`|`说明`|
|:-----|:------|
|`not 或 !`|`逻辑非 取反`|
|`and 或 &&`|`逻辑与`|
|`or 或 \|`|`逻辑或`|
|`xor`|`异或`|

##### [5.4 位运算符](#)
`位运算符是将给定操作数转换为二进制后，对每个操作数每一位进行指定的逻辑运算得到的二进制结果转换为二进制就是位运算非结果。`

|`运算符`|`说明`|
|:-----|:------|
|`&`|`位与操作`|
|`|`|`位或操作`|
|`^`|`位异或`|
|`~`|`位取反`|
|`>>`|`位右移`|
|`<<`|`位左移`|

##### [5.4 运算符的优先级](#)
`这个东西，咋说呢！ 真需要的时候上网查询吧！实在是记不住！`