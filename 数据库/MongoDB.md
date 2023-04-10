### 数据类型

文档id  object id

字符串    String

布尔值  Boolean

整型  Integer

浮点型  Double

数组   Arrays  

文档   Object

空值  Null

时间戳 Timestamp  

UNIX格式日期 Date 

### 常规使用

show  dbs

use db

show collections







#### 数据插入



db.col.insert({name:'xiaoming'})

终端中键可以省略双引号

#### 数据保存

文档中不存在_id相同的记录则添加，存在则修改

db.col.save({_id:10010,name:'xiaoming',age:16})

如果使用db.col.insert插入一条_id已经存在的记录，将会报错 _id重复



#### 数据更新

一般使用

db.col.update({查的键值对},{$set:{更新键值对}},{multi:布尔值是否更新查询的所有记录})

将col文档中所有的name键为xiaoming的值改为xiaomeng

db.col.update({name:'xiaoming'},{$set:{name:'xiaomeng'} },{multi:true})



不使用$set关键字会将没有指定的其他字段清除

db.col.update({查的键值对},{更新键值对},{multi:布尔值是否更新查询的所有记录})

db.col.update({name:'xiaoming'},{name:'xiaomeng'})

db.col.update({name:'xiaoming'},{name:'xiaomeng'},{multi:true})



#### 数据删除

db.col.remove({查询条件也就是一个键值对},{justOne:布尔值是否只删除搜索道德的第一条记录})



#### 数据查找

db.col.find({查询条件键值对},{justOne:布尔值是否只显示查到的第一条记录})



db.col.find()   括号内不写查询条件，返回col中所有记录



使用findOne({查询条件})可以起到和find({查询条件},{justOne:true})参数也可以



#### .pretty()输出格式化

如db.col.find().pretty()



### 比较运算符

#### 种类

等于，默认键值对条件就是等于

小于,  $lt

小于等于, $lte

大于,$gt

大于等于, $gte

不等于,$ne

#### 使用方法

{键:{比较运算符:值}}

如查找年龄大于18的记录 ：  db.col.find({age:{$gt:18}})  

### 范围运算符

in

类似于python语法中in操作

使用方法 {键:{$in: [值1,值2,值3] }}

 db.col.find({age:{$in:[18,20,22]}})  

### 逻辑运算符

##### 且

写多个查找条件键值对

db.col.find({键值对1},{键值对2})  

##### 或，$or

使用方法： 

​	{  $or：[ {条件1}，{条件2}，{条件3} ]  }



### 正则

使用/正则表达式/   或 $regex

db.col.find({键:/正则表达式/})

db.col.find({键:{$regex:正则表达式}})



db.col.find({name:/^xiao/})   以xiao开头

db.col.find({name:{$regex:wang$}})   以wang结尾



### limit()和skip()

对查找结果进行操作,可以用于分页

limit(整型) ，限制查到的记录数量

skip(整型)，跳过前几条记录

db.col.find({age:{$gt:18}}).skip(5).limit(5)



### $where 自定义查询

支持使用js函数

db.col.find({$where:function(){return this.age<=18} })

即将function(){return this.age<=18}放入到$where后作为查询函数，this代指col文档对象，对象的调用方法是this.age，就是指文档的age字段



### 投影

类似mysql中select功能，显示需要的字段

1显示，0不显示

db.col.find({查询条件键值对},{字段a:0或1,字段b:0或1,字段:0或1})



### sort()排序

1升序，-1降序

db.col.find().sort({排序指标字段a:1或-1  ,  排序指标字段b:1或-1})



### count() 记录统计求和

db.col.find().count()  

db.col.find({age:{$gt:18}}).count()  统计大于18岁的记录数

db.col.count({age:{$gt:18}})   直接使用count，省略find  

### distinct() 数据去重

db.col.distinct('hometown') 对hometown去重，显示一个列表

可以指定条件，对查询结果去重

db.col.distinct('hometown',{age:{$gt:20}})



### 数据备份和恢复

mongodump -h host -d db  -o dir   将数据库备份到dir

mongoresore   -h host -d db  --dir  dir

-h后接ip:port

### 聚合函数

#### 常用管道

$group  分组

$match  过滤数据

$project  修改输出表字段为可读，重命名，增加，删除字段

$sort   排序

$limit  限制聚合管道返回的文档数

$skip  跳过指定数量的文档，并返回余下的文档

$unwind 将数组类型的字段进行拆分

#### 表达式

$sum

$avg

$min

$max

$push

$first   获取第一个文档记录

$last   获取最后一个文档记录



#### 使用

聚合函数

##### $group

$group  分组后统计，需要指定分组的指标，使用 _id:$分组指标键  来指定，

db.col.aggregate(

​	{ 

​		$group:{

​			_id:$分组指标键，

​			第二个字段名称要自定义:{$函数操作符:{...} } , 

​			第三个字段名称要自定义:{$函数操作符:{...} }

​			 }

​	}

)



如对gender分组，然后每个分组对记录条数求和,$sum后数字是对统计数做乘积运算

db.col.aggregate(

​	{ 

​		$group:{

​			_id:$gender，

​			count:{$sum:1}  

​			 }

​	}

)

如对性别gender进行分组，分组后计算平均年龄，会有三个字段，第一个字段是性别，第二个字段是统计的数量count，第三个字段是平均年龄avg_age,每个函数操作结果就是自定义字段的值

db.col.aggregate(

​	{ 

​		$group:{

​			_id:$gender，

​			count:{$sum:1}  , 

​			avg_age:{$avg:''$age"} 

​		 	}

​	}

)

##### _id:null,分组依据为空的$group

$group

所有记录为一组，计算记录数和平均年龄

db.col.aggregate(

​	{ 

​		$group:{

​			_id:null，

​			count:{$sum:1}  , 

​			avg_age:{$avg:''$age"} 

​		 	}

​	}

)

##### $group使用要点

_id后接分组依据的字段

$group()后的字典有几个字段，返回的数据结果就有几个字段

$group后字典中引用文档中字段时，需要在字段前加$,如 $gender 、 $age

可以对多个字段进行排序，写法是   _id:[分组依据字段a,分组依据字典b]

##### $project 修改输出文档的字段名称

###### 对字段的显隐操作

_id字段隐藏，count、avg_age字段显示

db.col.aggregate(

​	{ 

​		$group:{

​			_id:null，

​			count:{$sum:1}  , 

​			avg_age:{$avg:''$age"} 

​		 	}

​	},

​	{       $project:   {_id:0 ,count:1 ,avg_age:1}        }

)

###### 对字典的重命名操作

db.col.aggregate(

​	{ 

​		$group:{

​			_id:null，

​			count:{$sum:1}  , 

​			avg_age:{$avg:''$age"} 

​		 	}

​	}，

​	{

​		$project:{

​			"性别":"$_id",

​			"人数统计":"$count",

​			"平均年龄":"$avg_age"

​			}	

​	}

)



##### $match 在聚合管道中使用

$match可以将结果给聚合函数中的下一个管道处理，find不能

db.col.aggregate(

​		{

​			$match:{

​				{age:{$gt:18}

​				}

​		},

​		{

​			$group:{

​				_id:null，

​				count:{$sum:1}  , 

​				avg_age:{$avg:''$age"} 

​			 	}

​		}

)

##### $limit、$skip在聚合函数中的使用

方式同上

db.col.aggregate(

​	{ 

​		$group:{

​			_id:null，

​			count:{$sum:1}  , 

​			avg_age:{$avg:''$age"} 

​		 	}

​	}，

​	{

​		$project:{

​			"性别":"$_id",

​			"人数统计":"$count",

​			"平均年龄":"$avg_age"

​			}	

​	},

​	{

​		$skip:5

​	},

​	{

​		$limit:1

​	}

)

##### $sort、$unwind同理



### 索引



js脚本创建实验数据

for(i =0;i<100000;i++){

​	db.col.insert((name:'test' + i ,age: i))

}

db.col.find({name:"test10000"})

db.col.find({name:"test10000"}).explain("executionStats")    获取查询耗时



1表示升序，-1表示降序,unique表示是否为唯一索引，默认创建的不是唯一索引,dropDups可以去重

db.col.ensureIndex({要建立索引的键:1或-1},{"unique":boolean,"dropDups":boolean})



db.col.getIndexes()  获取索引

设置多个键的联合索引

db.col.ensureIndex(name:1,age:1)

删除索引

db.col.dropIndex({name:1})



### python中操作mongodb

pip install pymongo  -i  https://pypi.douban.com/simple



from pymongo  import    MongoClient

client=MongoClient(host='127.0.0.1',port=27017)

col = client['db']\['col']



res = col.insert_one({"name":'xiaoming',"age":10})

print(res)

date_list=[    {"name":"test{}".fromat(i) }       for i in range(10)]

col.insert_many(date_list)



t1 = col.find_one("name":"xiaoming")

print(t1)

t2 = col.find("name":"xiaoming")

#t2 是一个游标对象

print(t2)

for i in t2:

​	print(i)

#通过list(t2)转换成可读数据

print(list(t2))



col.update_one("name":"xiaoming",{"$set":{"name":"xiaohong"}})

#更新多条

col.update_many("name":"xiaoming",{"$set":{"name":"xiaohong"}})



col.delete_one("name":"xiaoming")

col.delete_many({"name":"xiaohong"})



