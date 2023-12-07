## [LevelDB](https://github.com/google/leveldb)

**介绍**: Leveldb 是一个 google 实现的非常高效的 kv 数据库，能够支持 billion 级别的数据量了, 原理是依据LSM-Tree（Log Structed-Merge Tree）, LevelDB 可看作 Bigtable（ Google 三大核心技术之一（另外两个分别为 GFS 与 MapReduce）） 的简化版或单机版。

![Leveldb 基本介绍和使用指南](./assets/v2-63065039fa93e1bba9169f160e7f6a0f_1440w.png)

### 1. 基本概念

LevelDB 只是一个 [C/C++](https://baike.baidu.com/item/C%2FC%2B%2B/6824246?fromModule=lemma_inlink) [编程语言](https://baike.baidu.com/item/编程语言/9845131?fromModule=lemma_inlink)的库, 不包含[网络服务](https://baike.baidu.com/item/网络服务/9498645?fromModule=lemma_inlink)封装, 所以无法像一般意义的[存储服务器](https://baike.baidu.com/item/存储服务器/1625801?fromModule=lemma_inlink)(如 [MySQL](https://baike.baidu.com/item/MySQL/471251?fromModule=lemma_inlink))那样, 用客户端来[连接它](https://baike.baidu.com/item/连接它/16047635?fromModule=lemma_inlink). LevelDB 自己也声明, 使用者应该封装自己的[网络服务器](https://baike.baidu.com/item/网络服务器/99096?fromModule=lemma_inlink).

leveldb 是一个持久化的 key/value 存储，key 和 value 都是任意的字节数组(byte arrays)，并且在存储时，key 值根据用户指定的 comparator 函数进行排序。

#### 1.1 LevelDB的特性

LevelDB 是一个 C++ 语言编写的高效键-值嵌入式数据库，目前对亿级的数据也有着非常好的读写性能。虽然 LevelDB 有着许多键-值数据库所不具备的优秀特性，但是与 Redis 等一些主流键-值数据库相比也有缺陷。接下来就对 LevelDB 的优缺点进行具体阐述。

LevelDB 的优点体现在：

- key 与 value 采用字符串形式，且长度没有限制；
- 数据能持久化存储，同时也能将数据缓存到内存，实现快速读取；
- 基于 key 按序存放数据，并且 key 的排序比较函数可以根据用户需求进行定制；
- 支持简易的操作接口 API，提供基本的 `Put(key,value)`，`Get(key)`，`Delete(key)` 操作。，并支持批量写入,多个更改可以在一个原子批处理中生效；
- 可以针对数据创建数据内存快照,用户可以创建一个瞬时快照(snapshot)，以获得数据的一致性视图；
- 支持前向、后向的迭代器；
- 采用 Google 的 Snappy 压缩算法对数据进行压缩，以减少存储空间；
- 基本不依赖其他第三方模块，可非常容易地移植到 Windows、Linux、UNIX、Android、iOS。

LevelDB 的**缺点**体现在：

- 不是传统的关系数据库，不支持 SQL 查询与索引；
- **只支持单进程，不支持多进程**；
- 不支持多种数据类型；
- 不支持客户端-服务器的访问模式。用户在应用时，需要自己进行网络服务的封装。



#### 1.2 头文件介绍

leveldb 对外暴露的接口都在 `include/*.h` 中，用户不应该依赖任何其它目录下的头文件，这些内部 API 可能会在没有警告的情况下被改变。

- `include/leveldb/db.h`：主要的 DB 接口，从这开始。
- `include/leveldb/options.h`： 控制数据库的行为，也控制单个读和写的行为。
- `include/leveldb/comparator.h`： 比较函数的抽象。如果你只想对 key 逐字节比较，可以直接使用默认的比较器。如果你想要自定义排序（例如处理不同的字符编码、解码等），可以实现自己的比较器。
- `include/leveldb/iterator.h`：迭代数据的接口，你可以从一个 DB 对象获取到一个迭代器。
- `include/leveldb/write_batch.h`：原子地将多个操作应用到数据库。
- `include/leveldb/slice.h`：类似 string，维护着指向字节数组的指针和对应的长度。
- `include/leveldb/status.h`：许多公共接口都会返回 `Status`，用于报告成功或各种错误。
- `include/leveldb/env.h`：操作系统环境的抽象，该接口的 posix 实现位于 `util/env_posix.cc` 中.
- `include/leveldb/table.h, include/leveldb/table_builder.h`：底层的模块，大多数用户可能不会直接用到。



#### 1.3 编译安装

请百度一下，非常简单！



### 2. 基本使用

#### 2.1 打开一个数据库
leveldb 数据库都有一个名字，该名字对应文件系统上的一个目录，该数据库内容全都存在该目录下。下面的例子显示了如何打开一个数据库，必要时创建数据库：

```cpp
#include <cassert>
#include "leveldb/db.h"

int main() {
    leveldb::DB* db;
    leveldb::Options options;
    options.create_if_missing = true;
    leveldb::Status status = leveldb::DB::Open(options, "/tmp/testdb", &db);
    assert(status.ok());
}
```

如果你想在数据库已存在的时候触发一个异常，将下面这行配置加到 `leveldb::DB::Open` 调用之前：

```cpp
options.error_if_exists = true;
```

#### 2.2 Status
你也许注意到上面的 `leveldb::Status` 返回类型，leveldb 中大部分方法在遇到错误的时候会返回该类型的值，你可以检查它是否 ok，然后打印相关的错误信息：

```cpp
leveldb::Status s = ...;
if (!s.ok()) cerr << s.ToString() << endl;
```

尝试输出数据库已存在的错误信息吧！



#### 2.3 关闭数据库
当数据库不再使用的时候，像下面这样直接删除数据库对象就可以了：

```cpp
delete db;
```

是不是很简单？后面我们具体源码分析时会看到，`DB` 类是基于 RAII 实现的，在 delete 时会触发析构函数自动清理。



#### 2.4 数据库读写
leveldb 提供了 `Put`、`Delete` 和 `Get` 方法来修改/查询数据库，下面的代码展示了将 key1 对应的 value 移动到 key2 下。

```cpp
std::string value;
leveldb::Status s = db->Get(leveldb::ReadOptions(), key1, &value);
if (s.ok()) s = db->Put(leveldb::WriteOptions(), key2, value);
if (s.ok()) s = db->Delete(leveldb::WriteOptions(), key1);
```

联合使用：

```cpp
#include "leveldb/db.h"
#include<iostream>

int main() {
	// 声明
	leveldb::DB* db;

	leveldb::Options option;
	option.create_if_missing = true;
	auto status = leveldb::DB::Open(option, "muse_db", &db);

	if (status.ok()) {
		std::cout << "cteate successfully!" << std::endl;
		std::string key = "51162xxxx";
		std::string value = "jiangxing";

		auto s = db->Put(leveldb::WriteOptions(), key, value);
		if (s.ok())
		{
			std::string tvalue = "";
			db->Get(leveldb::ReadOptions(), key, &tvalue);
			std::cout << key << ":" << tvalue << std::endl;
		}
	}
	else {
		std::cerr << status.ToString() << std::endl;
	}

	delete db;
}
```



#### 2.5 原子更新
如果进程在操作1后 到 操作2 之前挂了，那么同样的 value 将被存储在多个 key (key1、key2)下，因为删除操作2没有被执行。

```cpp
std::string value;
leveldb::Status s = db->Get(leveldb::ReadOptions(), key1, &value); 
if (s.ok()) s = db->Put(leveldb::WriteOptions(), key2, value); //操作 1
if (s.ok()) s = db->Delete(leveldb::WriteOptions(), key1);     //操作 2
```

可以通过使用 `WriteBatch` 原子地应用一组操作来避免类似的问题

```cpp
#include "leveldb/write_batch.h"

std::string value;
leveldb::Status s = db->Get(leveldb::ReadOptions(), key1, &value);
if (s.ok()) {
  leveldb::WriteBatch batch;
  batch.Delete(key1);
  batch.Put(key2, value);
  s = db->Write(leveldb::WriteOptions(), &batch);
}
```

**WriteBatch** 保存着一系列将被应用到数据库的操作，这些操作会按照添加的顺序依次被执行。注意，我们先执行 `Delete` 后执行 `Put`，这样如果 key1 和 key2 一样的情况下我们也不会错误地丢失数据。



#### 2.6 同步写操作
默认情况下，leveldb 每个写操作都是异步的：进程把要写的内容丢给操作系统后立即返回，从操作系统内存到底层持久化存储的传输是异步进行的。

可以为某个特定的写操作打开同步标识：**write_options.sync = true**，以等到数据真正被记录到持久化存储后再返回（在 Posix 系统上，这是通过在写操作返回前调用 **fsync(...)** 或 **fdatasync(...)** 或 **msync(..., MS_SYNC)** 来实现的）。

```cpp
leveldb::WriteOptions write_options;
write_options.sync = true;
db->Put(write_options, ...);
```

**异步写通常比同步写快 1000 倍。**异步写的缺点是，一旦机器崩溃可能会导致最后几个更新操作丢失。

异步写通常可以安全使用。比如你要将大量的数据写入数据库，如果丢失了最后几个更新操作，也可以重做整个写过程。**如果数据量非常大，一个优化点是采用混合方案，每进行 N 个异步写操作则进行一次同步写，如果期间发生了崩溃，重启从上一个成功的同步写操作开始即可**。（同步写操作可以同时更新一个标识，描述崩溃时重启的位置）

**WriteBatch** 可以作为异步写操作的替代品，多个更新操作可以放到同一个 `WriteBatch` 中然后通过一次同步写(即 `write_options.sync = true`)一起落盘。



#### 2.7 并发
**一个数据库同时只能被一个进程打开**。leveldb 会从操作系统获取一把锁来防止多进程同时打开同一个数据库。**在单个进程中，同一个 leveldb::DB 对象可以被多个并发线程安全地使用**，也就是说，不同的线程可以在不需要任何外部同步原语的情况下，写入、获取迭代器或者调用 `Get`（leveldb 实现会确保所需的同步）。

但是其它对象，比如 **Iterator** 或者 **WriteBatch** 需要外部自己提供同步保证，如果两个线程共享此类对象，需要使用自己的锁进行互斥访问。具体见对应的头文件。



#### 2.8 迭代数据库
下面的用例展示了如何打印数据库中全部的 (key, value) 对。

```cpp
leveldb::Iterator* it = db->NewIterator(leveldb::ReadOptions());
for (it->SeekToFirst(); it->Valid(); it->Next()) {
  cout << it->key().ToString() << ": "  << it->value().ToString() << endl;
}
assert(it->status().ok());  // Check for any errors found during the scan
delete it;
```

下面的用例展示了如何打印 **[start, limit)** 范围内的数据：

```cpp
for (it->Seek(start); it->Valid() && it->key().ToString() < limit; it->Next()) {
   //...
}
```

当然你也可以反向遍历（注意，反向遍历可能要比正向遍历慢一些，具体见前面的读性能基准测试）

```cpp
for (it->SeekToLast(); it->Valid(); it->Prev()) {
  ...
}
```



#### 2.9 快照
快照提供了针对整个 KV 存储的一致性只读视图（consistent read-only views）。

ReadOptions::snapshot 不为 null 表示读操作应该作用在 DB 的某个特定版本上；若为 null，则读操作将会作用在当前版本的一个隐式的快照上。

快照通过调用 `DB::GetSnapshot()` 方法创建：

```cpp
leveldb::ReadOptions options;
options.snapshot = db->GetSnapshot();
//... apply some updates to db ...
leveldb::Iterator* iter = db->NewIterator(options);
//... read using iter to view the state when the snapshot was created ...

delete iter;
db->ReleaseSnapshot(options.snapshot);
```

注意，当一个快照不再使用的时候，应该通过 `DB::ReleaseSnapshot` 接口进行释放。



#### 2.10 Slice
`it->key()` 和 `it->value()` 调用返回的值是 `leveldb::Slice` 类型。熟悉 Go 的同学应该对 Slice 不陌生。

Slice 是一个简单的数据结构，包含一个长度和一个指向外部字节数组的指针，返回一个 Slice 比返回一个 `std::string` 更高效，因为不需要隐式地拷贝大量的 keys 和 values。另外，leveldb 方法不返回 `\0` 截止符结尾的 C 风格字符串，因为 leveldb 的 keys 和 values 允许包含 `\0` 字节。

C++ 风格的 string 和 C 风格的空字符结尾的字符串很容易转换为一个 Slice：

```cpp
leveldb::Slice s1 = "hello";

std::string str("world");
leveldb::Slice s2 = str;
```

一个 Slice 也很容易转换回 C++ 风格的字符串：

```cpp
std::string str = s1.ToString();
assert(str == std::string("hello"));
```

在使用 Slice 时要小心，**要由调用者来确保 Slice 指向的外部字节数组有效**。例如，下面的代码就有 bug ：

```cpp
leveldb::Slice slice;
if (...) {
  std::string str = ...;
  slice = str;
}
Use(slice);
```

当 if 语句结束的时候，str 将会被销毁，Slice 的指向也随之消失，后面再用就会出问题。



#### 2.11 比较器（Comparator）
前面的例子中用的都是默认的比较函数，即逐字节按字典序比较。你可以自定义比较函数，然后在打开数据库的时候传入，只需要继承 `leveldb::Comparator` 然后定义相关逻辑即可，下面是一个例子：

```cpp
class TwoPartComparator : public leveldb::Comparator {
 public:
  // Three-way comparison function:
  //   if a < b: negative result
  //   if a > b: positive result
  //   else: zero result
  int Compare(const leveldb::Slice& a, const leveldb::Slice& b) const {
    int a1, a2, b1, b2;
    ParseKey(a, &a1, &a2);
    ParseKey(b, &b1, &b2);
    if (a1 < b1) return -1;
    if (a1 > b1) return +1;
    if (a2 < b2) return -1;
    if (a2 > b2) return +1;
    return 0;
  }

  // Ignore the following methods for now:
  const char* Name() const { return "TwoPartComparator"; }
  void FindShortestSeparator(std::string*, const leveldb::Slice&) const {}
  void FindShortSuccessor(std::string*) const {}
};
```

在打开数据库的时候，传入上面定义的比较器：

```cpp
// 实例化比较器
TwoPartComparator cmp;
leveldb::DB* db;
leveldb::Options options;
options.create_if_missing = true;
// 将比较器赋值给 options.comparator
options.comparator = &cmp;
// 打开数据库
leveldb::Status status = leveldb::DB::Open(options, "/tmp/testdb", &db);
...
```



#### 2.12 向后兼容性
比较器 `Name()` 方法返回的结果在创建数据库时会被绑定到数据库上，后续每次打开都会进行检查，如果名称改了，对 `leveldb::DB::Open` 的调用就会失败。因此，当且仅当在新的 key 格式和比较函数与已有的数据库不兼容而且已有数据不再被需要的时候再修改比较器名称。

总而言之，一个数据库只能对应一个比较器，而且比较器由名字唯一确定，**一旦修改名称或比较器逻辑，数据库的操作逻辑统统会出错**，毕竟 leveldb 是一个有序的 **KV 存储**。



如果非要修改比较逻辑呢？你可以根据预先规划一点一点的演进你的 key 格式，注意，事先的演进规划非常重要。比如，你可以在每个 key 的结尾存储一个版本号（大多数场景，一个字节足矣），当你想要切换到新的 key 格式的时候（比如上面的例子 `TwoPartComparator`处理的 keys 中），那么需要做的是：

1. 保持相同的比较器名称
2. 递增新 keys 的版本号
3. 修改比较器函数以让其使用版本号来决定如何进行排序



### 3. 性能调优
通过修改 `include/leveldb/options.h` 中定义的类型的默认值来对 leveldb 的性能进行调优。



#### 3.1  Block 大小

leveldb 把相邻的 keys 组织在同一个 block 中(具体见后续文章针对 sstable 文件格式的描述)，block 是数据在内存和持久化存储传输之间的基本单位。

默认的未压缩 block 大小大约为 4KB，经常批量扫描大量数据的应用可能希望把这个值调大，而针对数据只做“点读”的应用则可能希望这个值小一些。

但是，没有证据表明该值小于 1KB 或者大于几个 MB 的时候性能会表现得更好。另外要注意的是，**使用较大的 block size，压缩效率会更高效**。



#### 3.2 压缩

每个 block 在写入持久化存储之前都会被单独压缩。压缩默认是开启的，因为默认的压缩算法非常快，而且对于不可压缩的数据会自动关闭压缩功能，极少有场景会让用户想要完全关闭压缩功能，除非基准测试显示关闭压缩会显著改善性能。按照下面方式即可关闭压缩功能：

```cpp
leveldb::Options options;
options.compression = leveldb::kNoCompression;

//... leveldb::DB::Open(options, name, ...) ....
```



#### 3.3 缓存

数据库的内容存储在文件系统中的一组文件中，每个文件都存储了一系列压缩后的 blocks，如果 `options.block_cache` 是非 NULL，则用于缓存经常使用的已解压缩 block 内容。

```cpp
#include "leveldb/cache.h"

leveldb::Options options;
options.block_cache = leveldb::NewLRUCache(100 * 1048576);  // 100MB cache

leveldb::DB* db;
leveldb::DB::Open(options, name, &db);

//... use the db ...

delete db
delete options.block_cache;
```

注意 cache 保存的是未压缩的数据，因此应该根据应用程序所需的数据大小来设置它的大小。（已压缩数据的缓存工作交给操作系统的 buffer cache 或者用户自定义的 `Env` 实现去干。）

当执行一个大块数据读操作时，应用程序可能想要取消缓存功能，这样读进来的大块数据就不会导致当前 cache 中的大部分数据被置换出去，我们可以为它提供一个单独的 iterator 来达到该目的：

```cpp
leveldb::ReadOptions options;
options.fill_cache = false;
leveldb::Iterator* it = db->NewIterator(options);
for (it->SeekToFirst(); it->Valid(); it->Next()) {
   //...
}
```



#### 3.4 Key 的布局

注意，磁盘传输和缓存的单位都是一个 block，相邻的 keys（已排序）总在同一个 block 中，因此应用可以通过把需要一起访问的 keys 放在一起，同时把不经常使用的 keys 放到一个独立的键空间区域来提升性能。

举个例子，假设我们正基于 leveldb 实现一个简单的文件系统。我们打算存储到这个文件系统的数据类型如下：

```cpp
filename -> permission-bits, length, list of file_block_ids
file_block_id -> data
```

我们可以给上面表示 filename 的 key 增加一个字符前缀，例如 '/'，然后给表示 file_block_id 的 key 增加另一个不同的前缀，例如 '0'，这样这些不同用途的 key 就具有了各自独立的键空间区域，扫描元数据的时候我们就不用读取和缓存大块文件内容数据了。



#### 3.5 过滤器

鉴于 leveldb 数据在磁盘上的组织形式，一次 `Get()` 调用可能涉及多次磁盘读操作，可配置的 FilterPolicy 机制可以用来大幅减少磁盘读次数。

```cpp
leveldb::Options options;
// 设置启用基于布隆过滤器的过滤策略
options.filter_policy = NewBloomFilterPolicy(10);
leveldb::DB* db;
// 用该设置打开数据库
leveldb::DB::Open(options, "/tmp/testdb", &db);
... use the database ...
delete db;
delete options.filter_policy;
```

上述代码将一个基于布隆过滤器的过滤策略与数据库进行了关联，基于布隆过滤器的过滤方式依赖于如下事实，在内存中保存每个 key 的部分位（在上面例子中是 10 位，因为我们传给 `NewBloomFilterPolicy` 的参数是 10），这个过滤器将会使得 Get() 调用中非必须的磁盘读操作大约减少 100 倍，每个 key 用于过滤器的位数增加将会进一步减少读磁盘次数，当然也会占用更多内存空间。**我们推荐数据集无法全部放入内存同时又存在大量随机读的应用设置一个过滤器策略。**

如果你在使用自定义的比较器，应该确保你在用的过滤器策略与你的比较器兼容。举个例子，如果一个比较器在比较 key 的时候忽略结尾的空格，那么 `NewBloomFilterPolicy` 一定不能与此比较器共存。相反，应用应该提供一个自定义的过滤器策略，而且它也应该忽略 key 的尾部空格，示例如下：

```cpp
class CustomFilterPolicy : public leveldb::FilterPolicy {
 private:
  FilterPolicy* builtin_policy_;

 public:
  CustomFilterPolicy() : builtin_policy_(NewBloomFilterPolicy(10)) {}
  ~CustomFilterPolicy() { delete builtin_policy_; }

  const char* Name() const { return "IgnoreTrailingSpacesFilter"; }

  void CreateFilter(const Slice* keys, int n, std::string* dst) const {
    //Use builtin bloom filter code after removing trailing spaces
    std::vector<Slice> trimmed(n);
    for (int i = 0; i < n; i++) {
      trimmed[i] = RemoveTrailingSpaces(keys[i]);
    }
    return builtin_policy_->CreateFilter(&trimmed[i], n, dst);
  }
};
```

当然也可以自己提供非基于布隆过滤器的过滤器策略，具体见 `leveldb/filter_policy.h`。

#### 3.6 校验和（Checksums）

leveldb 将校验和与它存储在文件系统中的所有数据进行关联，对于这些校验和，有两个独立的控制：

`ReadOptions::verify_checksums` 可以设置为 true，以强制对所有从文件系统读取的数据进行校验。默认为 false，即，不会进行这样的校验。

`Options::paranoid_checks` 在数据库打开之前设置为 true ，以使得数据库一旦检测到数据损毁立即报错。根据数据库损坏的部位，报错可能是在打开数据库后，也可能是在后续执行某个操作的时候。该配置默认是关闭状态，即，持久化存储部分损坏数据库也能继续使用。

如果数据库损坏了(当开启 Options::paranoid_checks 的时候可能就打不开了)，`leveldb::RepairDB()` 函数可以用于对尽可能多的数据进行修复。



#### 3.7 近似空间大小

**GetApproximateSizes** 方法用于获取一个或多个键区间占据的文件系统近似大小(单位, 字节)

```cpp
leveldb::Range ranges[2];
ranges[0] = leveldb::Range("a", "c");
ranges[1] = leveldb::Range("x", "z");
uint64_t sizes[2];
db->GetApproximateSizes(ranges, 2, sizes);
```

上述代码结果是，size[0] 保存 [a..c) 区间对应的文件系统大致字节数。size[1] 保存 [x..z) 键区间对应的文件系统大致字节数。



#### 3.8 环境变量

由 **leveldb** 发起的全部文件操作以及其它的操作系统调用最后都会被路由给一个 `leveldb::Env` 对象。用户也可以提供自己的 Env 实现以达到更好的控制。比如，如果应用程序想要针对 **leveldb** 的文件 IO 引入一个人工延迟以限制 leveldb 对同一个系统中其它应用的影响：

```cpp
// 定制自己的 Env 
class SlowEnv : public leveldb::Env {
  ... implementation of the Env interface ...
};

SlowEnv env;
leveldb::Options options;
// 用定制的 Env 打开数据库
options.env = &env;
Status s = leveldb::DB::Open(options, ...);
```



#### 3.9 可移植

如果某个特定平台提供 `leveldb/port/port.h` 导出的类型/方法/函数实现，那么 leveldb 可以被移植到该平台上，更多细节见 `leveldb/port/port_example.h`。

另外，新平台可能还需要一个新的默认的 leveldb::Env 实现。具体可参考 `leveldb/util/env_posix.h` 实现。









**参考链接**

* LevelDB介绍（非常详细） http://c.biancheng.net/view/9836.html
* Leveldb 基本介绍和使用指南 https://zhuanlan.zhihu.com/p/322520485
* 官方文档 https://github.com/google/leveldb/blob/main/doc/index.md

