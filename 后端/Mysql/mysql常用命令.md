# mysql常用命令

> 导读

## 数据库的操作

链接数据库

```
mysql -uroot -pPassword
```

退出数据库

```
exit/quit/ctrl+d
```

显示数据库版本

*sql语句最后需要有分号 ; 结尾*

```
select version();
```

显示时间

```
select now();
```

查询所有数据库

```
show databases;
```

创建数据库

```
create database 数据库名 charset=utf8;
create database python_test;
create database python_test charset=utf8;
```

使用数据库

```
use 数据库名;
use python_test;
```

查看当前使用的数据库

```
select database();
```

查询创建数据库的语句

```
show create database python_test;
```

删除数据库

```
drop database `python_test`;
```

## 表的操作

查看当前数据库中所有表

```
show tables;
```

创建表

```
auto_increment 表示自动增长
not null 表示不能为空
primary key 表示主键
default 默认值
create table 数据表名字 （字段 类型 约束，字段 类型 约束);
最后一个字段不要有 ；

创建classes表（id，name）
create table XXXXX(id int, name varchar(30));

create table XXXXX(
	id int primary ke bot null auto_increment, 
	name varchar(30) 
);
```

查看表结构

```
desc 数据表的名字
```

创建student表（id，name，age，high，gender，cls_id)

```
create table student(
		id int unsigned not null auto_increment primary key,
		name varchar(30),
		age tinyint unsigned default 0,
		high decimal(5,2),
		gender enum("男","女","保密") default "保密",
		cla_id int unsigned
);
```

查询表中数据

```
select * from 表名
```

### 修改表

添加字段

```
alter table 表名 add 字段名 类型及约束;
alter table student add birthday datetime;
```

修改字段：不更改字段名版

```
alter table 表名 modify 字段名 类型及约束;
alter table student modify birthday date;
```

修改字段：更改字段名版

```
alter table 表名 change 旧字段名 新字段名 类型及约束;
alter table student change birthday birth date default "2000-01-01";
```

删除字段

```
alter table 表名 drop 字段名;
alter table student drop birth;
```

### 删除表

```
drop table 表名;
drop table student;
```

### 外键

```
alter table 表A名 add foreign key (表A字段名) references 表B名 (表B字段名);
```

## 数据的增删改查

### 增加

全列插入

```
insert into 表名 values (,,,,,,,);
主键字段 可以用 0/null/default来占位
insert into student values(0,"老李","21","170.00","女",12321,"2019-8-12");
insert into student values(null,"老李","21","170.00","女",12321,"2019-8-12");
insert into student values(default,"老李","21","170.00","女",12321,"2019-8-12");
```

**枚举中的下标从 1 开始**

部分插入

```
insert into 表名 (字段名1，字段名2，) values(数据1，数据2，);
insert into student (name,gender) values("小乔",2);
```

多行插入

```
insert into 表名 (字段名1，字段名2，) values(数据1，数据2，),(数据1.1，数据2.2，);
insert into student (name,gender) values("小乔",2), ("大桥",2), ("貂蝉",3);
insert into student values(0,"西施","22","175.00","女",666,"0119-8-12"),(0,"王昭君","26","165.00","女",996,"0219-8-12");
```

### 修改

```
update 表名 set 字段1=数据1,字段2=数据2... where 条件;
update student set gender=1;
update student set gender=1 where name="小李飞刀";
update student set gender=1 where id=3;
update student set gender=1,age=33  where id=3;
```

### 查询

查询所有字段

```
select * from 表名;
select * from student;
```

一定条件查询

```
select * from 表名 where 条件;
select * from student where name="老王";
select * from student where id=3;
```

查询指定字段

```
select 字段1,字段2... from 表名;
select name,gender from student;
```

可以使用as为字段或表指定别名

```
select 字段1 as 别名1,字段2 as 别名2... from 表名;
select name as 姓名,gender as 性别 from student;
select gender as 性别,name as 姓名 from student;
```

### 删除

物理删除（真删）

```
delete from 表名 where 条件;
delete from student; 		// 整个表删除
delete from student where name="貂蝉";
```

逻辑删除（假删）

> 用一个字段来表示，这条信息是否已经不能再使用了
>
> 给student表添加一个is_delete字段，bit 类型

```
alter table student add is_delete bit default 0;
```

