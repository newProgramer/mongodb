# 概念解析

| SQL术语 | MongoDB术语 | 解释 |
| :--- | :--- | :--- |
| database | database | 数据库 |
| table | collection | 数据库表/集合 |
| row | document | 记录行/文档 |
| column | field | 字段/域 |
| index | index | 索引 |
| table joins | 嵌入文档 | 表连接，MongoDB不支持表连接 |
| primary key | primary key | 主键，MongoDB自动将\_id作为主键 |

# 创建数据库

```
use DatabaseName #创建数据库
```

# 删除数据库

```
use DatabaseName #切换到DatabaseName 数据库
db.dropDatabase() #删除当前数据库
```

# 创建集合

```
db.createCollection(name, options) #name集合名称，options可选参数
```

> | 字段 | 类型 | 描述 |
> | :--- | :--- | :--- |
> | capped | 布尔 | 是否创建固定集合。固定集合指大小固定的集合，如果达到最大值则覆盖最早记录。当该值为true时，必须指定size参数 |
> | autoIndexId | 布尔 | 是否自动在\_id字段创建索引 |
> | size | 数值 | 为集合指定一个大小 |
> | max | 数值 | 指定固定集合中包含文档的最大数量 |

# 删除集合

```
db.collection.drop()
```

# 插入文档

```
db.COLLECTION_NAME.insert(document) #document是一个BSON格式数据，BSON类似于JSON
db.col.insert(document) #不指定_id字段就是插入，如果指定_id字段则更新该条数据
```

### 3.2版本推荐方式

```
db.collection.insertOne()
db.collection.insertMany()
```

# 更新文档

### update\(\)

```
db.collection.update(
   <query>, #更新的查询条件
   <update>, #更新的对象，类似sql的update set后面的
   {
     upsert: <boolean>, #可选，如果update记录不存在是否插入objNew,默认是false
     multi: <boolean>, #可选，默认为false，只更新找到的第一条记录，如果为true，则更新所有找到的记录
     writeConcern: <document> #可选，抛出异常的级别
   }
)
```

### save\(\)

```
db.collection.save(
   <document>, #文档数据
   {
     writeConcern: <document> #可选，抛出的异常级别
   }
)
```

### 3.2版本推荐方式

```
db.collection.updateOne()
db.collection.updateMany()
```

# 删除文档

```
#MongoDB 2.6版本以前
db.collection.remove(
   <query>,
   <justOne>
)
#MongoDB 2.6版本以后
db.collection.remove(
   <query>, #可选，删除文档的查询条件
   {
     justOne: <boolean>, #可选，默认为false，删除查到的全部记录，如果为true则删除查到的第一条记录
     writeConcern: <document> #可选，抛出异常的级别
   }
)

db.col.remove({})  #删除所有数据，类似SQL的truncate

#3.2版本推荐方式
db.inventory.deleteMany({}) #删除所有
db.inventory.deleteOne({}) #删除一条
```

# 查询文档

```
db.collection.find(query, projection)
#query：可选，查询条件
#projection:可选，查询的key，要查询所有就不填写

db.collection.find().pretty() #格式化查询出来的文档
```

### 查询比较参数

| 等于 | { key : value } |
| :--- | :--- |
| 小于 | { key : { $lt : value } } |
| 小于等于 | { key : { $lte : value}} |
| 大于 | { key : { $gt : value } } |
| 大于等于 | { key : { $gte : value } } |
| 不等于 | { key : { $ne : value } } |
| 大于等于 |  |
| 不等于 |  |

### AND 

MongoDB 的 find\(\) 方法可以传入多个键\(key\)，每个键\(key\)以逗号隔开，即常规 SQL 的 AND 条件。

### OR

```
db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```

### $type

$type操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果。

MongoDB 中可以使用的类型如下表所示：

| **类型** | **数字** | **备注** |
| :--- | :--- | :--- |
| Double | 1 |  |
| String | 2 |  |
| Object | 3 |  |
| Array | 4 |  |
| Binary data | 5 |  |
| Undefined | 6 | 已废弃。 |
| Object id | 7 |  |
| Boolean | 8 |  |
| Date | 9 |  |
| Null | 10 |  |
| Regular Expression | 11 |  |
| JavaScript | 13 |  |
| Symbol | 14 |  |
| JavaScript \(with scope\) | 15 |  |
| 32-bit integer | 16 |  |
| Timestamp | 17 |  |
| 64-bit integer | 18 |  |
| Min key | 255 | Query with -1. |
| Max key | 127 |  |

```
db.col.find({"key" : {$type : 2}})
或
db.col.find({"key" : {$type : 'string'}})
```

### 分页 limit\(\)

```
db.COLLECTION_NAME.find().limit(NUMBER)
```

### skip\(\)

跳过指定数量的数据

```
db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```

### 排序 sort\(\)

```
db.COLLECTION_NAME.find().sort({KEY:1}) #KEY排序的字段，1 升序排列，-1 降序排列
```

# 索引





