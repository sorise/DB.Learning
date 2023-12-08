## [LevelDB](https://github.com/google/leveldb)

**介绍**: Leveldb 是一个 google 实现的非常高效的 kv 数据库，能够支持 billion 级别的数据量了, 原理是依据LSM-Tree（Log Structed-Merge Tree）, LevelDB 可看作 Bigtable（ Google 三大核心技术之一（另外两个分别为 GFS 与 MapReduce）） 的简化版或单机版，适合**写多读少**下的业务场景。**非关系型数据模型**（NoSQL），不支持**SQL语句**，也不支持索引。



### 简介

Google 开源的 **NOSQL 存储引擎库**，是现代分布式存储领域的一枚原子弹。在它的基础之上，Facebook 开发出了另一个 NOSQL 存储引擎库 RocksDB，沿用了 LevelDB 的先进技术架构的同时还解决了 LevelDB 的一些短板。你可以将 RocksDB 比喻成氢弹，它比 LevelDB 的威力更大一些。现代开源市场上有很多数据库都在使用 RocksDB 作为底层存储引擎，比如大名鼎鼎的 TiDB。

* LevelDB是一个持久化存储的KV系统，和Redis这种内存型的KV系统不同，LevelDb不会像Redis一样狂吃内存，而是将大部分数据存储到磁盘上。
* LevleDB在存储数据时，是根据记录的key值有序存储的，即相邻的key值在存储文件中是依次顺序存储的。用户可以自定义key大小比较函数Comparator，来实现依序存储的过程。
* 操作接口很简单，基本操作包括写记录，读记录以及删除记录。也支持针对多条操作的原子批量操作。
* 支持数据快照（snapshot）功能，使得读取操作不受写操作影响，可以在读操作过程中始终看到一致的数据。

<img src="./assets/image-20230911153439455.png" alt="image-20230911153439455" width="700px" />

### 内容

- [levelDB 体系结构](./contents/structure.md)
- [levelDB 使用介绍](./contents/introduction.md)



#### 参考：

* wiscKey论文精读 https://hardcore.feishu.cn/docs/doccnxbpzJQ5KJulm5YtGPULRxc#eQXr5r
* Leveldb源码解读(一) [https://hardcore.feishu.cn/docs/doccnqc6VrpZk2JXSTcNr3Pq7Eb#](#)



















