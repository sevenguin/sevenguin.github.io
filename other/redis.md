#Redis
Redis是一个数据结构服务器，但不仅仅是一个简单的键值存储，因为Redis实现了数据结构——允许键包括二进制字符串，hash，set和sorted sets，以及list。这种灵活和快速的结合，使得Redis在很多应用中都是一个理想的工具。

##An Introduction to Redis
内容：如何安装Redis Server和Client，以及Redis的数据类型。  
何时使用Redis？  
Redis是一种NoSQL存储系统，所以需要考虑何时适合使用NoSQL。

1. 你的应用和数据更适合NoSQL？一个考虑的方法是，因为性能原因逆规范化，而且不在从关系型数据库的优点中获益（例如ACID属性）；但是得注意NoSQL不保证ACID；NoSQL数据库一般更快更灵活，这个也是考虑的一个因素
2. 不要相信hype？综合考虑SQL和NoSQL之间的差异和优缺点，根据自己的应用来对每种属性进行赋权值，最后计算得到最优解。（例如稳定性、可预测性、关系数据——SQL；临时的、动态数据——NoSQL）

###安装
可以通过source安装也可以直接使用`yum install redis`(当然不同版本的Linux（还有Mac）可能具体命令不同)安装

###Redis Data Types
理解Redis的数据类型更有利于在特定应用中恰当的使用

`set`、`get`、`incr`、`incrby`、`decr`、`decrby` command;  
`command key (value)`
各个命令前面加上`h`(如`hset`)，则表示数据类型为hash（`hkey/hval/hgetall/hmset`获得键、值、键值、一次设置多个键值对（键值相间隔）），而对于circle（set/list类型）来说，则用`sadd/smembers`来增加、查看值。`sinter`可以求两个circle类型值得交集，而`sunion`可以求并。    
`keys *` 命令可以列出所有数据（键），后面的参数也支持通配符；`type keyname`可以查看数据的类型是set、list还是hash等;注意使用`keys`时，redis会扫描数据库中所有的键值，这个会有点慢。    
`SADD /nonces/key:dpf43f3p2l4k3l03/timestamp:20110306182600 dji430splmx33448` 设置一个时间戳    
`EXPIRE /nonces/key:dpf43f3p2l4k3l03/timestamp:20110306182600 1800`设置这个时间戳过期时间    

###OAuth on Top of Redis
--用Redis来做OAuth
可以使用python做，然后将源码示例传到github上
###Pub/Sub Functionality
--chat system
###Inverted-Index Text Search
倒排索引文本查找——例如以词为索引，文件、文件位置为值，查找文件
###Analytics and Time-Based Data
存储分析结果或者基于时间的数据（例如网站访问情况等）
###Job Queue

