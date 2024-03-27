# 1 - 什么是eBPF，为何它如此重要？
eBPF大概可以认为是给内核提供的一段代码，它可以被关联到一个事件上，当这个事件发生时，这段代码就会被执行。eBPF有自己的一套语法，内核会处理好这段代码并验证它的安全性，只有安全的代码才会被接受。这意味着eBPF不会导致机器崩溃或陷入死循环，并且不会允许数据被泄露。

## eBPF的动态加载
eBPF几乎是热插拔的，添加eBPF程序以后无需重启内核就可以生效。

## eBPF程序的高性能
eBPF会被即时编译成本地机器码，并且它直接在内核中运行，不需要用户态和内核态之间的交互，从而获得了非常高的性能。通过eBPF在内核态先处理一些数据，可以避免一些数据被传递到用户态，从而提高性能。

## 云原生环境中的eBPF
云服务器往往采用云原生的方式组织，使用容器、Kubernetes或ECS等编排工具，或者像Lambda、云函数、Fargate等无服务器方法。它们往往自动进行负载均衡，在无服务器环境中，甚至不知道每个工作负载在哪个服务器上运行。

然而使用eBPF可以实现：
- 无需修改应用程序就可以用eBPF监控应用程序
- 无需重启服务器就可以用eBPF观察应用进程

相比之下Sidecar模型把监控工具作为一个容器运行，并向Kubernetes Pod注入它们，这比直接修改应用程序要方便。然而也有一些缺点：
- 添加Sidecar需要重启应用程序Pod
- 需要对应用程序的YAML修改，一般这是自动完成的，不过一旦有问题添加Sidecar就会失败
- 注入Sidecar会显著增加Pod的启动时间，并且可能向程序引入竞争条件或其他不确定问题
- 如果使用Sidecar来实现网络功能，那么流量需要先在内核中绕一圈，然后再到达Sidecar，这会降低性能，如下图

![Sidecar](image/Sidecar.png)

# 2 - eBPF的Hello World

## BCC的Hello World
此程序使用BCC的Python库编写：
```python
#!/usr/bin/python3
from bcc import BPF

# eBPF程序，使用C语言编写，调用辅助函数bpf_trace_printk()打印消息
program = r"""
int hello(void *ctx) {
    bpf_trace_printk("Hello World!");
    return 0;
}
"""

# 创建BPF对象，传入eBPF程序，它会自动编译并加载到内核中
b = BPF(text=program)

# 为了把eBPF附加到系统调用execve()上，先查询该系统调用
syscall = b.get_syscall_fnname("execve")

# 使用内核探针kprobe，把hello程序附加到该事件上
b.attach_kprobe(event=syscall, fn_name="hello")

# 读取内核输出的消息并打印
b.trace_print()
```
实际上 `bpf_trace_printk()` 把输出写入一个伪文件 `/sys/kernel/debug/tracing/trace_pipe` 中，然后程序从中取出输出信息。可以使用 `cat` 查看它的内容，这需要root权限。

## 运行Hello World
eBPF需要特权才能运行，在早期的内核版本上需要root身份或者使用sudo运行。内核5.8版本以后，可以使用 `CAP_BPF` 能力：
- 加载跟踪程序需要：`CAP_PERFMON` 和 `CAP_BPF`
- 加载网络程序需要：`CAP_NET_ADMIN` 和 `CAP_BPF`

运行它可能看到如下的输出：
```shell
$ sudo ./hello.py
b' bash-5412 [001] .... 90432.904952: 0: bpf_trace_printk: Hello World'
```
在这条信息中能够看到，调用 `execve()` 的进程的ID为5412，它正在运行bash，以及打印出来的消息"Hello World"。

使用单一eBPF程序并从 `trace_pipe` 中读取输出是简单的。然而它只能输出字符串并且格式受限，当有多个eBPF程序时更是混乱，相比之下eBPF Map更加灵活。

## BPF Map
Map是一种数据结构，在内核态的eBPF程序和用户态程序都可以访问它，常用于多个eBPF程序以及用户态程序之间进行通信。

它通常有如下用途：
- 用户空间写入配置信息，由eBPF程序读取
- 一个eBPF程序存储状态，供另一个eBPF程序使用
- eBPF程序写入结果或指标，用户空间可以读取

Linux的 `uapi/linux/bpf.h` 文件中定义了各种BPF Map，内核文档中有一些相关信息。它们都是键-值存储，包括哈希表、perf和环形缓冲区以及数组等。

有的Map被定义为数组类型，使用一个4字节索引为键，其他MAP可以使用任意数据类型作为键。

有的Map针对某些操作进行了优化，如队列、栈、LRU存储、最长前缀匹配和Bloom过滤器等。

有的Map用来存储特定类型的信息，如 `sockmap` 和 `devmap` 保存套接字和网络设备的信息，程序数组存储一组eBPF程序的索引便于调用其他的eBPF程序，甚至可以有一个Map存储其他Map。

有的Map具有per-CPU版本，使得每个CPU使用不同的内存来避免数据一致性问题。而对于普通版本，在内核5.1版本以后，提供了自旋锁来进行同步。

### Hash Table Map
看一个使用Map的示例程序，它同样附加到 `execve()` 系统调用上，首先是eBPF程序本身：
```c
// BPF_HASH()是BCC宏，定义了一个哈希表映射（这是一行变量定义语句）
BPF_HASH(counter_table);

int hello(void *ctx) {
   u64 uid;
   u64 counter = 0;
   u64 *p;

   // bpf_get_current_uid_gid()获取触发事件的进程的uid和gid
   // 返回值的低32位为uid，高32位为保存gid
   uid = bpf_get_current_uid_gid() & 0xFFFFFFFF;

   // 在哈希表中查找键为uid的条目，返回一个指向其值的指针，找不到则返回0
   p = counter_table.lookup(&uid);

   // 如果有该条目，把值赋值给counter
   if (p != 0) {
      counter = *p;
   }
   
   // 递增counter
   counter++;

   // 更新哈希表中键为uid的条目的值为counter
   counter_table.update(&uid, &counter);

   return 0;
}
```
比较神奇的是像 `counter_table.lookup(&uid)` 这样的代码，这并不是标准的C语言。BCC会在编译之前对这样的代码进行处理，转换成标准的C语言代码。

然后是Python程序：
```python
b = BPF(text=program)
syscall = b.get_syscall_fnname("execve")
b.attach_kprobe(event=syscall, fn_name="hello")

while True:
    # 每隔两秒执行一次
    sleep(2)

    # 存储结果的字符串
    s = ""

    # 哈希表由BCC创建一个Python对象来维护，可以用名字获取特定的Map
    for k, v in b["counter_table"].items():
        s += s += f"ID {k.value}: {v.value}\t"

    # 打印结果
    print(s)
```
Map的使用非常方便，但是用户空间需要轮询来获取数据。

### Perf和环形缓冲区Map
Linux内核支持了perf子系统，并且eBPF支持perf缓冲区，以及它的后继者BPF环形缓冲区。

在5.8或更高版本的内核中，更推荐使用BPF环形缓冲区，而不是BPF perf缓冲区，在安德烈·纳克里科的[BPF 环形缓冲区博文](https://oreil.ly/ARRyV)介绍了两者的区别。

![RingBuffer](image/RingBuffer.png)

环形缓冲区并不只用于eBPF，它实际上可以看做逻辑上为环形的一块内存，具有读写两个指针。数据被写入到写指针的位置，并被附上长度信息的头部，然后移动写指针。然后读指针可以通过头部获取信息长度，读取数据并移动读指针。当读指针赶上写指针，表示没有数据可读。当写指针赶上读指针，表示环形缓冲区已满，它不会再写入数据，而是递增丢弃计数器。读取数据时会一并读取丢弃计数器，这样用户空间可以知道有多少数据丢失。

接下来这个Hello World程序会比较复杂，首先是eBPF程序：
```c
// BCC宏BPF_PERF_OUTPUT用于创建一个map，它用来将消息从内核传递到用户空间
BPF_PERF_OUTPUT(output);

// 数据被写入一个结构体，包含pid、uid、当前命令名称、文本消息
struct data_t {
   int pid;
   int uid;
   char command[16];
   char message[12];
};

int hello(void *ctx) {
   // data用来构造要提交的结构体，message保存字符串"Hello World"
   struct data_t data = {};
   char message[12] = "Hello World";

   // bpf_get_current_pid_tgid()获取触发事件线程的pid和tgid（应相同）
   // 高32位为pid，低32位为tgid
   data.pid = bpf_get_current_pid_tgid() >> 32;

   // bpf_get_current_uid_gid()如前所述
   data.uid = bpf_get_current_uid_gid() & 0xFFFFFFFF;

   // bpf_get_current_comm()获取触发事件的进程运行的可执行文件名称
   // 结果被写入提供的缓冲区，需要提供缓冲区大小
   bpf_get_current_comm(&data.command, sizeof(data.command));

   // 本程序中，固定地将消息Hello World写入data结构体的message字段
   bpf_probe_read_kernel(&data.message, sizeof(data.message), message);

   // 结构体准备好，调用perf_submit()将该数据放入map中
   output.perf_submit(ctx, &data, sizeof(data));

   return 0;
}
```
然后可以在Python中读取这个Map：
```python
b = BPF(text=program)
syscall = b.get_syscall_fnname("execve")
b.attach_kprobe(event=syscall, fn_name="hello")

def print_event(cpu, data, size):
    # 同样可以用名字output来获取Map，用event()方法获取数据
    data = b["output"].event(data)
    print(f"{data.pid} {data.uid} {data.command.decode()} {data.message.decode()}")

# 打开perf环形缓冲区，注册收到数据时的回调函数print_event
b["output"].open_perf_buffer(print_event)

# 不断轮询，有数据到达就会触发回调函数
while True:
    b.perf_buffer_poll()
```

### 函数调用
早期的eBPF仅支持调用提供的辅助函数，为了调用其他函数，程序员通常用 `__always_inline` 指示编译器内联展开自己的函数，如：
```c
static __always_inline void my_function(void *ctx, int val)
```
不过在4.16版本的内核和LLVM 6.0开始，eBPF提供了函数调用的支持，名为“BPF to BPF函数调用”或“BPF子程序”，它目前不被BCC支持。

### 尾部调用
尾部调用可以调用其他的eBPF程序，与普通函数调用不同的是，它并不从被调函数中返回，从而不需要为调用函数保存栈帧。实际上eBPF中栈大小限制为512字节，因此这是非常有用的。

尾部调用使用特殊辅助函数实现：
```c
long bpf_tail_call(void *ctx, struct bpf_map *prog_array_map, u32 index);
```
其中， `ctx` 把上下文传递给被调程序； `prog_array_map` 是一个 `BPF_MAP_TYPE_PROG_ARRAY` 类型的Map，保存了一组eBPF程序的描述符； `index` 是要调用的程序的索引。如果它执行成功就不会返回，否则它返回然后程序继续向下执行。

所有要被调用的eBPF需要被加载到内核中，然后存储到Map里面。

看一个示例，主eBPF被附加到系统调用通用入口，它使用尾部调用跟踪某些系统调用操作码的特定消息，如果没有匹配的操作码它就跟踪一个通用的信息。在BCC中，尾部调用可以简化一些：
```c
prog_array_map.call(ctx, index)
```
然后BCC会把它重写为：
```c
bpf_tail_call(ctx, prog_array_map, index)
```

eBPF程序如下：
```c
// BCC宏BPF_PROG_ARRAY定义BPF_MAP_TYPE_PROG_ARRAY类型的Map
// 这里数组名字为syscall，支持最多300条数据
BPF_PROG_ARRAY(syscall, 300);

// 它附加到sys_enter原始跟踪点上，任何系统调用时都会触发它
// 附加到原始跟踪点的程序的上下文以bpf_raw_tracepoint_args的形式传递
int hello(struct bpf_raw_tracepoint_args *ctx) {
    // 对于sys_enter，参数中包括用于标识正在进行的系统调用的操作码
    int opcode = ctx->args[1];

    // 进行尾部调用
    syscall.call(ctx, opcode);

    // 没有对应条目的情况，打印通用调试信息
    bpf_trace_printk("Another syscall: %d", opcode);

    return 0;
}

// 被尾部调用的程序。当操作码指示为execve()时，它被执行，生成一行跟踪信息
int hello_exec(void *ctx) {
    bpf_trace_printk("Executing a program");
    return 0;
}


// 多个opcode会调用这个程序，它在内部根据opcode打印不同的消息
int hello_timer(struct bpf_raw_tracepoint_args *ctx) {
    int opcode = ctx->args[1];
    switch (opcode) {
        case 222:
            bpf_trace_printk("Creating a timer");
            break;
        case 226:
            bpf_trace_printk("Deleting a timer");
            break;
        default:
            bpf_trace_printk("Some other timer operation");
            break;
    }
    return 0;
}

// 什么都不做，不打印跟踪信息的系统调用会调用这个程序
int ignore_opcode(void *ctx) {
    return 0;
}
```

接下来是Python程序：
```python
b = BPF(text=program)

# 不附加到kprobe，而是附加到sys_enter原始跟踪点上
b.attach_raw_tracepoint(tp="sys_enter", fn_name="hello")

# 加载尾部调用程序并返回一个文件描述符
# 尾部调用程序需要与父程序有相同的类型，这里为BPF.RAW_TRACEPOINT
ignore_fn = b.load_func("ignore_opcode", BPF.RAW_TRACEPOINT)
exec_fn = b.load_func("hello_exec", BPF.RAW_TRACEPOINT)
timer_fn = b.load_func("hello_timer", BPF.RAW_TRACEPOINT)

# 在map中创建条目，如果opcode没有对应条目，尾部调用不会被实际执行
prog_array = b.get_table("syscall")
prog_array[ct.c_int(59)] = ct.c_int(exec_fn.fd)
prog_array[ct.c_int(222)] = ct.c_int(timer_fn.fd)
prog_array[ct.c_int(223)] = ct.c_int(timer_fn.fd)
prog_array[ct.c_int(224)] = ct.c_int(timer_fn.fd)
prog_array[ct.c_int(225)] = ct.c_int(timer_fn.fd)
prog_array[ct.c_int(226)] = ct.c_int(timer_fn.fd)

# 一些频繁执行并且没有必要跟踪的系统调用
prog_array[ct.c_int(21)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(22)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(25)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(29)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(56)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(57)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(63)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(64)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(66)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(72)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(73)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(79)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(98)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(101)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(115)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(131)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(134)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(135)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(139)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(172)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(233)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(280)] = ct.c_int(ignore_fn.fd)
prog_array[ct.c_int(291)] = ct.c_int(ignore_fn.fd)

# 将跟踪输出打印到屏幕，直到用户终止程序。
b.trace_print()
```

从内核4.2版本开始，eBPF开始支持尾调用。很长一段时间中，尾部调用和BPF子程序并不兼容，不过在内核5.10版本解决了这个问题。（从BPF子程序进行尾调用需要JIT编译器支持）

尾部调用最多可以链式组合到33次，每个eBPF程序可以复杂到100万条指令，从而eBPF可以完成非常复杂的功能。
