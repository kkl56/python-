#### 基本介绍

redis属于 非关系型数据库 nosql

sql 适用于关系特别复杂的数据查询场景，nosql反之

sql支持事务，nosql基本不支持

key-value形式存储数据



redis支持数据持久化

支持中文，存储中文时是base64格式，需要解码decode()

##### 5种数据类型

string、list、set、zset（有序集合）、hash

支持数据备份，master-slave模式

性能极高，读11万次/s，写8万1千次/s。

端口默认6379

#### 连接命令

redis-cli

16个数据库，0-15

切换数据库 

select  数据库序号（0-15）

桌面客户端 RDM

#### 数据操作

##### 赋值命令

set key value

set name xiaoming

##### 赋值并设置过期时间

单位为秒

 setex key 过期时间  value

setex name  10   xiaoming

##### 给存在的键设置过期时间

expire key  过期时间单位秒

查看键的有效时间

ttl key

##### 获取键值

get k1

##### 获取多个键值

mget k1  k2

##### 查询键

keys  匹配条件（支持正则）

keys *   获取所有键

##### 判断键是否存在

存在返回1，不存在返回0

exists  k1

##### 判断键类型

type  k1

##### 删除键

del k1

del k1 k2



##### 哈希操作

hash用于存储对象，对象的结构为单个或多个属性值，值类型为string

###### 设置单个属性

hset  object  attribute  value

###### 设置多个属性

hmset  object  att1  v1  att2 v2

###### 获取hash的单个属性

hget object  att1

###### 获取hash多个属性

hmget boject att1  att2

###### 获取hash所有属性

hvals object

###### 删除hash单个属性

hdel object  att1

###### 删除hash使用del

del object

##### list操作

列表元素类型为string

###### lpush 从列表左侧插入数据

lpush  key value1  value2

###### rpush从列表右侧插入数据

rpush  key value1  value2



###### lrange从左侧查看列表的元素

0第一个，-1最后一个

lrange key  元素起始下标 元素结束下标 

###### lrem删除列表元素

count  删除元素的次数（元素存在多个重复的情况）如列表[a,b,ab,a,b,a,a,b]

count可取负，count小于0表示从右边开始删除，大于零表示从左往右删，如果count等于零，表示删除列表种所有的该元素

lrem  list   count   元素

##### set操作

无序集合，元素具有唯一性，不重复，集合没有修改操作

###### 添加元素

sadd set1  member1 member2

###### 获取集合元素

smembers set1

###### 删除集合

del set1

###### 删除指定元素

srem set1  member1

##### zset操作

zset有序集合

元素唯一，不重复

每个元素都有一个double类型的score权重，通过score从小到大排序

###### 添加元素

zadd zset1  member1_score  member1       m2_score    m2   m3_score  m3   

###### 获取元素

最左侧元素index为0，-1表示最后一个

zrange zset1   indexa  indexb  

###### 删除集合

del  zset1 

###### 删除元素

zrem zset1  member1 m2  m3



#### python 操作redis

pip install redis

from redis import  Redis

redis_client = Redis(host='localhost',port=6379,db=1)



result =redis_client.set('name','xiaoming')

print(result)



result=redis_client.get('name')

print(result.decode())



result=redis_client.delete('name')

print(result)



result=redis_client.keys()

print(result)



result=redis_client.mset({'name':'zs','age':'16'})

print(result)



#### 配置文件

位置 /etc/redis/redis.conf

bind 127.0.0.1

port 6379

daemonize yes   是否以守护进程运行，守护进程不会在命令行阻塞，类似于服务，

defilename dump.rdb  数据文件名

dir /var/lib/redis   数据文件存储目录

logfile  "/var/log/redis/redis-server.log" 日志文件

datebase 16  默认16个

slaveof   主从复制