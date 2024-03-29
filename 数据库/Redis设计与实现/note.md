# 数据结构与对象
Redis数据库中每个键值对都是由对象组成的：
- 数据库键总是一个字符串对象
- 对应的值可以是字符串对象、列表对象、哈希对象、集合对象、有序集合对象中的一种

# 简单动态字符串
Redis没有使用C风格字符串，而是构建了一种名为简单动态字符串（SDS）的抽象类型，用它做默认字符串表示。C风格字符串则基本只用于字面值常量，如：
```c
redisLog(REDIS_WARNING, "Redis is now ready to exit, bye bye...");
```

因此，在Redis数据库中，存储字符串使用的是SDS。例如执行命令：
```shell
redis> SET msg "hello world"
```
Redis创建一个键值对：
- 键是一个字符串对象，底层中是保存"msg"的SDS
- 值是一个字符串对象，底层中是保存"hello world"的SDS

SDS还可以用作缓冲区，如AOF模块中的缓冲区，以及客户端状态中的输入缓冲区。

## SDS的定义
```c
struct sdshdr {
    int len;        // 已使用空间的长度
    int free;       // 未使用空间的长度
    char buf[];     // 字节数组，保存字符串（以'\0'结尾）
};
```
空字符由Redis自动维护，len和free均不计入空字符。例如：

![SDS示例](image/SDS示例.png)

## SDS和C字符串的区别

### 常数复杂度获取字符串长度
对于C风格字符串，获取它的长度总是需要遍历整个字符串。而SDS则可以简单地返回len属性。SDS在操作字符串的时候会自动维护好len属性。

### 杜绝缓冲区溢出
由于C字符串获取长度的困难，库函数往往不对字符串进行检查，很容易会有缓冲区溢出的问题。如在没有分配空间的情况下就把字符串 `strcat` 到已有字符串后面。

相比之下SDS会提前检查空间是否足够，如果不够则会自动扩展空间。

### 减少修改字符串时带来的内存重分配次数
SDS通过预留一部分未使用空间来试图减少内存重新分配的次数，它通过两种优化策略来实现：
- 空间预分配：如果字符串需要更大的空间，SDS通常会多分配一些内存，具体策略为
    - 如果需要的长度小于1M，那么free会等于len，即分配双倍的空间
    - 如果需要的长度大于1M，那么free会等于1M，即多分配1M的空间
- 惰性空间释放：如果字符串的长度缩短，SDS并不会立即释放多余的空间，同时它提供了API来手动释放多余的空间。

### 二进制安全
SDS是二进制安全的，这意味着字符串中间可以出现空字符，SDS的API可以良好的处理这种情况。从而可以使用SDS存储图片等二进制数据。

### 兼容部分C字符串函数
因为SDS的数组也以空字符结尾，所以它能够复用某些C语言字符串库函数。

### 总结
两者的区别在表格中列出：
| 特点 | SDS | C字符串 |
| --- | --- | --- |
| 获取长度 | O(1) | O(N) |
| 防止缓冲区溢出 | 是 | 否 |
| 减少内存重分配次数 | 是 | 否 |
| 可以保存二进制数据 | 是 | 否 |
| 使用 `<string.h>` 中函数 | 部分 | 全部 |

## SDS API
| 函数 | 作用 | 时间复杂度 |
| --- | --- | --- |
| sdsnew | 创建一个包含给定C字符串的SDS | O(N) |
| sdsempty | 创建一个空SDS | O(1) |
| sdsfree | 释放SDS | O(N) |
| sdslen | 获取SDS的长度 | O(1) |
| sdsavail | 获取SDS的未使用空间 | O(1) |
| sdsdup | 复制SDS | O(N) |
| sdsclear | 清空SDS | O(1) |
| sdscat | 将给定C字符串拼接到SDS的末尾 | O(N)，N为C字符串的长度 |
| sdscatsds | 将给定SDS拼接到SDS的末尾 | O(N)，N为被拼接的SDS的长度 |
| sdscpy | 将给定C字符串复制到SDS中，覆盖原有字符串 | O(N)，N为C字符串的长度 |
| sdsgrowzero | 扩展SDS的长度，未使用空间用0填充 | O(N)，N为扩展的长度 |
| sdsrange | 截取SDS的一部分 | O(N)，N为截取的长度 |
| sdstrim | 提供一个C字符串，如果SDS两端出现了其中的字符，移除它们 | O(MN)，M为C字符串的长度，N为SDS的长度 |
| sdscmp | 比较两个SDS是否相同 | O(N)，N为两个SDS中较短的长度 |

<br><br>

# 链表
由于C语言没有自己的链表，Redis自己定义了链表实现，常常在列表键中使用。当列表包含了比较多的元素或其中的元素都是长字符串时，Redis会使用链表。例如：
```shell
# 查看列表integers的长度
redis> LLEN integers
(integer) 1024

# 查看索引0到10的元素
redis> LRANGE integers 0 10
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
6) "6"
7) "7"
8) "8"
9) "9"
10) "10"
11) "11"
```
`integers` 底层就是使用链表实现的，每个节点保存一个整数。除了列表，发布与订阅、慢查询、监视器等功能也用到链表，Redis服务器也使用链表来保存客户端状态，并且构建客户端输出缓冲区。

## 链表和链表节点的实现
链表节点通过 `adlist.h` 中的一个结构体定义：
```c
typedef struct listNode {
    // 前置节点
    struct listNode *prev;

    // 后置节点
    struct listNode *next;

    // 节点的值
    void *value;
} listNode;
```
仅使用节点就可以形成链表了，不过Redis还在 `adlist.h` 中定义了链表结构体：
```c
typedef struct list {
    // 表头节点
    listNode *head;

    // 表尾节点
    listNode *tail;

    // 节点数量
    unsigned long len;

    // 节点值复制函数
    void *(*dup)(void *ptr);

    // 节点值释放函数
    void (*free)(void *ptr);

    // 节点值对比函数
    int (*match)(void *ptr, void *key);
} list;
```
其中 `dup` 、 `free` 和 `match` 用于实现多态链表。

可以看出，Redis的链表具有如下特性：
- 双向：具有指向前置节点和后置节点的指针
- 无环：表头节点的 `prev` 和表尾节点的 `next` 都指向 `NULL`
- 带表头和表尾指针：可以快速在头部和尾部操作
- 带链表长度计数器：可以快速获取链表长度
- 多态：三个函数被实现为函数指针，可以自行定义来用于保存不同类型的值

## 链表和链表节点的API
| 函数 | 作用 | 时间复杂度 |
| --- | --- | --- |
| listSetDupMethod | 设置 `match` 函数 | O(1) |
| listGetDupMethod | 获取 `match` 函数 | O(1) |
| listSetFreeMethod | 设置 `free` 函数 | O(1) |
| listGetFree | 获取 `free` 函数 | O(1) |
| listSetMatchMethod | 设置 `match` 函数 | O(1) |
| listGetMatchMethod | 获取 `match` 函数 | O(1) |
| listLength | 获取链表长度 | O(1) |
| listFirst | 获取链表头节点 | O(1) |
| listLast | 获取链表尾节点 | O(1) |
| listPrevNode | 获取给定节点的前置节点 | O(1) |
| listNextNode | 获取给定节点的后置节点 | O(1) |
| listNodeValue | 获取给定节点的值 | O(1) |
| listCreate | 创建一个链表 | O(1) |
| listAddNodeHead | 在链表头部添加一个节点 | O(1) |
| listAddNodeTail | 在链表尾部添加一个节点 | O(1) |
| listInsertNode | 在给定节点前或后插入一个节点 | O(1) |
| listSearchKey | 在链表中查找包含给定值的节点 | O(N) |
| listIndex | 获取链表中给定索引的节点 | O(N) |
| listDelNode | 删除给定节点 | O(1) |
| listRotate | 将链表尾部节点移动到头部 | O(1) |
| listDup | 复制链表 | O(N) |
| listRelease | 释放链表以及所有节点 | O(N) |

<br><br>

# 字典
字典又称符号表、关联数组、映射，按键值对的方式保存数据。由于C语言没有自己的字典，Redis自己实现了字典，它的应用相当广泛，实际上Redis数据库底层上就是使用字典来实现的。例如：
```shell
# 创建一个键值对，它被保存在数据库的字典中
redis> SET msg "hello world"
OK
```
除了数据库，哈希键也使用字典实现。如果它的键值对比较多或者键值对中元素都是长字符串，Redis就会使用字典。例如：
```shell
# 查看哈希键website的键值对数量
redis> HLEN website
(integer) 10086

# 查看哈希键website的所有键值对
redis> HGETALL website
1) "Redis"          # 键
2) "Redis.io"       # 值
3) "MariaDB"        # 键
4) "MariaDB.org"    # 值
5) "MangoDB"        # 键
6) "MangoDB.org"    # 值
# ...
```

## 字典的实现
字典是通过哈希表数据结构实现的。

### 哈希表
哈希表定义在 `dict.h` 中：
```c
typedef struct dictht {
    // 哈希表数组
    dictEntry **table;

    // 哈希表大小
    unsigned long size;

    // 哈希表大小掩码，用于计算索引值，总是等于size-1
    unsigned long sizemask;

    // 哈希表已有节点的数量
    unsigned long used;
} dictht;
```
`table` 是一个指针数组，每个元素指向一个 `dictEntry` 结构体，它保存一个键值对。 `sizemask` 和哈希值一起决定一个键应该被放到哈希表的哪个索引上。

### 哈希表节点
哈希表节点也通过结构体定义：
```c
typedef struct dictEntry {
    // 键
    void *key;

    // 值
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
    } v;

    // 指向下一个哈希表节点，形成链表
    struct dictEntry *next;
} dictEntry;
```
可以看出，值可以是指针和64位整数。有一个 `next` 指针，说明Redis哈希表使用的是链地址法解决哈希冲突。

### 字典
字典定义在 `dict.h` 中：
```c
typedef struct dict {
    // 类型特定函数
    dictType *type;

    // 私有数据
    void *privdata;

    // 哈希表
    dictht ht[2];

    // rehash索引。如果值为-1，说明此时没有进行rehash操作
    long rehashidx;
} dict;
```
`type` 和 `privdata` 是为了支持多态字典：
- `type` 指向一个 `dictType` 结构体，它保存了一组用于操作键值对的函数
- `privdata` 保存了传递给这些函数的可选参数

其中 `dictType` 结构体定义如下：
```c
typedef struct dictType {
    // 计算哈希值的函数
    unsigned int (*hashFunction)(const void *key);

    // 复制键的函数
    void *(*keyDup)(void *privdata, const void *key);

    // 复制值的函数
    void *(*valDup)(void *privdata, const void *obj);

    // 对比键的函数
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);

    // 销毁键的函数
    void (*keyDestructor)(void *privdata, void *key);

    // 销毁值的函数
    void (*valDestructor)(void *privdata, void *obj);
} dictType;
```

注意到 `ht` 包含两个元素，它们都是一个哈希表，一般来说字典只使用 `ht[0]` ，在 `rehash` 的时候会临时使用 `ht[1]` 。

一般情况下的字典如下图所示：

![字典示例](image/字典示例.png)

## 哈希算法
Redis使用的哈希算法如下：
```c
// 使用type中的hashFunction函数计算键的哈希值
hash = dict->type->hashFunction(key);

// 使用掩码计算索引值
index = hash & dict->ht[0].sizemask;
```
如果字典用作数据库的底层实现， `hashFunction` 由Redis来选择，它会使用 `MurmurHash` 算法。这个算法的优点是，即使输入的键是有规律的，算法依然能良好地把它们分布到哈希表的不同索引上。算法最新的版本已经是 `MurmurHash3` 了，不过Redis仍然使用 `MurmurHash2` 。

## 解决键冲突
Redis使用链地址法解决键冲突，因为没有指向表尾的指针，所以Redis使用的是头插法。

## rehash
如果哈希表的负载因子太大，效率就不理想；如果太小，会浪费内存。因此在负载因子达到一定阈值的时候，哈希表会进行 `rehash` 操作。步骤如下：
1. 为 `ht[1]` 分配空间，大小取决于执行的操作：
    - 扩展：原来的 `used` 为 $N_0$ ，新 `size` 是 $N$ ，满足 $N \geq 2N_0$ 并且 $N$ 是2的幂次方
    - 缩小：原来的 `used` 为 $N_0$ ，新 `size` 是 $N$ ，满足 $N \leq N_0$ 并且 $N$ 是2的幂次方
1. 把 `ht[0]` 中的所有键值对迁移到 `ht[1]` 中
1. 释放 `ht[0]` 的空间，把 `ht[1]` 的地址赋给 `ht[0]`，在 `ht[1]` 上重新创建一个空的哈希表

### 哈希表的扩展与收缩
当满足以下条件时，Redis会对哈希表进行扩展：
- 服务器当前没有执行 `BGSAVE` 或 `BGREWRITEAOF` 命令，且哈希表的负载因子达到1
- 服务器正在执行 `BGSAVE` 或 `BGREWRITEAOF` 命令，但哈希表的负载因子达到5

负载因子如下计算：
```c
load_factor = ht[0].used / ht[0].size
```

当负载因子小于0.1时，Redis会对哈希表进行收缩。

## 渐进式rehash
实际上把 `ht[0]` 中的键值对迁移到 `ht[1]` 并不是一次性完成的，这是为了避免迁移过程在短时间内占用了大量的服务器资源从而导致服务器停止服务。因此，渐进式 `rehash` 过程如下：
1. 为 `ht[1]` 分配空间
1. 把 `rehashidx` 设为0，标志着 `rehash` 开始
1. 每次对字典执行添加、删除、查找或更新操作时，顺带把 `ht[0]` 中对应于 `rehashidx` 索引的桶中的所有键值对迁移到 `ht[1]` 中，然后递增 `rehashidx`
1. 直到某个时刻完成所有的迁移，把 `rehashidx` 设为-1，表示 `rehash` 结束

### 渐进式rehash执行期间的哈希表操作
如果查找某个键，先在 `ht[0]` 中查找，如果没有找到还需要在 `ht[1]` 中查找。所有的添加操作只会在 `ht[1]` 中进行。

## 字典API
| 函数 | 作用 | 时间复杂度 |
| --- | --- | --- |
| dictCreate | 创建一个字典 | O(1) |
| dictAdd | 向字典中添加一个键值对 | O(1) |
| dictReplace | 向字典中添加一个键值对，如果键已存在则替换原有值 | O(1) |
| dictFetchValue | 返回给定键的值 | O(1) |
| dictGetRandomKey | 随机返回一个键值对 | O(1) |
| dictDelete | 删除给定键的键值对 | O(1) |
| dictRelease | 释放字典以及所有键值对 | O(N) |
