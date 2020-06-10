---
title: MongoDB 基础教程
comments: true
date: 2019-10-17 10:18:35
tags:
- js
- node
- mongodb
categories: WEB前端
author: jun.zhou
---

# MongoDB基础教程

![MongoDB](https://img.fengjr.com/image/2019/10/17/76a8980ba092a2e602c20dc4498b0fdb.png)

## 简介

[MongoDB](https://baike.baidu.com/item/MongoDB)是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的，它支持的数据结构非常松散，是类似json的bson（二进制JSON）格式。

特点是高性能、易部署、易使用，存储数据非常方便，适用于敏捷开发。

## 安装和连接

### Mac OSX 平台安装 MongoDB

#### 官网

官网下载地址 https://www.mongodb.com/download-center#community

按照教程直接下载即可，命令行下载的方式如下。

#### 使用 curl 命令安装


```bash
# 进入 /usr/local
cd /usr/local

# 下载
sudo curl -O https://fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.11.tgz

# 解压
sudo tar -zxvf mongodb-osx-ssl-x86_64-4.0.9.tgz

# 重命名为 mongodb 目录

sudo mv mongodb-osx-x86_64-4.0.9/ mongodb
```

安装完成后，我们可以把 MongoDB 的二进制命令文件目录（安装目录/bin）添加到 PATH 路径中：

```bash
export PATH=/usr/local/mongodb/bin:$PATH
```

#### 使用 brew 安装

此外，还可以使用macos的homebrew安装mongodb


```bash
sudo brew install mongodb
# 如果要安装支持 TLS/SSL 命令如下：
sudo brew install mongodb --with-openssl
# 安装最新开发版本
sudo brew install mongodb --devel
```

### 运行MongoDB

1、首先我们创建一个数据库存储目录

```bash
sudo mkdir -p /data/db
```

2、启动 mongodb，默认数据库目录即为 /data/db

```bash
sudo mongod

# 如果没有创建全局路径 PATH，需要进入以下目录
cd /usr/local/mongodb/bin
sudo ./mongod
```

3、再打开一个终端进入执行以下命令：


```bash
$ cd /usr/local/mongodb/bin 
$ ./mongo
MongoDB shell version v4.0.11
connecting to: mongodb://127.0.0.1:27017/?gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("27e2b200-fa7c-4463-8c5d-506b97004e97") }
MongoDB server version: 4.0.11
……
> 1 + 1
2
> 
```

如果你的数据库目录不是 /data/db，可以通过 --dbpath指定

```bash
sudo mongod --dbpath=/data/db 
```

4、还可以通过工具 [Robo 3T](https://robomongo.org/) 来创建连接

如下图所示，自定义应用名称，mongodb默认端口为27017

![](https://img.fengjr.com/image/2019/10/16/58af404fd31241f2a6fb8d8195590f24.png)

5、node mongoose 连接


```bash
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost:27017/test');

const Cat = mongoose.model('Cat', { name: String });

const kitty = new Cat({ name: 'Zildjian' });
kitty.save().then(() => console.log('meow'));
```



## 基础概念解析


SQL术语/概念 | MongoDB术语/概 | 解释/说明
---|---|---
database | database | 数据库
row | document | 数据记录行/文档
column | field | 数据字段/域
index | index | 索引
table joins |  | 表连接,MongoDB不支持
primary key | primary key | 主键,MongoDB自动将_id字段设置为主键

### 数据库

一个mongodb可以建立多个数据库

MongoDB的默认数据库为"db"，该数据库存储在data目录中。

MongoDB的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中

"show dbs" 命令可以显示所有数据的列表。

```shell
> show dbs
<database>  0.000GB
admin       0.000GB
config      0.000GB
fund-cms    0.000GB
local       0.000GB
>
```

执行 "db" 命令可以显示当前数据库对象或集合。

```bash
$ ./mongo
MongoDB shell version: 3.0.6
connecting to: test
> db
test
> 
```

运行"use"命令，可以连接到一个指定的数据库。

```
> use local
switched to db local
> db
local
> 
```

### 文档(Document)

文档是一组键值(key-value)对(即 BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

一个简单的文档例子如下：

```JSON
{
    "site":"www.fengjr.com", 
    "name":"凤凰金融"
}
```
需要注意的是：
1. 文档中的键/值对是有序的。
1. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
1. MongoDB区分类型和大小写。
1. MongoDB的文档不能有重复的键。
1. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符

文档键命名规范：

- 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
- .和$有特别的意义，只有在特定环境下才能使用。
- 以下划线"_"开头的键是保留的(不是严格要求的)。

### 集合

集合就是 MongoDB 文档组，集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

```
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
{"site":"www.runoob.com","name":"菜鸟教程","num":5}
```

当第一个文档插入时，集合就会被创建

#### 合法的集合名

- 集合名不能是空字符串""。
- 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
- 集合名不能以"system."开头，这是为系统集合保留的前缀。
- 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。　


#### MongoDB 数据类型

数据类型 | 描述
---|---
String | 字符串
Integer | 整数数值
Double | 双精度浮点值
Array | 数组
Timestamp | 时间戳
Object | 用户内嵌文档
Null | 空
Symbol | 符号
Date | 日期时间
Object ID | 对象ID
Binary Data | 二进制数据
Code | 代码类型
Regular expression | 正则表达式



## 基本命令

### 创建数据库
#### 语法

```
use DATABASE_NAME
```

如果数据库不存在，则创建数据库，否则切换到指定数据库

### 删除数据库

#### 语法
```
db.dropDatabase()
```
### 创建集合

##### 语法

```
db.createCollection(name, options)
```

- name:要创建的集合名称
- options：可选参数，指定内存大小和索引


|字段 | 类型 | 描述|
|---|---|---|
|capped | boolean | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为 true 时，必须指定 size 参数。|
|autoIndexId | boolean |（可选）如为 true，自动在 _id 字段创建索引。默认为 false。|
|size | number | 为固定集合指定一个最大值 |
|max | number | 指定固定集合中包含文档的最大数量 |

##### 实例

创建test1实例，可以使用 show collections 或 show tables 命令

```bash
> use test1
switched to db test1
> db.createCollection("abc")
{ "ok" : 1 }
> show collections
abc
> show tables
abc
>
```

在 MongoDB 中，你不需要创建集合。当你插入一些文档时，MongoDB 会自动创建集合


```bash
> db.abc3.insert({name:"yupan"})
WriteResult({ "nInserted" : 1 })
> show collections
abc
abc1
abc2
abc3
>
```

### 删除集合

#### 语法

```
db.collection.drop()
```

#### 返回值

如果成功删除选定集合，则 drop() 方法返回 true，否则返回 false。

#### 实例

```bash
> db.abc1.drop()
true
> db.abc2.drop()
true
> db.abc4.drop()
false
> show collections
abc
abc3
```

### 插入文档

#### 语法

MongoDB 使用 insert() 或 save() 方法向集合中插入文档，语法如下

```
db.COLLECTION_NAME.insert(document)
```
##### 实例

```
db.col1.insert({"_id":1, "name":"111"})
db.save.insert({"_id":1, "name":"222"})
```

查看已插入的文档用find()

```bash
> db.col1.find()
{ "_id" : 1, "name" : "222" }
{ "_id" : ObjectId("5da7313b00818179fd1c9b98"), "name" : "jin" }
>
```
#### save和update的区别

当主健"_id"不存在时，都是添加一个新的文档，但是当主键"_id"存在时，

insert:当主键"_id"在集合中存在时，不做任何处理。

save:当主键"_id"在集合中存在时，进行更新。

```bash
> db.col1.insert({"_id":1, "name":"yupan"})
WriteResult({ "nInserted" : 1 })
> db.col1.find()
{ "_id" : 1, "name" : "yupan" }
> db.col1.insert({"_id":1, "name":"111"})
WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "E11000 duplicate key error collection: test1.col1 index: _id_ dup key: { : 1.0 }"
	}
})
> db.col1.save({"_id":1, "name":"111"})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

### 更新文档

MongoDB 使用 **update()** 和 **save()** 方法来更新集合中的文档

#### update() 方法

update() 方法用于更新已存在的文档

##### 语法

```bash
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

参数说明

- query : update的查询条件，类似sql update查询内where后面的。
- update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- writeConcern :可选，抛出异常的级别

#### save() 方法

save() 方法通过传入的文档来替换已有文档。

##### 语法

```
db.collection.save(
   <document>,
   {
     writeConcern: <document>
   }
)
```

参数说明：

- document : 文档数据。
- writeConcern :可选，抛出异常的级别

##### 实例

```
>db.col.save({
    "_id" : 1,
    "name":"111"
})
```

> save 其实就是update的变种，通过设置upsert等即可使update实现save的功能


### 删除文档

MongoDB remove()函数是用来移除集合中的数据

#### 语法

```bash
# 2.6之前
db.collection.remove(
   <query>,
   <justOne>
)
# 2.6之后
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```

##### 参数说明

- query :（可选）删除的文档的条件。
- justOne : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
- writeConcern :（可选）抛出异常的级别。

#### 实例


```bash
> db.col1.find()
{ "_id" : 1, "name" : "nnn" }
{ "_id" : ObjectId("5da7313b00818179fd1c9b98"), "name" : "jin" }
{ "_id" : 2, "name" : "nnn" }
> db.col1.remove({"_id":1})
WriteResult({ "nRemoved" : 1 }) # 删除了一条数据
> db.col1.find()
{ "_id" : ObjectId("5da7313b00818179fd1c9b98"), "name" : "jin" }
{ "_id" : 2, "name" : "nnn" }
```

如果你想删除所有数据

```
> db.col1.remove({})
WriteResult({ "nRemoved" : 2 })
> db.col1.find()
>
```

### 查询文档

MongoDB 查询文档使用 find() 方法。

find() 方法以非结构化的方式来显示所有文档

pretty() 方法以格式化的方式来显示所有文档。

#### 语法

```bash
db.collection.find(query, projection)
```

- query ：可选，使用查询操作符指定查询条件
- projection ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）

易读pretty() 方法

```
> db.col1.find()
{ "_id" : ObjectId("56063f17ade2f21f36b03133"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
> db.col1.find().pretty()
{
	"_id" : ObjectId("56063f17ade2f21f36b03133"),
	"title" : "MongoDB 教程",
	"description" : "MongoDB 是一个 Nosql 数据库",
	"by" : "菜鸟教程",
	"url" : "http://www.runoob.com",
	"tags" : [
		"mongodb",
		"database",
		"NoSQL"
	],
	"likes" : 100
}

```

另外还有findOne()方法，只返回一个文档

### 条件操作符

#### AND 条件

MongoDB 的 find() 方法可以传入多个键(key)，每个键(key)以逗号隔开，即常规 SQL 的 AND 条件

##### 语法

```bash
>db.col.find({key1:value1, key2:value2}).pretty()
```

#### OR 条件

MongoDB OR 条件语句使用了关键字 $or

##### 语法

```bash
>db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```

##### 实例

以下实例中，我们演示了查询键 name 值为yupan 或键 age 值为 20 的文档。

```bash
> db.col1.find()
{ "_id" : 1, "name" : "yupan" }
{ "_id" : 2, "name" : "jin", "age" : 20 }
> db.col1.find({$or:[{"name":"yupan"}, {"age":20}]})
{ "_id" : 1, "name" : "yupan" }
{ "_id" : 2, "name" : "jin", "age" : 20 }
>
```

### AND 和 OR 联合使用

#### 实例

```bash
> db.col1.find()
{ "_id" : 1, "name" : "yupan" }
{ "_id" : 2, "name" : "jin", "age" : 20 }
{ "_id" : 3, "name" : "messi", "age" : 30 }
> db.col1.find({"age":{$gt:10}, $or:[{"name":"yupan"}, {"age":20}]})
{ "_id" : 2, "name" : "jin", "age" : 20 }
```

### (>) 大于操作符 - $gt

#### 语法

```bash
db.col1.find({age : {$gt : 10}})
```

### （>=）大于等于操作符 - $gte

#### 语法

```bash
db.col1.find({age : {$gt : 10}})
```

### （<）小于操作符 - $lt

#### 语法

```bash
db.col1.find({age : {$gt : 10}})
```

### （<=）小于等于操作符 - $lte

#### 语法

```bash
db.col1.find({age : {$gt : 10}})
```

### limit（）和skip方法

#### limit（）

读取指定数量的数据记录。

##### 语法

```
>db.COLLECTION_NAME.find().limit(NUMBER)
```

#### skip（）

跳过指定数量的数据记录。默认为0

##### 语法

```bash
>db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```

### sort() 

在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

#### 语法

```bash
>db.COLLECTION_NAME.find().sort({KEY:1})
```

#### 实例

```bash
> db.col1.find()
{ "_id" : 1, "name" : "yupan" }
{ "_id" : 2, "name" : "jin", "age" : 20 }
{ "_id" : 3, "name" : "messi", "age" : 30 }
> db.col1.find().sort({age: -1})
{ "_id" : 3, "name" : "messi", "age" : 30 }
{ "_id" : 2, "name" : "jin", "age" : 20 }
{ "_id" : 1, "name" : "yupan" }
>
```



