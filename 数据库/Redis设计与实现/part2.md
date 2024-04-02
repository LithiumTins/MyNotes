# 10 - RDB持久化
Redis是内存数据库，为了把数据写入磁盘，Redis提供了RDB持久化功能，可以把Redis在内存中的数据库状态保存到磁盘上。RDB持久化既可以手动执行也可以根据服务器配置选项定期执行，它生成一个经过压缩的二进制RDB文件，通过该文件就可以还原数据库状态。

## RDB文件的创建和载入
有两个命令用于手动创建RDB文件，分别是 `SAVE` 和 `BGSAVE` 。其中 `SAVE` 命令阻塞Redis服务器进程直到RDB文件创建完成，如：
```shell
redis> SAVE
# 阻塞直到完成
OK
```
相比之下， `BGSAVE` 命令会派生一个子进程来执行RDB文件的创建，父进程可以继续处理客户端请求，如：
```shell
redis> BGSAVE
# 立即返回
Background saving started
```
创建RDB文件实际上通过 `rdb.c/rdbSave` 函数完成，两个命令以不同的方式调用这个函数。以Python伪代码来表示：
```python
def SAVE():
    rdbSave()   # 直接调用

def BGSAVE():
    pid = fork()    # 创建子进程
    if pid == 0:
        rdbSave()           # 子进程调用
        signal_parent()     # 通知父进程完成
    elif pid > 0:
        handle_request_and_wait_signal()
    else:
        handle_fork_error()
```
虽然RDB文件通过两个命令手动创建，但启动服务器时Redis会自动检测并载入RDB文件，因此它没有提供载入RDB文件的命令。RDB文件的载入是通过 `rdb.c/rdbLoad` 函数来完成的，在有RDB文件的情况下，可以看到这样的日志：
```shell
$ redis-server
[7379] 30 Aug 21:07:01.270 * Server started, Redis version 2.9.11
[7379] 30 Aug 21:07:01.270 * DB loaded from disk: 0.018 seconds     # 表示载入RDB文件
[7379] 30 Aug 21:07:01.270 * The server is now ready to accept connections on port 6379
```
由于AOF文件更新频率更高，因此它记录的数据库很可能更新，Redis会优先选择AOF文件来载入数据库。

### SAVE命令执行时的服务器状态
服务器被阻塞，所有的客户端请求也被阻塞，只有执行完 `SAVE` 以后服务器才重新开始处理客户端请求。

### BGSAVE命令执行时的服务器状态
虽然 `BGSAVE` 在写入RDB文件时依然可以处理客户端请求，但是它处理 `SAVE` 、`BGSAVE`、`BGREWRITEAOF` 等命令的方式会和平时不同：
- 为了防止父进程和子进程同时调用 `rdbSave` 产生竞争条件，不允许在此时调用 `SAVE` 和 `BGSAVE` 命令
- 为了避免短时间内占用过多性能，`BGREWRITEAOF` 命令会被推迟到 `BGSAVE` 完成后执行

如果服务器正在执行 `BGRWRITEAOF` 命令，那么 `BGSAVE` 命令会被拒绝。

### RDB文件载入时的服务器状态
服务器一直阻塞直至RDB文件载入完成。

## 自动间隔性保存
Redis允许配置服务器的 `save` 选项，让其每过一段时间自动执行一次 `BGSAVE` 命令。可以同时设置多个 `save` 配置，其中一个满足就会执行 `BGSAVE` 命令。例如：
```
save 900 1
save 300 10
save 60 10000
```
那么任何一个条件满足就会进行保存：
- 900秒内，进行了1次修改
- 300秒内，进行了10次修改
- 60秒内，进行了10000次修改

自动保存时可以看到如下日志：
```shell
[5085] 03 Sep 17:09:49.463 * 10000 changes in 60 seconds. Saving...
[5085] 03 Sep 17:09:49.463 * Background saving started by pid 5189
[5189] 03 Sep 17:09:49.522 * DB saved on disk
[5189] 03 Sep 17:09:49.522 * RDB: 0 MB of memory used by copy-on-write
[5085] 03 Sep 17:09:49.563 * Background saving terminated with success
```

### 设置保存条件
可以通过设置配置文件或传入启动参数来设置 `save` 选项，如果没有进行设置，Redis会使用默认的 `save` 选项：
```
save 900 1
save 300 10
save 60 10000
```
