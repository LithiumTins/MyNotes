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
# 定义一个列表integers，包含1024个整数
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
