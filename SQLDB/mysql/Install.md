#### [MySQL 安装教程](#top) <b id="top"></b> 

----
`不会linux直接用window环境安装！甚至快捷，直接开始学习！ ` [`官方安装地址`](https://dev.mysql.com/doc/refman/8.0/en/installing.html)


- [x] [`1. 简述`](#1-简述)
- [x] [`2. Windows安装mysql`](#2-windows安装mysql)
- [x] [`3. Ubuntu 安装mysql`](#3-ubuntu-安装mysql)
- [x] [`4. CentOS7 Yum 安装MYSQL`](#4-centos7-yum-安装mysql-80)
- [x] [`5. 安装后需要配置的内容`](#5-安装后需要配置的内容)
    - [x] [`新版本MYSQL的变化`](#change)
    - [x] [`MYSQL 大小写敏感问题`](#ignore)


------

##### [1. 简述](#) 
`MYSQL 有社区版本，和企业版本。一般我们都是使用免费的社区版本。白嫖嘛，谁不喜欢呢！`

##### 1.1 mysql不同版本的重要改进
`MYSQL 数据经过多年的累进发展，历经了许多版本调整，主要如下` `作为一款开源软件，其发展有着许多里程碑的事件，2000年采用GUN GPL许可协议， BDB存储引擎来自Berkeley DB. InnoDB数据库存储引擎来自
芬兰Innobase OY公司。 2002年全面支持事务，满足事务ACID属性。支持外键约束。....` `ANSI/ISO是工人的关系数据库标准，但是MYSQL并不是符合标准最好的开源数据库。他在标准与性能方面做了取舍，优先
考虑性能因素。这也是MYSQL性能好的原因之一。因为许多高级的功能特性，用户并不经常使用，好会增加系统的复杂性、`

|`版本`|`重要改进`
|:----|:----|
|`4.1`|`增加了子查询的支持；增加了字符集中对UTF-8的支持`|
|`5.0`|`增加了视图、过程、触发器的支持，增加了 infomation_schema （信息数据库）系统库`|
|`5.1`|`增加了表分区的支持；支持基于行的复制 (row-based replication)`|
|`5.5`|`InnoDB 成为默认存储引起；支持半同步复制；引入 performance_schema 动态性能视图`|
|`5.6`|`支持 Online DDL操作；支持 ICP / BKA / MRR 等优化器改进；引入GTID支持多库并行复制`|
|`5.7`|`密码安全性提高；支持多线程并行复制、多源复制；支持 JSON ；引入sys系统库；引入MGR`|
|`8.0`|`在线持久化全局参数；大幅提高数据字典性能；引入窗口函数、ROLE、直方图、降序索引、不可见索引；修复自增列重启BUG`|

##### [2. windows安装mysql](#) 
|`版本`|`重要改进`
|:----|:----|
|`mysql-installer-web-community-xxx.msi installer`|`需要联网安装，安装包小。资源从网上获得`|
|`mysql-installer-community-8.0.27.1.msi`|`安装包，包含所有组件，向导式安装，可以灵活选择安装，删除，更改MYSQL，提供MySQL所有的组件`|

`下载好点击安装就可以了，很简单的！`

`Windows 平台配置MySQL 可以使用mysql-installer-community。里面有个reconfigurate按钮。如果你要改变默认配置的话。` `当然也可以直接修改安装路径下面的 my.ini配置文件。`

`my.ini 文件一些常见字段。`
```ini
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 

[mysqld]

#设置3306端口
port = 3306 
# 设置mysql的安装目录
basedir=D:\mysql\mysql-5.6.17-winx64

# 设置mysql数据库的数据的存放目录
datadir=D:\mysql\mysql-5.6.17-winx64\data

# 允许最大连接数
max_connections=200

# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```
`MySQL在windows下是不区分大小写的，将script文件导入MySQL后表名也会自动转化为小写。Linux系统是大小写敏感的。我也不知道谁这样设计的。真的是看不懂。` `找到mysql安装目录下的my.ini文件，在文件中最后添加（1表示大小写不敏感，2表示大小写敏感）`

```ini
lower_case_table_names=2
```


##### 2.1 启动和关闭MySQL服务
`启动服务,首先你要知道你安装的MYSQL服务的名称。加入其名称为 MySQL80`
```shell
net start mysql80
```
`关闭服务`
```shell
net stop mysql80
```

`搞定！`

##### [3. Ubuntu 安装mysql](#)  
`Linux 这个命令行模式下安装，就要一些小的操作了！` `以下操作均在root用户下进行，如在普通用户，请自行加上sudo！` `建议你先看看 ` `apt-get`

```shell
# 查看有没有安装MySQL：
dpkg -l | grep mysql

# 安装MySQL：
apt install mysql-server
```
`安装完成之后可以使用如下命令来检查是否安装成功：`
```shell
root@ecs-135748:~# netstat -tap | grep mysql
#tcp        0      0 localhost:mysql         0.0.0.0:*               LISTEN      27486/mysqld    
```
`登录mysql数据库可以通过如下命令：`

```shell
mysql -u root -p
```
`-u 表示选择登陆的用户名， -p 表示登陆的用户密码，现在是mysql数据库是没有密码的，Enter password:处直接回车，就能够进入mysql数据库。`
`然后通过 show databases; 就可以查看当前的所有数据库。`

`退出数据库！`
```shell
mysql> quit; 
```

`接下来，为了确保数据库的安全性和正常运转，对数据库进行初始化操作。这个初始化操作涉及下面5个步骤。`

`安装验证密码插件。`  
`设置root管理员在数据库中的专有密码。`  
`随后删除匿名账户，并使用root管理员从远程登录数据库，以确保数据库上运行的业务的安全性。`  
`删除默认的测试数据库，取消测试数据库的一系列访问权限。`  
`刷新授权列表，让初始化的设定立即生效。` 


```shell
mysql_secure_installation
```

`关键点:`
```shell
secure enough. Would you like to setup VALIDATE PASSWORD plugin?    # 要安装验证密码插件吗?
Press y|Y for Yes, any other key for No: N    # 这里我选择N

#...
New password:   # 输入要为root管理员设置的数据库密码
Re-enter new password:   # 再次输入密码

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y     # 删除匿名账户
Success.

#...

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : N    # 禁止root管理员从远程登录，这里我没有禁止

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y   # 删除test数据库并取消对它的访问权限
- Dropping test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y   # 刷新授权表，让初始化后的设定立即生效
Success.
```

`检查mysql服务状态：`
```shell
systemctl status mysql
```
`再次用mysql -u root -p命令，Enter password:处输入刚设置的密码，回车，就能够进入mysql数据库。`

`使用 use mysql; 命令打开mysql命名的数据库，显示当前数据库的表：show tables; 查询user表里的数据：select * from user;（user表里是mysql数据库的所有账户信息）`
```shell
use mysql;

show tables;

select * from user;
```
`现在配置mysql允许远程访问，首先编辑 /etc/mysql/mysql.conf.d/mysqld.cnf 配置文件：`  
`修改bind-address          = 0.0.0.0`
```shell
35 basedir         = /usr
36 datadir         = /var/lib/mysql
37 tmpdir          = /tmp
38 lc-messages-dir = /usr/share/mysql
39 skip-external-locking
40 #
41 # Instead of skip-networking the default is now to listen only on
42 # localhost which is more compatible and is not less secure.
43 bind-address           = 0.0.0.0
44 #
45 # * Fine Tuning
46 #
47 key_buffer_size         = 16M
48 max_allowed_packet      = 16M
49 thread_stack            = 192K
50 thread_cache_size       = 8

```
`保存退出，然后进入mysql数据库，执行授权命令：` `新版的的mysql版本已经将创建账户和赋予权限的方式分开了`

`mysql 8.0版本使用！`
```sql
use mysql;
select user, host from user;

-- 允许root 远程
update user set host = '%' where user = 'root';

-- 禁止root 远程
update user set host = 'localhost' where user = 'root';

-- 给root用户授权 不然可能出现 You are not allowed to create a user with GRANT
GRANT ALL ON *.* TO 'root'@'%' ;
```


`mysql 5.7 及其以下版本使用这个`
```mysql
mysql -u root -p

mysql> grant all on *.* to root@'%' identified by '你的密码' with grant option;

mysql> flush privileges;    # 刷新权限

mysql> exit
```
`然后执行exit命令退出mysql服务，再执行如下命令重启mysql：`

```shell
systemctl restart mysql
```

##### [3.1 启动和关闭数据库](#)
`linux 环境下`
```shell
service mysql start  //启动
service mysql restart //重启
service mysql stop  //关闭
```

##### [3.2 mysql 8.0 用户管理小例子](#)
`通常我们会这样进行用户管理！`
```sql
create database paydb;  -- 创建数据库：paydb

create user payuser identified by 'EDRCRFT44434^^&#@WWS';  -- 创建账号和密码，账号:payuser，密码：EDRCRFT44434^^&#@WWS

grant all on paydb.* to 'payuser'@'%'  with grant option; 
-- 授权账号和密码只能从本机访问这个数据库 修改权限时在后面加with grant option；


-- 禁止root 远程
update user set host = 'localhost' where user = 'root';

flush privileges; # 刷新权限

-- 退出mysql 然后重启
```

```mysql
service mysql restart //重启
```


##### [4. CentOS7 Yum 安装MYSQL 8.0](#)
`使用Yum 安装MYSQL` [`MYSQL YUM源`](https://dev.mysql.com/downloads/repo/yum/) `安装其他版本只需要替换yum包就行了`

`下载 rpm 包`:`使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了。`
```shell
wget -i -c https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
```
`安装mysql rpm`
```powershell
yum -y install https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
```
`安装MYSQL 服务器`
```powershell
yum -y install mysql-community-server
```
`安装好了之后 打开MYSQL服务 `
```powershell
systemctl start  mysqld.service
systemctl status mysqld.service --查看服务状态
```
`不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：`
```c
grep "password" /var/log/mysqld.log
//登陆mysql
mysql -uroot -p  
```
`修改密码 运行远程连接mysql`
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
update user set host = '%' where user = 'root';
```

##### [5. 安装后需要配置的内容](#)


##### [4.1 新版本MYSQL的变化](#)  
`新版本的mysql 配置文件 my.ini文件 不在 安装根目录下 了  而在另一个文件夹下面 ` `C:\ProgramData\MySQL\MySQL Server 8.0`


##### [4.2 MYSQL 大小写敏感问题](#)
`C:\ProgramData\MySQL\MySQL Server 8.0 找打my.ini 文件 将里面的 lower_case_table_names 设置为2` `因为mysql 数据库信息存放在表中 所以表对
大小写敏感 所有信息都对大小写敏感了`  `大消息不敏感可以设置为 1 `

```sql
show variables like 'lower%'; /*可以查看*/

|lower_case_file_system	| ON|
|lower_case_table_names|1|
```

`lower_case_file_system：OFF表示大小写敏感，ON表示大小写不敏感。lower_case_table_names：0表示敏感，1表示不敏感。`
```xml
lower_case_table_names=1
```
