# 基础篇

## 安装启动

###### CentOS安装MongoDB6

1. 创建目录

```shell
mkdir mongodb/{config,log,data}
```

2. 软件包解压重命名

```shell
tar -zxvf mongodb-linux-x86_64-rhel70-6.0.14.tgz
mv mongodb-linux-x86_64-rhel70-6.0.14 app
```

3. ==创建用户==

```
# 1.启动mongod
app/bin/mongod --fork --dbpath=/root/software/mongodb/data --logpath=/root/software/mongodb/log/mongod.log

# 2.mongosh连接
mongosh/bin/mongosh -port 27017

# 3.创建用户
use admin
db.createUser({user:"root",pwd:"root",roles:["root"]})

# 关闭mongod服务
db.shutdownServer()
```

4. `配置文件`

```yaml
# vim config/mongod.config
 
# 日志文件位置
systemLog:
  destination: file
  logAppend: true
  path: /root/software/mongodb/log/mongod.log
 
# 数据存储位置
storage:
  dbPath: /root/software/mongodb/data
  journal:
    enabled: true
# 存储引擎
#  wiredTiger:
 
# 进程运行(开启守护进程;设置时区)
processManagement:
  timeZoneInfo: /usr/share/zoneinfo
  fork: true

# 网络配置(设置为0.0.0.0后可以通过外网访问，远程连接mongodb)
net:
  port: 27017
  bindIp: 0.0.0.0

# 访问控制(启用或禁用基于角色的访问控制,远程连接mongodb)
security:
  authorization: enabled
```

5. **启动mongod**

```she
app/bin/mongod -f config/mongod.config
```

6. 登录

```shel
# 1.启动mongosh
mongosh/bin/mongosh

# 2.切换到admin库
use admin

# 3.用户密码登录
db.auth("root","root")
```

> 1. ==mongod==是MongoDB系统的主要守护进程,负责处理数据请求，管理数据访问并执行后台管理操作(服务器)
> 2. `mongo`是MongoDB的交互式JavaScript Shell界面，它为系统管理员提供了强大的界面，并为开发人员提供了一种直接通过数据库测试查询和操作的方式(客户端)
> 3. 对于分片群集，**mongos**实例提供客户端应用程序和分片群集之间的接口

## 入门简介

###### MongoDB是什么

MongoDB是一个文档数据库（以 JSON 为数据模型），由C++语言编写，旨在为WEB应用提供可扩展的高性能数据存储解决方案

###### MongoDB特点

1. 易于使用

==MongoDB中也没有预定义模式(predefined schema):文档键值的类型和大小不是固定的==

2. 易于扩展

MongoDB的设计采用了横向扩展。面向文档的数据模型使跨多台服务器拆分数据更加容易。MongoDB会自动平衡跨集群的数据和负载，自动重新分配文档，并将读写操作路由到正确的机器上

![image-20240318194650456](./assets/image-20240318194650456.png)

3. 功能丰富

* 索引
* 聚合
* 特殊的集合(`固定集合`)和索引类型(`TTL`)
* **文件存储**

4. 性能卓越

###### GridFS是什么

***GridFS 是一种用于存储大型文件的协议，它使用子集合将文件元数据与内容块分开存储***

###### MongoDB部署模型

![image-20240306220125593](./assets/image-20240306220125593.png)。

###### 文档中的key键规则

* 键中不能含有\0(空字符),这个字符用于表示一个键的结束
* `. 和 $ 是特殊字符，只能在某些特定情况下使用`
* **MongoDB会区分类型和大小写**
* 不能包含重复的键

###### 集合名命名规则

* 不能是空字符串("")
* 不能含有\0(空字符)，因为这个字符用于表示一个集合名称的结束
* 不能以`system.`开头，该前缀是为内部集合保留的
* 不应包含保留字符`$`
* ==使用 . 字符分隔不同命名空间的子集合==

###### 数据库命名规范

* 数据库名称不能是空字符串("")
* 数据库名称不能包含特殊字符以及 \0 (空字符)，基本上只能使用ASCII字母和数字
* 数据库名称区分大小写
* 数据库名称的长度限制为64字节

###### 系统数据库

* admin

***admin数据库会在身份验证和授权时被使用***

* local

特定于单个服务器的数据会存储在此数据库中。在副本集中，local用于存储复制过程中所使用的数据，而local数据库本身不会被复制

* config

MongoDB的分片集群会使用config数据库存储关于每个分片的信息。通过将数据库名称与该库中的集合名称连接起来，可以获得一个完全限定的集合名称，称为命名空间;命名空间的长度限制为120字节，而实际使用时应该小于100字节

# 中级篇

## 一 数据类型

###### MongoDB有哪些数据类型

1. NULL

null类型用于表示空值或不存在的字段

2. 布尔类型

布尔类型的值可以为true或者false

3. 数值类型

shell默认使用64位的浮点数来表示数值类型

对于整数，可以使用`NumberInt`或`NumberLong`类，它们分别表示***4字节和8字节***的有符号整数

4. 字符串类型

任何UTF-8字符串都可以使用字符串类型来表示

5. 正则表达式

查询时可以使用正则表达式，语法与JavaScript的正则表达式语法相同

6. 数组类型

集合或者列表可以表示为数组

7. 内嵌文档

文档可以嵌套其他文档，此时被嵌套的文档就成了父文档的值

8. Object ID

Object ID 是一个 12 字节的 ID，是文档的唯一标识

9. 二进制数据

二进制数据是任意字节的字符串，不能通过 shell 操作。如果要将非 UTF-8 字符串存入数据库，那么使用二进制数据是唯一的方法

10. 代码

MongoDB还可以在查询和文档中存储任意的JavaScript代码

11. 日期类型

MongoDB中有两种日期类型：ISODate和Date。

ISODate类型



###### MongoDB获取日期的方式

* ==new Date()==
* ==new ISODate()==
* `Date()`

###### new Date()有什么

ISODate类型是MongoDB中存储日期时间的标准格式，也是MongoDB官方推荐的日期类型。它的格式为ISO 8601，精确到毫秒，例如：“2019-08-21T17:37:46.123Z”。其中，T表示时间的开始，Z表示时区为UTC（协调世界时

在MongoDB命令行中，可以使用new Date()函数来创建一个ISODate类型的日期

ISODate类型支持各种日期操作，例如比较、范围查询等。例如，可以使用g t e 、 gte、gte、lte等操作符来对日期进行范围查询



Date类型是JavaScript中的日期类型，在MongoDB中也可以使用Date类型来存储日期。Date类型存储的数据精度为毫秒













MongoDB会将日期存储为64位整数，表示自Unix纪元(1970年1月1日)以来的毫秒数,==不包含时区信息==



---

## 二 使用语法

```javascript
var tags=["nosql","mongodb","document","developer","popular"];
var types= ["technology","sociality","travel","novel","literature"];
var books=[];
for(var i=0;i<50;i++){
    var typeIdx=Math.floor(Math.random()*types.length);
    var tagIdx=Math.floor(Math.random()*tags.length);
    var favCount=Math.floor(Math.random()*100);
    var book={
        title:"book"+i,
        type:types[typeIdx],
        tag:tags[tagIdx],
        favCount:favCount,
        author:"xxx"+i
    };
 books.push(book)
 }
 db.books.insertMany(books);
```

### 1 数据库

```javascript
// 用户认证
use admin
db.auth("root","root")

// 显示当前数据库
db

// 切换数据库
use admin

// 显示当前所有数据库
show dbs

// 显示当前数据库的用户
show users

// 查看数据库操作mingl
db.help()

// 查看数据库状态
db.stats()
db.serverStatus()

// 查看数据库版本
db.version()

// 删除数据库
db.dropDatabase()

// 删除用户
db.removeUser("root")
```

### 2 集合

```javascript
// 显示所有集合
show collections

// 查看集合操作命令
db.role.help()
```

### 3 新增

###### 新增语法

```javascript
db.collection.insert(
   <document or array of documents>,
   {
      writeConcern: { w: "majority", wtimeout: 100 },
      ordered: <boolean>
   }
)
```

* document:要插入到集合中的文档或文档数组
* writeConcern:决定一个写操作落到多少个节点上才算成功
  * `0:发起写操作,不关心是否成功`
  * **1~集群最大数据节点数:写操作需要被复制到指定节点数才算成功**
  * ==majority:写操作需要被复制到大多数节点上才算成功==
* ordered:指定是否按顺序写入，默认 true，按顺序写入

> 1. 如果该集合当前不存在，则插入操作将创建该集合
> 2. MongoDB中的所有写入操作都是单个级别的原子操作
> 3. 不指定_id字段的新文档插入集合，会自动创建ObjectId类型的`_id`

###### insert和save的区别

1. 主键重复:==insert若插入的数据主键已经存在，则会抛DuplicateKeyException异常，提示主键重复,不保存当前数据==;save:如果_id主键存在则更新数据，如果不存在就插入数据

2. 批量插入:insert在客户端和SpringBoot都支持批量插入;save只能在客户端批量插入,SpringBoot不支持批量插入

###### 顺序写入和随机写入的区别

###### bulkWrite使用

### 4 查询

###### 查询语法

```javascript
db.collection.find(query, projection)
```

* query:可选，使用查询操作符指定查询条件
* projection:可选，使用投影操作符指定返回的键。查询时返回文档中所有键值,只需省略该参数即可(默认省略),投影时,**_id**为1的时其他字段必须是1；_id是0的时候，其他字段可以是0；如果没有_id字段约束，多个其他字段必须同为0或同为1

###### $gt $lt $ne

| MySQL  | MongoDB        | SpringBoot |
| ------ | -------------- | ---------- |
| a=1    | {a: 1}         |            |
| a!=1   | {a: {$ne: 1}}  |            |
| a > 1  | {a: {$gt: 1}}  |            |
| a >= 1 | {a: {$gte: 1}} |            |
| a < 1  | {a: {$lt: 1}}  |            |
| a <= 1 | {a: {$lte: 1}} |            |

###### $and $or

| MySQL           | MongoDB                                | SpringBoot |
| --------------- | -------------------------------------- | ---------- |
| a = 1 AND b = 1 | {a: 1, b: 1}或{$and: [{a: 1}, {b: 1}]} |            |
| a = 1 OR b = 1  | {$or: [{a: 1}, {b: 1}]}                |            |

###### $in $nin

| MySQL          | MongoDB               |
| -------------- | --------------------- |
| a IS NULL      | {a: {$exists: false}} |
| a IN (1, 2, 3) | {a: {$in: [1, 2, 3]}} |

###### 排序

```javascript
db.books.find({type:"travel"}).sort({favCount:‐1})
```

* 1为升序排列，而-1是用于降序排列

###### 分页

```javascript
db.books.find().skip(8).limit(4)
```

* skip用于指定跳过记录数，limit则用于限定返回结果数量

> 1. 数据量大的时候，应该避免使用skip/limit形式的分页,采用"==查询条件+唯一排序条件=="
>    1. 第一页：db.posts.find({}).sort({_id: 1}).limit(20)
>    2. db.posts.find({_id: {$gt: <第一页最后一个_id>}}).sort({_id: 1}).limit(20)
>    3. db.posts.find({_id: {$gt: <第二页最后一个_id>}}).sort({_id: 1}).limit(20)
> 2. 处理分页问题,==避免使用count==
>
> ```javascript
> db.coll.find({x: 100}).limit(50);
> db.coll.count({x: 100});
> ```
>
> * 前者只需要遍历前 n 条，直到找到50条x=100的文档即可结束；
> * 后者需要遍历完 1000w 条找到所有符合要求的文档才能得到结果。为了计算总页数而进行的 count() 往往是拖慢页面整体加载速度的原因

###### 正则表达式匹配

```javascript
//使用正则表达式查找type包含 so 字符串的book
db.books.find({type:{$regex:"so"}})
db.books.find({type:/so/})
```

### 5 更新

###### 更新语法

```javascript
db.collection.update(query,update,options)
```

* query:更新的查询条件
* update:更新的动作及新的内容
* options:更新的选项
  * upsert:可选，`如果不存在update的记录，是否插入新的记录`。默认false不插入
  * multi:可选，`是否按条件查询出的多条记录全部更新`。 默认false,只更新找到的第一条记录
  * writeConcern:可选，**决定一个写操作落到多少个节点上才算成功**

> 1. 默认情况下，update命令只在更新第一个文档之后返回，如果需要更新多个文档，则可以使用multi选项
> 2. upsert是一种特殊的更新，其表现为如果目标文档不存在，则执行插入命令
> 3. update命令中的更新描述（update）通常由操作符描述，如果更新描述中不包含任何操作符，那么MongoDB会实现文档的replace语义

###### 更新操作符

| 操作符    | 格式                                        | 描述                                                |
| --------- | ------------------------------------------- | --------------------------------------------------- |
| $set      | {$set:{field:value}}                        | 指定一个键并更新值，若键不存在则创建                |
| $unset    | {$unset : {field : 1 }}                     | 删除一个键                                          |
| $inc      | {$inc : {field : value } }                  | 对数值类型进行增减                                  |
| $rename   | {$rename:{old_field_name :new_field_name }} | 修改字段名称                                        |
| $push     | { $push : {field : value } }                | 将数值追加到数组中，若数组不存在则会进行<br/>初始化 |
| $pushAll  | {$pushAll : {field : value_array }}         | 追加多个值到一个数组字段内                          |
| $pull     | {$pull : {field : _value } }                | 从数组中删除指定的元素                              |
| $addToSet | {$addToSet : {field : value } }             | 添加元素到数组中，具有排重功能                      |
| $pop      | {$pop : {field : 1 }}                       | 删除数组的第一个或最后一个元素                      |









































---

## SpringBoot集成

### 依赖配置

* 依赖

```shell
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
</dependencies>
```

* mongo去除自带_class字段

#### 单数据源

```yaml
spring:
  application:
    name: mongodb-study-service
  data:
    mongodb:
      uri: mongodb://root:root@192.168.2.40:27017/mongodb_study?authSource=admin
```

#### 多数据源

```yaml
```



#### 复制集

```yaml
```



#### 分片集群

```yaml
```

### 常见对象

#### MongoClient

#### MongoTemplate

#### MongoRepository

### 增删改查

#### 新增

#### 修改

#### 删除

#### 查询

## 三 索引

## 四 事务





# 高级篇

## 

## 副本集

## 分片集

## 调优







