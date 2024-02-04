# 变量和基本类型

## 基本内置类型

### 算术类型
标准规定了算数类型的最小尺寸：
类型 | 含义 | 最小尺寸
---|---|---
bool | 布尔类型 | 未定义
char | 字符 | 8位
wchar_t | 宽字符 | 16位
char16_t | Unicode字符 | 16位
char32_t | Unicode字符 | 32位
short | 短整型 | 16位
int | 整型 | 16位
long | 长整型 | 32位
long long | 长整型 | 64位
float | 单精度浮点数 | 6位有效数字
double | 双精度浮点数 | 10位有效数字
long double | 扩展精度浮点数 | 10位有效数字

还有一些其他的规定，如：
$$
sizeof(short) \leq sizeof(int) \leq sizeof(long) \leq sizeof(long\ long)
$$

标准对于浮点类型的精度限制较为宽松，一般来说， `float` 支持7位有效数字， `double` 则支持16位有效数字。

#### 带符号类型和无符号类型
除了布尔型和字符型，其他的整型都分为带符号类型和无符号类型。默认情况下是带符号的，前面加 `unsigned` 则是无符号的。然而 `char` 就有三种，分别是 `char` 、 `signed char` 和 `unsigned char` ，单独的 `char` 并没有规定是带符号还是无符号。

### 类型转换
- 非 `bool` 赋值给 `bool` 时， `0` 转换为 `false` ，其他值转换为 `true`
- `bool` 赋值给非 `bool` 时， `false` 转换为 `0` ， `true` 转换为 `1`
- 浮点赋值给整型，只保留整数部分
- 整型赋值给浮点，保留整数部分，小数部分为 `0` ，如果超出浮点数的精度范围，那么会有精度损失
- 把过大的数赋值给无符号类型，得到的是取模后的结果
- 把超出范围的数赋值给带符号类型，结果是**未定义**的

#### 含有无符号类型的表达式
如果表达式中同时有带符号类型和无符号类型，那么带符号类型会转换为无符号类型。这可能导致负数转换成无符号类型，尽量避免混用带符号类型和无符号类型。

### 字面值常量
像 `42` 这样的，一看就知道其取值，称作字面值常量。

#### 整型和浮点型字面值
整型值可以是十进制、八进制、十六进制的，如20的三种表示形式：
```cpp
20      // 十进制
024     // 八进制
0x14    // 十六进制
```
十进制的字面值实际类型是带符号的，八进制和十六进制的字面值实际类型可能带符号也可能不带符号。十进制字面值的类型在 `int` 、 `long` 和 `long long` 中选取，八进制和十六进制的字面值的类型在 `int` 、 `unsigned int` 、 `long` 、 `unsigned long` 、 `long long` 和 `unsigned long long` 中选取。会采用能够容纳其值的最小的类型来存储。如果放不下，那么会报错。

浮点型字面值有如下形式：
```cpp
3.14159
3.14159E0
0.
0e0
.001
```

#### 字符和字符串字面值
像 `'a'` 这样的，称作字符字面值。像 `"Hello, World!"` 这样的，称作字符串字面值。当两个字符串字面值放在一起时，如：
```cpp
"Hello, " "World!"
```
它们实际上等价于 `"Hello, World!"` ，所以字符串过长时可以分行写。

#### 转义序列
表示 | 含义
--- | ---
`\n` | 换行
`\t` | 横向制表符
`\v` | 纵向制表符
`\a` | 响铃
`\b` | 退格
`\r` | 回车
`\"` | 双引号
`\'` | 单引号
`\\` | 反斜杠
`\?` | 问号
`\f` | 进纸符

可以使用泛化的转义序列，即用字符的ASCII码来表示字符，如：
- `'\7'` ：不加x则为八进制，不超过3个数字，否则截取前3个。响铃符
- `'\x4d` ：加x则为十六进制。字母M

#### 指定字面值的类型
加上特定的前缀和后缀，可以不使用字面值的默认类型，如字符和字符串字面量有：
| 前缀 | 含义 | 类型 |
--- | --- | ---
u | Unicode16字符 | char16_t
U | Unicode32字符 | char32_t
L | 宽字符 | wchar_t
u8 | UTF-8字符 | char

整型字面值有（其中也可以组合UL表示 `unsigned long` ）：
| 后缀 | 含义 | 类型 |
--- | --- | ---
u 或 U | 无符号 | unsigned
l 或 L | 长整型 | long
ll 或 LL | 长长整型 | long long

浮点型字面值有：
| 后缀 | 含义 | 类型 |
--- | --- | ---
f 或 F | 单精度浮点数 | float
l 或 L | 扩展精度浮点数 | long double

#### 布尔字面值
`true` 和 `false` 是布尔类型的字面值。

## 变量
具名的、可供程序操作的存储空间，在本书中与**对象**一词等价。

### 变量定义

#### 列表初始化
在C++11以后，对于一个 `int` 类型的变量，有以下四种初始化方式：
```cpp
int units_sold = 0;
int units_sold = {0};
int units_sold(0);
int units_sold{0};
```
其中使用花括号的初始化方式称为列表初始化，对于内置类型来说，如果存在丢失信息的风险，这种初始化方式将会出错。

#### 默认初始化
如果不指定初始值，称为进行了默认初始化。内置类型，对于全局变量，会被初始化为0；对于局部变量，不会被初始化，其值是未定义的。

### 变量声明和定义的关系
为了允许把程序拆分成多个逻辑部分进行开发，C++支持**分离式编译**，即程序可以被分割成多个文件，每个文件单独进行编译。为此，C++将声明和定义区分开来，声明使得名字为程序所知，定义负责创建与名字关联的实体，变量只能定义一次而可以声明多次。只声明而不定义一个变量的方法是使用 `extern` 关键字：
```cpp
extern int i; // 声明i而非定义i
int j; // 声明并定义j
```
然而，任何声明只要赋予了初始值，就是定义，比如这里的 `extern` 就没有任何作用：
```cpp
extern int i = 0; // 定义
```
在函数体内部，试图初始化一个由 `extern` 关键字标记的变量是非法的。

### 标识符
C++的标识符是由字母、数字、下划线组成，必须以字母或下划线开头。标识符是大小写敏感的。C++保留了一些标识符用作关键字：
| 列1 | 列2 | 列3 | 列4 | 列5 |
| --- | --- | --- | --- | --- |
| alignas | continue | friend | register | true |
| alignof | decltype | goto | reinterpret_cast | try |
| asm | default | if | return | typedef |
| auto | delete | inline | short | typeid |
| bool | do | int | signed | typename |
| break | double | long | sizeof | union |
| case | dynamic_cast | mutable | static | unsigned |
| catch | else | namespace | static_assert | using |
| char | enum | new | static_cast | virtual |
| char16_t | explicit | noexcept | struct | void |
| char32_t | export | nullptr | switch | volatile |
| class | extern | operator | template | wchar_t |
| const | false | private | this | while |
| constexpr | float | protected | thread_local | |
| const_cast | for | public | throw | |

还有一些操作符替代名，似乎会被宏替换成操作符，有：
| 列1 | 列2 | 列3 | 列4 | 列5 | 列6 |
| --- | --- | --- | --- | --- | --- |
| and | bitand | compl | not_eq | or_eq | xor_eq |
| and_eq | bitor | not | or | xor | |

### 名字的作用域
定义在所有花括号外的名字具有**全局作用域**，否则具有**块作用域**。

### 嵌套作用域
作用域可以包含在其他的作用域之中，被包含的称为**内层作用域**，包含的称为**外层作用域**。内层作用域可以重新定义外层作用域的名字，再次使用这个名字时，实际上访问的是内层作用域中的名字。

## 复合类型
指基于其他类型定义的类型，包括引用和指针。

### 引用
是对象的别名，实际不是一个对象。它绑定到一个对象上，不可以更改绑定的对象。

### 指针

#### 空指针
有几种可以获取空指针的方法：
```cpp
int *p1 = nullptr;
int *p2 = 0;
int *p3 = NULL; // 需要包含cstdlib
```
`nullptr` 是C++11引入的，是一种特殊的指针类型，可以转换成任意指针类型。在新标准下，尽量使用 `nullptr` 代替 `0` 和 `NULL` 。

## const限定符

#### 默认状态下，const对象仅在文件内有效
对于一个const对象，编译器往往把它们的所有出现替换成它的初始值，为了做到这一点，编译器需要知道这个对象的定义，所以const对象默认情况下只在文件内有效。如果需要一个行为类似于常规对象的const对象，需要在声明和定义的时候都加上 `extern` 关键字。

### const的引用
引用可以绑定到const对象上：
```cpp
const int ci = 1024;
const int &r1 = ci;
```

#### 初始化和对const的引用
一般来说，引用的类型需要与赋值给它的对象类型一致。实际上有两个例外：
- 初始化常量引用时，还可以使用表达式的计算结果，编译器会创建一个临时量
    ```cpp
    const int &r = 42;```
- const引用可以绑定到一个普通的变量上，这时不可以通过引用 `r` 来修改 `i` 的值
    ```cpp
    int i = 42;
    const int &r = i;
    ```

### 指针和const
指向常量的指针，不能使用该指针来修改指向的对象。常量指针，不允许改变其指向的对象。

### 顶层const
顶层const表示指针本身是个常量，底层const表示指针所指的对象是个常量。它们的声明方式不同：
```cpp
const int *p; // 底层const
int *const p; // 顶层const
```

### constexpr和常量表达式
常量表达式是指值不会改变并且在编译过程中就能得到计算结果的表达式。

#### constexper变量
C++11引入了 `constexpr` 关键字，用于声明变量或函数是常量表达式，编译器会检查它的初始值是否是一个常量表达式。普通的函数返回值不是常量表达式，只有 `constexpr` 函数才能返回常量表达式。

#### 字面值类型
常量表达式的值需要在编译器计算，所以对于 `constexpr` 可以修饰类类型需要加以限制，因为它们的类型简单、值容易得到，这种类型称为**字面值类型**。算术类型、引用和指针都是字面值类型。类类型一般不是字面值类型，但存在一些特例。

虽然指针和引用都可以用 `constexpr` 修饰，然而 `constexpr` 指针的初始值只能是 `nullptr` 或者 `0` ，或者存储在某个固定地址中的对象（创建在函数体内的对象使用栈空间，不是固定地址的；全局变量和静态变量的地址是固定的）。 `constexpr` 只对当前语句声明的指针有效，并不意味着指向的对象是const的，如果要指向const对象，需要：
```cpp
const int i = 5;    // 全局变量（满足constexpr的要求）
constexpr const int *p = &i;
```

## 处理类型

### 类型别名
有两种方式创建类型别名：
```cpp
// 使用关键字typedef
typedef double wages;       // wages是double的同义词
typedef wages base, *p;     // base是double的同义词，p是double*的同义词

// C++11引入了别名生命
using SI = Sales_item;      // SI是Sales_item的同义词
```

#### 指针、常量和类型别名
使用类型别名时，并不总是等效于把类型别名换成它所表示的类型后的结果。比如：
```cpp
typedef char *pstring;
const pstring cstr = 0; // 这里是顶层const
const pstring *ps;      // 这里是底层const
```
也就是说，这里的 `const` 是修饰 `pstring` 的，然而替换回原类型的话：
```cpp
const char *cstr = 0;
```
这条语句和上面的声明是不等价的。

### auto类型说明符
C++11引入了 `auto` 关键字，可以自动推断对象的类型，如：
```cpp
auto item = val1 + val2;    // item的类型就是val1和val2相加的结果的类型
```
一条语句也可以声明多个变量，要求基本类型一致，如：
```cpp
auto i = 0, *p = &i;        // 正确：i是整数，p是整型指针
auto sz = 0, pi = 3.14;     // 错误：基本类型不一致
```

#### 复合类型、常量和auto
有两种情况 `auto` 获得的类型和原类型不一样：
- 引用会被忽略
    ```cpp
    int i = 0, &r = i;
    auto a = r;             // a是一个整数

    const auto a1 = r;      // 显式指出a1是一个引用
    ```
- 顶层const会被忽略
    ```cpp
    const int ci = i;
    auto b = ci;            // b是一个整数

    const auto b1 = ci;     // 显式指出b1是一个顶层const
    ```

### decltype类型指示符
C++11引入了 `decltype` 关键字，可以仅获取表达式的类型，而不使用它的值初始化变量，如：
```cpp
decltype(f()) sum = x;      // sum的类型就是函数f的返回类型
```
这里 `f()` 并不会实际被调用。与 `auto` 不同， `decltype` 会保留顶层const和引用，这要求谨慎使用该关键字：
```cpp
int i = 42, *p = &i;
decltype(*p) c = i;         // c是引用
decltype((i)) d = i;        // d是引用
```

## 自定义数据结构

### 定义Sales_data类型

#### 类数据成员
C++11引入了类内初始值，可以为类的数据成员提供一个初始值，可以使用 `=` 或者 `{}` ，但不能使用 `()` 。

### 编写自己的头文件

#### 预处理器概述
为了避免反复包含同一个头文件，可以借助预处理器指令和**预处理变量**来形成**头文件保护符**：
```cpp
#ifndef SALES_DATA_H
#define SALES_DATA_H
// 头文件内容
#endif
```
预处理变量没有作用域规则，所以在整个程序中必须是唯一的。

<br><br>

# 字符串、向量和数组

## 命名空间的using声明
```cpp
using std::cin;
```

## 标准库类型string

### 定义和初始化string对象
有以下方式可以初始化一个string对象：
```cpp
string s1;              // 默认初始化，s1是一个空串
string s2(s1);          // s2是s1的副本
string s2 = s1;         // 等价于上一条
string s3("value");     // s3是字面值"value"的副本
string s3 = "value";    // 等价于上一条
string s4(10, 'c');     // s4的内容是cccccccccc
```
上面使用了等号的是**拷贝初始化**，其他的是**直接初始化**。

### string对象上的操作
```cpp
os << s;                // 将s写到输出流os中，返回os
is >> s;                // 从is中读取字符串赋给s，字符串以空白分隔，返回is
getline(is, s);         // 从is中读取一行赋给s，返回is
s.empty();              // s为空则返回true，否则返回false
s.size();               // 返回s中字符的个数
s[n];                   // 返回s中第n个字符的引用
s1 + s2;                // 返回s1和s2连接后的结果
s1 = s2;                // 用s2的副本代替s1中原来的字符
s1 == s2;               // 如果两个字符串相等则返回true
s1 != s2;               // 如果两个字符串不相等则返回true
s1 < s2;
s1 > s2;
s1 <= s2;
s1 >= s2;               // 字典序比较两个字符串
```

#### 读写string对象
```cpp
string word;
cin >> word;            // 忽略开头空白，遇到空白停止
```

#### string::size_type类型
是一个无符号类型，可以存放下任何string对象的大小。

#### 比较string对象
规则是：
- 逐位比较，遇到不同的字符，两个字符比较的结果就是string比较的结果
- 如果前面都相同，但是一个string先结束，那么短string小于长string

### 处理string对象中的字符
在头文件 `cctype` 中定义了一些函数：
| 函数 | 描述 |
| --- | --- |
| isalnum(c) | c是字母或数字则返回true |
| isalpha(c) | c是字母则返回true |
| iscntrl(c) | c是控制字符则返回true |
| isdigit(c) | c是数字则返回true |
| isgraph(c) | c是除空格之外的可打印字符则返回true |
| islower(c) | c是小写字母则返回true |
| isprint(c) | c是可打印字符则返回true |
| ispunct(c) | c是标点符号则返回true |
| isspace(c) | c是空白字符则返回true（空格、制表符、回车、换行、进纸符） |
| isupper(c) | c是大写字母则返回true |
| isxdigit(c) | c是十六进制数字则返回true |
| tolower(c) | 如果c是大写字母则返回其小写形式，否则返回c |
| toupper(c) | 如果c是小写字母则返回其大写形式，否则返回c |

## 标准库类型vector
在C++11以前，如果vector的元素类型仍是vector，两个尖括号之间需要加一个空格；在C++11以后，不需要加空格。

### 定义和初始化vector对象
有以下方式可以初始化一个vector对象：
```cpp
vector<T> v1;               // v1是一个空的vector，其中的元素类型是T，执行默认初始化
vector<T> v2(v1);           // v2中包含v1所有元素的副本
vector<T> v2 = v1;          // 等价于上一条
vector<T> v3(n, val);       // v3包含了n个重复的元素，每个元素的值都是val
vector<T> v4(n);            // v4包含了n个重复的元素，执行值初始化
vector<T> v5{a, b, c};      // v5包含了三个元素，分别是a、b、c
vector<T> v6 = {a, b, c};   // 等价于上一条
```
这里最后两种初始化方式是C++11引入的列表初始化。

#### 值初始化
指具有某种默认值的初始化方式，对于内置类型，值初始化就是初始化为0；对于类类型，值初始化就是默认初始化。

#### 列表初始值还是元素数量？
当使用花括号的时候，程序会优先进行列表初始化，如果不行，会考虑通过给出的参数调用构造函数，即实现和圆括号一样的效果。

### 其他vector操作
```cpp
vector<int> v;

v.empty();                  // 如果v为空则返回true，否则返回false
v.size();                   // 返回v中元素的个数
v.push_back(t);             // 在v的末尾添加一个值为t的元素
v[n];                       // 返回v中第n个元素的引用
v1 = v2;                    // 用v2的副本代替v1中原来的元素
v1 = {a, b, c};             // 用列表中的元素代替v1中原来的元素
v1 == v2;                   // 如果两个vector相等则返回true
v1 != v2;                   // 如果两个vector不相等则返回true
v1 < v2;
v1 > v2;
v1 <= v2;
v1 >= v2;                   // 字典序比较两个vector
```
这里的 `v.size()` 返回的类型是 `vector<int>::size_type` 。

## 迭代器介绍

### 使用迭代器

#### 迭代器运算符
```cpp
*iter;                      // 返回iter所指元素的引用
iter->mem;                  // 返回iter所指元素的名为mem的成员的引用
++iter;                     // 令iter指向容器中的下一个元素
--iter;                     // 令iter指向容器中的上一个元素
iter1 == iter2;             // 如果两个迭代器指向的元素相等则返回true
iter1 != iter2;             // 如果两个迭代器指向的元素不相等则返回true
```

#### 迭代器类型
以一个 `vector<int>` 为例，它有两种迭代器类型：
- `vector<int>::iterator` ：可以读写元素
- `vector<int>::const_iterator` ：只能读元素

而 `begin()` 和 `end()` 返回的是 `vector<int>::iterator` 类型的迭代器。C++11引入了两个新的函数成员 `cbegin()` 和 `cend()` ，它们返回 `vector<int>::const_iterator` 类型的迭代器。

#### 结合解引用和成员访问操作
语句 `(*it).empty()` 中的圆括号是必须的，因为成员访问运算符 `.` 的优先级高于解引用运算符 `*` 。为了避开这个问题，可以使用箭头运算符 `->` ，即 `it->empty()` 。

### 迭代器运算
所有的迭代器都支持递增运算，而string和vector的迭代器则支持更多的运算（以下的前后似乎是从容器的头部指向容器的尾部来说的）：
| 运算 | 描述 |
| --- | --- |
| iter + n | 返回iter向前移动n个元素的迭代器，返回指向某元素的迭代器或者尾后迭代器 |
| iter - n | 返回iter向后移动n个元素的迭代器，返回指向某元素的迭代器或者尾后迭代器 |
| iter += n | iter = iter + n |
| iter -= n | iter = iter - n |
| iter1 - iter2 | 返回iter1和iter2之间的距离 |
| >、>=、<、<= | 两个迭代器位置的比较 |

其中的减法运算返回的是 `vector<int>::difference_type` 类型的值，它是一个带符号类型。

## 数组
数组比起vector或许性能好上一些。

### 指针和数组
数组名常常被转换成指针，以至于当使用 `auto` 关键字的时候，数组名会被推断为指针类型，而不是数组类型。然而 `decltype` 保留了数组类型以及数组的大小。

#### 标准库函数begin和end
在C++中，指针也被视作一种迭代器，并可以完成迭代器的基本操作。如果用指针遍历内置数组，需要获得首元素指针和尾后指针，可以通过计算获得，然而这样比较麻烦。C++11引入了两个新的标准库函数 `begin` 和 `end` ，可以返回指向数组首元素和尾后元素的指针：
```cpp
int ia[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
int *beg = begin(ia);
int *last = end(ia);
```

#### 指针运算
类似于vector的迭代器，指针也可以进行加减运算来一次性经过多个元素。指向同一个数组的两个指针可以相减，得到的结果是相距的元素个数，类型为定义在 `cstddef` 头文件中机器相关的带符号类型 `ptrdiff_t` 。

#### 下标和指针
相比于标准库容器的下标运算只能使用无符号索引，内置数组的下标甚至可以使用负数。

### C风格字符串

#### C标准库String函数
头文件 `cstring` 中定义了一些函数：
| 函数 | 描述 |
| --- | --- |
| strlen(s) | 返回s的长度，不包括空字符 |
| strcmp(s1, s2) | 比较s1和s2，如果s1小于s2则返回负数，如果s1大于s2则返回正数，如果s1等于s2则返回0 |
| strcat(s1, s2) | 将s2连接到s1的后面，返回s1 |
| strcpy(s1, s2) | 将s2复制给s1，返回s1 |

以上函数均不验证其参数的合法性，所以使用时要特别小心。

### 与旧代码的接口
如果要把string对象转换成C风格字符串，可以使用 `c_str` 函数，注意使用const指针：
```cpp
string s("Hello World!");
const char *str = s.c_str();
```
`c_str` 返回的指针不是一直有效的，所以最好得到指针后拷贝一个副本来使用。

#### 使用数组初始化vector对象
可以使用数组初始化vector对象，如：
```cpp
int int_arr[] = {0, 1, 2, 3, 4, 5};
vector<int> ivec(begin(int_arr), end(int_arr));
```

### 多维数组

#### 多维数组的初始化
```cpp
// 显式初始化前三行所有元素
int ia[3][4] = {
    {0, 1, 2, 3},
    {4, 5, 6, 7},
    {8, 9, 10, 11}
};

// 这种情况下，可以不嵌套花括号
int ia[3][4] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11};

// 每行只初始化首元素（内层的花括号是必须的）
int ia[3][4] = {
    {0},
    {4},
    {8}
};

// 不等价于上一条，只初始化第一行的前三个元素
int ia[3][4] = {0, 4, 8};
```

#### 使用范围for语句处理多维数组
C++11引入了范围for语句，可以用来遍历数组：
```cpp
size_t cnt = 0;
for (auto &row : ia) {
    for (auto &col : row) {
        col = cnt;
        ++cnt;
    }
}
```
外层的指针必须是引用类型，否则会导致其退化成指针，从而无法继续遍历。

#### 类型别名简化多维数组的指针
多维数组和指针交互时，类型会很复杂，可以使用类型别名来简化，不过在此 `using` 和 `typedef` 的使用有一些区别：
```cpp
// 两条语句等价
using int_array = int[4];
typedef int int_array[4];
```