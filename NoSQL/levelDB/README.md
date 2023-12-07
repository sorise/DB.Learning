## [LevelDB](https://github.com/google/leveldb)

**介绍**: Leveldb 是一个 google 实现的非常高效的 kv 数据库，能够支持 billion 级别的数据量了, 原理是依据LSM-Tree（Log Structed-Merge Tree）, LevelDB 可看作 Bigtable（ Google 三大核心技术之一（另外两个分别为 GFS 与 MapReduce）） 的简化版或单机版，适合**写多读少**下的业务场景。

Google 开源的 **NOSQL 存储引擎库**，是现代分布式存储领域的一枚原子弹。在它的基础之上，Facebook 开发出了另一个 NOSQL 存储引擎库 RocksDB，沿用了 LevelDB 的先进技术架构的同时还解决了 LevelDB 的一些短板。你可以将 RocksDB 比喻成氢弹，它比 LevelDB 的威力更大一些。现代开源市场上有很多数据库都在使用 RocksDB 作为底层存储引擎，比如大名鼎鼎的 TiDB。

> [非关系型数据模型](https://www.zhihu.com/search?q=非关系型数据模型&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"80684560"})（NoSQL），不支持[sql语句](https://www.zhihu.com/search?q=sql语句&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"80684560"})，也不支持索引。

<img src="./assets/image-20230911153439455.png" alt="image-20230911153439455" width="700px" />

**介绍**：

- [levelDB 体系结构](#)
- [levelDB 使用介绍](./contents/introduction.md)





#### 参考：

* wiscKey论文精读 https://hardcore.feishu.cn/docs/doccnxbpzJQ5KJulm5YtGPULRxc#eQXr5r
* Leveldb源码解读(一) [https://hardcore.feishu.cn/docs/doccnqc6VrpZk2JXSTcNr3Pq7Eb#](#)



















