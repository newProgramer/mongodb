# MongoDB复制（副本集）

MongoDB复制是指将数据同步在多个服务器的过程。

* 保障数据的安全性
* 数据高可用性
* 灾难恢复
* 无需停机恢复
* 分布式读取数据

![](http://www.runoob.com/wp-content/uploads/2013/12/replication.png)

 以上结构图中，客户端从主节点读取数据，在客户端写入数据到主节点时， 主节点与从节点进行数据交互保障数据的一致性。

##### 特征

*  N 个节点的集群
* 任何节点可作为主节点
* 所有写入操作都在主节点上
* 自动故障转移
* 自动恢复

# MongoDB分片

在Mongodb里面存在另一种集群，就是分片技术,可以满足MongoDB数据量大量增长的需求。

当MongoDB存储海量的数据时，一台机器可能不足以存储数据，也可能不足以提供可接受的读写吞吐量。这时，我们就可以通过在多台机器上分割数据，使得数据库系统能存储和处理更多的数据。

* 复制所有的写入操作到主节点
* 延迟的敏感数据会在主节点查询
* 单个副本集限制在12个节点
* 当请求量巨大时会出现内存不足。
* 本地磁盘不足
* 垂直扩展价格昂贵

![](https://www.runoob.com/wp-content/uploads/2013/12/sharding.png)

上图中主要有如下所述三个主要组件：

* **Shard:**
  用于存储实际的数据块，实际生产环境中一个shard server角色可由几台机器组个一个replica set承担，防止主机单点故障

* **Config Server:**
  mongod实例，存储了整个 ClusterMetadata，其中包括 chunk信息。

* **Query Routers:**
  前端路由，客户端由此接入，且让整个集群看上去像单一数据库，前端应用可以透明使用。



