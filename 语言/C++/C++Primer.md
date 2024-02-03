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