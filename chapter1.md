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

```
db.collection.createIndex(keys, options)
```

createIndex\(\) 接收可选参数，可选参数列表如下：

| Parameter | Type | Description |
| :--- | :--- | :--- |
| background | Boolean | 建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为**false**。 |
| unique | Boolean | 建立的索引是否唯一。指定为true创建唯一索引。默认值为**false**. |
| name | string | 索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。 |
| dropDups | Boolean | 在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 **false**. |
| sparse | Boolean | 对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 **false**. |
| expireAfterSeconds | integer | 指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。 |
| v | index version | 索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。 |
| weights | document | 索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。 |
| default\_language | string | 对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语 |
| language\_override | string | 对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language. |

# 聚合

```
db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)
```

| 表达式 | 描述 | 实例 |
| :--- | :--- | :--- |
| $sum | 计算总和。 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", num\_tutorial : {$sum : "$likes"}}}\]\) |
| $avg | 计算平均值 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", num\_tutorial : {$avg : "$likes"}}}\]\) |
| $min | 获取集合中所有文档对应值得最小值。 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", num\_tutorial : {$min : "$likes"}}}\]\) |
| $max | 获取集合中所有文档对应值得最大值。 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", num\_tutorial : {$max : "$likes"}}}\]\) |
| $push | 在结果文档中插入值到一个数组中。 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", url : {$push: "$url"}}}\]\) |
| $addToSet | 在结果文档中插入值到一个数组中，但不创建副本。 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", url : {$addToSet : "$url"}}}\]\) |
| $first | 根据资源文档的排序获取第一个文档数据。 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", first\_url : {$first : "$url"}}}\]\) |
| $last | 根据资源文档的排序获取最后一个文档数据 | db.mycol.aggregate\(\[{$group : {\_id : "$by\_user", last\_url : {$last : "$url"}}}\]\) |



