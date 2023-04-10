### mysql

关系型数据库

#### 基本操作

sql语句以；结尾



mysql -uroot -ppass   登录

exit、quit、ctrld、\q    退出



selcet version();    查看数据库版本

select now();  查看时间

show databases;   查看数据库

use  dbname  ； 进入数据库

create database dbname charset=utf8;   默认字符集是拉丁



show create  database  dbname; 查看数据库是什么命令创建的

select  database();   查看当前使用的数据库



drop database   dbname;   

show tables;   查看表

##### 创建表

###### 数据类型

int 、bit



decimal       decimal(5,2)   小数点后2位，整数加小数一共5位

 varchar、 char     

说明：char(3) 固定长度3位字节，不管存不存；varchar(3)    可变长度字符串，存多少用多少

text    存储大文本，当字符长度大于4000时推荐使用

data 、time、datetime

enum

对于图片、音频、视频等文件，可存储在文件服务器，数据库中存储其路径

| 类型          | 字节大小 | 有符号范围(Signed)                       | 无符号范围(Unsigned)   |
| ------------- | -------- | ---------------------------------------- | ---------------------- |
| tinyint       | 1        | -128 ~127                                | 0-255                  |
| smallint      | 2        | -32768~32767                             | 0~65635                |
| mediumint     | 3        | -8388608~8388607                         | 0~16777215             |
| int   integer | 4        | -2147483648~2147483647                   | 0~4294967295           |
| bigint        | 8        | -9223372036854775808~9223372036854775807 | 0~18446744073709551615 |



| 类型    | 字节大小 | 示例                   |
| ------- | -------- | ---------------------- |
| char    | 0-255    | char(3) 存'ab'会占3bit |
| varchar | 0-255    | char(3) 存'ab'会占2bit |
| text    | 0-65535  | 大文本                 |



| 类型 | 字节大小 | 示例                   |
| ---- | -------- | ---------------------- |
| date | 4        | char(3) 存'ab'会占3bit |
| time | 0-255    | char(3) 存'ab'会占2bit |
| text | 0-65535  | 大文本                 |





###### 字段修饰

auto_increment

not null

primary key

default    默认值

unique  

foreign key     外键

signed、unsigned   有无符号

###### 建表语法

字段的字段修饰可以接多个

create  table  tbname(字段1  数据类型  字段修饰，字段2  数据类型  字段修饰)

create  table students(	id  	int  unsigned 	primary key 	not null auto_increment,

​				name varchar(30),

​				age tinyint  unsigned  default 0  ,

​				high decimal(5,2),

​				gender enum("男","女","中性","保密") default  "保密",

​				cls_id int unsigned

​			)；

##### desc 查看 表结构

desc tabname；

##### show create 查看 表创建语句

show create  table  students;



##### insert 存数据

###### 插入单条记录

insert into  students  values(0,'xiaoming',18,188.90,"男",0);

对于enum类型，插入时可以使用元素下标代替元素。

###### 插入部分字段

表名后加(字段1,字段2)    其他未指定字段会是默认值或者空

insert  into students(name,gender) values ('xiaogong',2);

###### 插入多条记录

values后接多个()用逗号分隔即可插入多条

insert into  students  values(0,'xiaoming',18,188.90,"男",0),(1,'xiamen',17,189.90,2,0);

##### select 查数据

select  *  from  students;

select  id,name  from  students;

select * from   students  where  age=18;

###### as 使用别名渲染字段

select  name as 姓名,gender as 性别 from students;

###### as也可将表名渲染

select s.name  as 姓名，s.age as 年龄  form students as  s;  



###### like 模糊查询

%代表一个或多个字符

select  name  from students  where name like "小%";           

_代表一个字符

select  name  from students  where name like "小_";   小开头的两个字的名字

select  name  from students  where name like "__";    两个字的名字

select  name  from students  where name like "__%"; 至少两个字的名字

###### rlike 正则查询

select  name  from students  where name rlike "^小.*";   小开头的名字

select  name  from students  where name rlike "^小.*明$";   小开头、明结尾的名字

###### 范围查询

in 在集合范围内的匹配

select  name  from students  where age in  (12,15,18);    小开头的名字

select  name  from students  where age=12 or age=15 or age=18;

between 在区间范围内的匹配, 区间是闭合的

select  name  from students  where age between  18 and 34;

select  name  from students  where age >=  18 and age<=34;



###### is Null 空值查询 

select  name  from students  where name  is Null;

###### is not Null  非空值查询

select  name  from students  where name  is  not Null;



###### order by  排序

select  name  from students  where (age between  18 and 34)  order by   age  ;   默认升序排序

select  name  from students  where (age between  18 and 34)  order by   age asc ;  升序排序

select  name  from students  where (age between  18 and 34)  order by   age  desc ;   降序排序

select  name  from students  where (age between  18 and 34)  order by   age asc,id desc ;    多排序条件

###### 聚合函数count

select  * from students  where gender=1;

select  count(*)  from students  where gender=1;

select  count(*)   as "男性人数"from students  where gender=1;

select  count(*)   as "女性人数"from students  where gender=2;

###### 求和max

select sum(age) from  students;

###### 平均值avg

select avg(age) from  students;

select sum(age)/count(*)  from  students;

###### 四舍五入round

使用：round(浮点数,保留小数点后几位)

select  round(sum(age)/count(*)  ,2) from  students;

###### 分组 group by

select  gender,count(*)  from students  group by  gender；  根据性别分组，并统计人数

select  gender as 性别,count(*) as 人数 from students  group by  gender；  根据性别分组，并统计人数

select  gender as 性别 ,count(*) as 人数  from students  where gender=1 group by  gender；  统计男性人数

###### 获取分组中的某字段所有值group_concat

select  gender,group_concat(name)  as 姓名 from students  group by  gender；  根据性别分组，显示各分组中所有的姓名记录

select  gender,group_concat(name,age,id)  as 姓名 from students  group by  gender；  根据性别分组，显示各分组中所有记录的姓名+年龄+序号

select  gender,group_concat(name,"_",age," ",id)  as 姓名 from students  group by  gender；  根据性别分组，显示各分组中所有记录的信息，格式为   姓名\_年龄 序号 ，可以自定义加入字符连接各字段。

###### having + group by 做条件判断

having和where作用类似，但是having只能用于和group by组合使用

select gender,group_concat(name),avg(age)  from students group by  gender  having  avg(age)>38;

select gender,group_concat(name),avg(age)  from students group by  gender  having  count(*)>2;

###### 分页

limit  start ,count   ,从第start+1条数据开始取，取count个数据

select * from students  where gender=1   limit 2;

select * from students      limit 0 , 5;    左闭右开，(0，5]

select * from students      limit 0 , 2; 

select * from students      limit 2, 2; 

select * from students      limit 4 , 2; 



连接查询

inter join

left join

right join

子查询

##### alter修改表字段

###### add添加

alter   table    表名  add  字段   字段类型 字段修饰;   

alter  table students  add  birthday  datetime;



###### modify修改字段的字段类型或修饰

alter  table  表名  modify   字段   修改后的字段类型     修改后的字段修饰

alter  table students   modify  birthday  date;

###### change 字段重命名

alter  talbe 表名  change  原名 新名   类型  字段修饰

alter  talbe students  change   birthday  birth  date default '2000-01-01'  

###### drop 删除表字段

alter  table  表名  	  drop  字段;

alter  table  students  drop  birth;

##### 删除表

drop table tbname;

##### update 更新表数据

update students  set  gender =1;

update students  set  gender =1  where  name='xiaomen';

update students  set age=21, gender =1  where  id=3;

##### delete 删除表记录

delete from students where  name="xiaoxi";

###### 清空表

delete from students;

###### 逻辑删除

表字段加一个字段 is_delete 布尔值类型,前端获取该字段判断是否显示。

alter table  students add  is_delete  boolean default  false;

##### distinct 字段数据去重操作

gender重复去除

select distinct gender from students；



##### whre中比较运算符

 `>  `

 `< `

 ` >=` 

 ` <=  ` 

 `!= `

##### whre中逻辑运算符

and 或 &&

or  或 |

not   取反



age>18 and gender="男"

age>18 or gender="男"

not(age>18 and gender="男")