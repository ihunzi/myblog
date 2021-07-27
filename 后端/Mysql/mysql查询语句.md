# mysql查询语句

## 准备数据

准备一个数据库
两个表students，classes
准备数据students

```
insert into students values
(0,'小明',18,180.00,2,1,0),
(0,'小月月',18,180.00,2,2,1),
(0,'彭于晏',29,185.00,1,1,0),
(0,'刘德华',59,175.00,1,2,1),
(0,'黄蓉',38,160.00,2,1,0),
(0,'凤姐',28,150.00,4,2,1),
(0,'王祖贤',18,172.00,2,1,1),
(0,'周杰伦',36,null,1,1,0),
(0,'程坤',27,181.00,1,2,0),
(0,'刘亦菲',25,166.00,2,2,0),
(0,'金星',33,162.00,3,3,1),
(0,'静香',12,180.00,2,4,0),
(0,'郭靖',12,170.00,1,4,0),
(0,'周杰',34,176.00,2,5,0);
```

准备数据classes

```
insert into classes values(0,"python_01期"),(0,"python_02期"),(0,"python_04期");
```

## 查询

查询所有字段

```
select * from 表名;
select * from students;
select * from classes;
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
select name,age from students;
```

可以使用as为`字段`指定别名

```
select 字段1 as 别名1,字段2 as 别名2... from 表名;
select name as 姓名,ager as 年龄 from students;
select gender as 性别,name as 姓名 from students;
```

可以使用as为`表`指定别名

```
select 表别名.字段1,表别名.字段2... from 表名  as 表别名;
select sss.name,sss.age from students as sss;
select student.name,student.age from students as sss;(失败);
```

消除重复行

```
select distinct 字段 from 表名;
select distinct gender from students;
```

## 条件查询

```
select ... from 表名 where ....
```

比较运算符

- `>`

查询大于18岁的信息

```
select * from students where age>18;
```

- `<`

查询小于18岁的信息

```
select * from students where age<18;
```

- `<=`
- `>=`

查询小于等于/大于等于18岁的信息

```
select * from students where age<=18;
select * from students where age>=18;
```

- `=`

查询等于18岁的所有学生的名字

```
select name from students where age=18;
```

- `!=`

查询不等于18岁的所有学生的名字

```
select name from students where age!=18;
```

## 逻辑运算符

- `and`

18到28之间的所以学生信息

```
select * from students where age>18 and age<28;
select * from students where age＞18 and ＜28（这样会报错）
```

18岁以上的女性

```
select * from students where age>18 and gender="女";
select * from students where age>18 and gender=2;
```

- `or`

18以上或者身高超过180(包含)以上

```
select * from students where age>18 and height>=180;
```

- `not`

不在18岁以上的女性这个范围内的信息

```
select * from students where not age>18 and gender=2;
select * from students where not (age>18 and gender=2); -- (正确)
```

年龄不是小于或者等于18并且是女性

```
select from students where not age<=18 and gender=2;
```

## 模糊查询

- `like`
  - `%`替换1个或多个
  - `_`替换1个（必须有一个）

查询姓名中 以“小”开始的名字

```
// select name from students where name="小"; 
select name from students where name like "小%"; 
```

查询姓名中 有“小”的名字

```
select name from students where name like "%小%"; 
```

查询有2个字的名字

```
select name from students where name like "__"; 
```

查询有3个字的名字

```
select name from students where name like "___"; 
```

 查询至少有2个字的名字

```
select name from students where name like "__%"; 
```

- `rlike `正则

查询以 周 开始的姓名

```
select name from students where name rlike "^周.*";
```

查询以 周 开始，伦 结尾的姓名

```
select name from students where name rlike "^周.*伦$";
```

## 范围查询

- `in`

in(1，3，8)表示在一个非连续的范围内

查询年龄为18、34的姓名

```
select name,age from students where age in (12,18,34);
```

- `not in`不非连续性的范围之内

查询年龄不是18、34之间的信息

```
select name,age from students where age not in (12,18,34);
```

- `between ... and ... `表示在一个连续的范围内

查询年龄在18到34之间的的信息

```
select name,age from students where age between 18 and 34;
```

- `not between ... and ...`表示不在一个连续的范围内

查询年龄不在 在18到34之间的的信息

```
select * from students where age not between 18 and 34;
// 下面是失败的例子
select * from students where age not (between 18 and 34);
select * from students where not age between 18 and 34;
```

## 空判断

- 判空 `is null`

查询身高为空的信息

```
select * from students where height is null;
```

- 判非空 `is not null`

```
select * from students where height is not null;
```

## 排序

- `order by` 字段

- `asc`从小到大排列，即升序
- `desc`从大到小排序，即降序

查询年龄在18到34岁之间的男性，按照年从小到大排序

```
select * from students where (age between 18 and 34) and gender=1;
select * from students where (age between 18 and 34) and gender=1 order by age;
select * from students where (age between 18 and 34) and gender=1 order by age asc;//从小到大
select * from students where (age between 18 and 34) and gender=1 order by age desc;//从大到小
```

查询年龄在18到34岁之间的女性，身高从高到矮排序

```
select * from students where (age between 18 and 34) and gender=2 order by height desc;
```

- `order by` 多个字段

查询18到34岁之间的女性，身高从高到矮排序，如果身高相同的情况下按照年龄从小到大排序

```
select * from students where (age between 18 and 34) and gender=2 order by height desc,age asc;
```

查询18到34岁之间的女性，身高从高到矮排序，如果身高相同的情况下按照年龄从小到大排序,如果年龄也相同那么按照id从大到小排序

```
select * from students where (age between 18 and 34) and gender=2 order by height desc,age asc,id desc;
```

按照年先按龄从小到大、再同时按身高从高到矮的排序

```
select * from students order by age asc,height desc;
```

## 聚合函数

- `count`总数

查询男性有多少人，女性有多少人

```
select count(*) as 男性人数 from students where gender=1;
select count(*) as 女性人数 from students where gender=2;
```

- `max`最大值
- `min`最小值

查询最大/最小的年龄

```
select age from students ;
select max(age) as 最大年龄 from students ;
select min(age) as 最大年龄 from students ;
```

查询女性的最高/最矮身高

```
select max(height) as 最高身高 from students where gender=2;
select min(height) as 最高身高 from students where gender=2;
```

- `sum`求和

计算所有人的年龄总和

```
select sum(age) from students ;
```

- `avg`平均值

计算所有人的平均年龄

```
select avg(age) from students ;
```

计算平均年龄 sum(age)/count(*)

```
select sum(age)/count(*) from students ;
```

- ` round(123.23,1)`四舍五入，保留一位小数

计算所有人的平均年龄，保留两位小数

```
select round(sum(age)/count(*),2) from students;
```

计算男性的平均身高，保留两位小数

```
select round(avg(height),2) from students where gender=1;
```

## 分组

> 分组(配合聚合函数使用)

- `group by`

按照性别分组，查询所有的性别

```
select gender from students group by gender;
```

计算每种性别的信息

```
select gender,count(*) from students group by gender;
select gender,avg(age) from students group by gender;
select gender,max(age) from students group by gender;
```

计算男性的人数

```
select gender,count(*) from students where gender=1 group by gender;
```

- `group_concat(...)`

查询同种性别中的姓名

```
select gender,group_concat(name) from students where gender=1 group by gender;
select gender,group_concat(name,age,gender) from students where gender=1 group by gender;
select gender,group_concat(name,"_",age,"_",gender) from students where gender=1 group by gender;
```

- `having` 查出结果进行判断

查询平均年龄超过30岁的性别，以及姓名` having avg(age) >30`

```
select gender,group_concat(name),avg(age) from students group by gender having avg(age) >30;
```

查询每种性别中的人数多于2的信息

```
select gender,group_concat(name) from students group by gender having count(*)>2;
```

## 分页

- `limit start`(开始位置)，`count`(总数)

限制查询出来的数据个数

```
select * from students where gender=1 limit 2;
```

查询前5个数据

```
select * from students where gender=1 limit 开始位置,数据个数;
select * from students where gender=1 limit 0,5;
select * from students where gender=1 limit 5,5;
select * from students where gender=1 limit 10,5;
```

查询id6-10（包含）的顺序

```
select * from students where gender=1 limit 5,5;
```

每页显示2个，第1个页面

```
select * from students where gender=1 limit 0,2;
```

每页显示2个，第2个页面

```
select * from students where gender=1 limit 2,2;
```

每页显示2个，第3个页面

```
select * from students where gender=1 limit 4,2;
```

每页显示2个，第4个页面

```
select * from students where gender=1 limit 6,2;
```

**limit (第N页-1)*每页个数，每页个数；**

每页显示2个，显示第6个页面的信息，按照年龄从小到大排序

limit 在最后

```
（失败）select *from students limit 2*(6-1),2;
（失败）select *from students limit 10,2 order by age asc;
select * from student order by age asc limit 10,2;
```

 查询所有女性的身高信息，从高到矮排序，只显示两个

```
select * from students where gender=2 order by height desc limit 0,2；
```

## 连接查询

- `inner join ... on`

select ... from 表A inner join 表B;

```
select * from students inner join classes;
```

查询 有能够对应班级的学生以及班级信息

```
select * from students inner join classes on students.cls_id=classes.id;
```

按照要求显示姓名班级

```
select students.* ,classes.name from students inner join classes on students.cls_id=classes.id;
```

给数据表起名字

```
select s.name ,c.name from students as s inner join classes as c on s.cls_id=c.id;
```

查询 有能够对应班级的学生以及班级信息，显示学生的所有信息，只显示班级名称

```
select s.* ,c.name from students as s inner join classes as c on s.cls_id=c.id;
```

在以上查询中，将班级姓名显示在第1列

```
select c.name,s.*  from students as s inner join classes as c on s.cls_id=c.id;
```

查询 有能够对应班级的学生以及班级信息，按照班级进行排序

```
select c.name,s.*  from students as s inner join classes as c on s.cls_id=c.id order by c.name;
```

当时同一个班级的时候，按照学生的id进行从小到大排序

```
select c.name,s.*  from students as s inner join classes as c on s.cls_id=c.id order by c.name,id;
```

- `left join`

查询每位学生对应的班级信息

```
select * from students as s left join classes as c on s.cls_id=c.id;
```

查询没有对应班级信息的学生

从原表里面找用where，从结果表中找用having

```
select * from students as s left join classes as c on s.cls_id=c.id where c.id is null;	
select * from students as s left join classes as c on s.cls_id=c.id having c.id is null;
```

`right join`

将数据表名字互换位置，用left join完成

## 自关联

一张表的时候

```
select * from areas as province inner join areas as city on city.pid=province.aid having province.atitle="xx省";
```

## 子查询

> selcet语句里面嵌套一个子select，先执行子select

查询最高的男生信息

```
select * from students where height = (select max(height) from students);
```

