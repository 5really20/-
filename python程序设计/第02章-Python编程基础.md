# 第2章 Python编程基础

> 对应教材：王雷春《Python 程序设计——从基础入门到实战应用（微课版）（第2版）》第2章｜建议学时：4（讲授）+ 6（实验）｜重要度：★★★★☆

本章是整门课的**语法地基**：数据类型、变量本质、运算符、内置函数。考试中第 2 章通常占 15～20 分，且**「变量是引用」「可变对象 vs 不可变对象」「浮点精度」「`is` 与 `==`」这四个点会在后面每一章反复出现**。学不好这一章，第 4 章（组合数据）和第 5 章（函数）一定会崩。

## 本章知识地图

- 2.1 数据类型
  - 动态类型、`type()` / `isinstance()`
  - **可变对象 vs 不可变对象**
- 2.2 数字类型
  - 整型（任意精度）、浮点（IEEE 754）、复数、布尔
  - 类型转换、`Fraction` / `Decimal`
  - `math` 库常用函数
- 2.3 字符串类型
  - 创建、索引、切片、运算
  - 字符串方法速查、转义字符
  - **三种格式化方式对比：`%` / `str.format()` / f-string**
- 2.4 常量和变量
  - 常量约定、变量是「名字 → 对象」的绑定
  - `id()` / `is` / `==`、整数缓存
  - 赋值、多重赋值、`del`
- 2.5 运算符和表达式
  - 算术、比较、逻辑、位、成员、身份运算符
  - 优先级、短路求值、`+=` 的陷阱
- 2.6 内置函数
  - 常用内置函数表、特殊内置函数 `eval` / `exec`
- 2.7 程序调试
  - 语法错误、运行时错误、逻辑错误；常见异常表；PyCharm 断点调试
- 2.8 典型案例
  - 复杂算术表达式、几何体面积体积、一元二次方程、验证码验证

## 学习目标

- 能判断任意一个字面量或表达式的类型，并区分 `type()` 与 `isinstance()` 的适用场景。
- 能解释 `0.1 + 0.2 != 0.3` 的原因，并用 `round()` / `math.isclose()` / `Decimal` 解决。
- 能熟练对字符串做索引、切片、反转、格式化，能说出三种格式化方式的区别与推荐用法。
- 能画图说明「变量是贴在对象上的标签」，并解释 `a = [1,2]; b = a` 之后修改 `b` 为什么影响 `a`。
- 能说出 `is` 与 `==` 的本质区别，并知道小整数缓存的范围。
- 能写出完整准确的运算符优先级顺序，能判断 `-7 // 2`、`7 % -2` 这类结果。
- 能看懂 PyCharm 报出的异常类型，并说出它属于哪一类错误。

## 2.1 数据类型

### 为什么 Python 不用声明类型

C 语言里 `int a = 5;` 必须写类型；Python 里 `a = 5` 就够了。原因是**类型信息不挂在变量上，而挂在对象上**。

```python
a = 5
print(type(a))      # <class 'int'>
a = "hello"         # 同一个变量名，可以随时改指另一种类型
print(type(a))      # <class 'str'>
```

**「变量无类型，对象有类型」是理解 Python 的第一把钥匙。** 变量只是一个名字（标签），赋值就是把标签贴到某个对象上。

### type() 与 isinstance()

| 函数 | 作用 | 返回 | 适用场景 |
|---|---|---|---|
| `type(x)` | 返回对象的**确切**类型 | 类型对象 | 需要精确判断类型 |
| `isinstance(x, T)` | 判断 `x` 是否是 `T` 或其子类的实例 | `bool` | **推荐**，兼容继承 |

```python
print(type(3))                      # <class 'int'>
print(type(3) == int)               # True
print(isinstance(3, int))           # True
print(isinstance(True, int))        # True  ← bool 是 int 的子类
print(type(True) == int)            # False ← 精确比较就露馅了
print(isinstance("abc", (int, str)))  # True  第二个参数可以是元组
```

> **为什么推荐 `isinstance()`？** 因为它支持继承关系。`True` 在数学上就是 1（`True + True` 得 `2`），`bool` 是 `int` 的子类，所以 `isinstance(True, int)` 为真而 `type(True) == int` 为假。写通用代码时前者更安全。

### 可变对象 vs 不可变对象

**这是 Python 最核心的分类，没有之一。**

| 类别 | 类型 | 改了会怎样 |
|---|---|---|
| **不可变**（immutable） | `int`、`float`、`complex`、`bool`、`str`、`tuple`、`frozenset` | 任何「修改」其实是**新建一个对象** |
| **可变**（mutable） | `list`、`dict`、`set`、自定义对象 | **原地修改**，所有人看到的都变 |

```python
# 不可变：看起来像改了，其实换了对象
s = "abc"
print(id(s))        # 2798...   （每次运行不同）
s = s + "d"
print(id(s))        # 另一个地址，说明是新对象

# 可变：原地改，地址不变
lst = [1, 2, 3]
print(id(lst))      # 2980...
lst.append(4)
print(id(lst))      # 2980...  地址完全一样
print(lst)          # [1, 2, 3, 4]
```

**为什么字符串没有 `append`？** 因为它不可变，只能重新拼一个新字符串。这解释了两个常见现象：

```python
s = "hello"
s[0] = "H"          # TypeError: 'str' object does not support item assignment

# 在循环里拼接字符串很慢，因为每次都在造新对象
result = ""
for i in range(10000):
    result += str(i)        # 每次都新建对象，O(n²)
# 正确做法：先收集到列表，最后一次性 join
result = "".join(str(i) for i in range(10000))    # O(n)
```

## 2.2 数字类型

Python 的数字类型只有四种：**整型 `int`、浮点型 `float`、复数型 `complex`、布尔型 `bool`**。

### 整型 int

**Python 的 `int` 是任意精度的**，只要内存够，多大都不溢出。这一点和 C 的 `int`（32 位，约 ±21 亿就溢出）完全不同，是考试常考的比较点。

```python
print(2 ** 10)          # 1024
print(2 ** 100)         # 1267650600228229401496703205376
print(2 ** 1000 % 1000) # 376      ← 1000 位的整数依然精确
print(10 ** 100 + 1)    # 1000...0001
```

整型字面量的几种写法：

```python
print(100)           # 100    十进制
print(0b1010)        # 10     二进制（0b 开头）
print(0o12)          # 10     八进制（0o 开头）
print(0xff)          # 255    十六进制（0x 开头）
print(1_000_000)     # 1000000  下划线只是给人看的，可读性写法
print(int("1010", 2))   # 10   按二进制解析字符串
print(int("ff", 16))    # 255  按十六进制解析
print(bin(10), oct(10), hex(255))   # 0b1010 0o12 0xff
```

### 浮点类型 float

**Python 的 `float` 就是 C 的 `double`：64 位 IEEE 754 双精度**，1 位符号 + 11 位阶码 + 52 位尾数，有效数字约 **15～17 位十进制**。

```python
import sys
print(sys.float_info.max)       # 1.7976931348623157e+308
print(sys.float_info.min)       # 2.2250738585072014e-308
print(sys.float_info.epsilon)   # 2.220446049250313e-16
print(0.1 + 0.2)                # 0.30000000000000004   ← 经典精度问题
print(0.1 + 0.2 == 0.3)         # False
print(1.1 + 2.2)                # 3.3000000000000003
print(0.1 * 3)                  # 0.30000000000000004
```

> **为什么 `0.1 + 0.2 != 0.3`？** 因为十进制小数 0.1、0.2 在二进制里是**无限循环小数**（就像 1/3 在十进制里是 0.3333…），双精度只能存 53 位有效位，存进去就已经有微小误差，相加后误差被放大到第 17 位。**这不是 Python 的 bug，任何用 IEEE 754 的语言（C、Java、JavaScript）都一样**：在 C 里 `printf("%.17f", 0.1+0.2)` 同样得到 `0.30000000000000004`。

浮点数的正确比较方式：

```python
import math

print(abs((0.1 + 0.2) - 0.3) < 1e-9)          # True  自己写容差
print(math.isclose(0.1 + 0.2, 0.3))           # True  推荐，默认相对容差 1e-9
print(round(0.1 + 0.2, 2))                    # 0.3   四舍五入到 2 位
```

> **坑**：`round(0.1 + 0.2, 2)` 得到 `0.3` 只适合显示，**参与后续运算仍可能带误差**。金融计算请用 `decimal.Decimal`。

科学计数法与其他写法：

```python
print(1.5e3)        # 1500.0
print(1.5e-3)       # 0.0015
print(3.)           # 3.0
print(.5)           # 0.5
print(float("inf")) # inf
print(float("nan")) # nan
print(1e16 + 1)     # 1e+16  ← 精度不够，加 1 没反应
print((1.0).is_integer())   # True
```

### 复数类型 complex

```python
z = 3 + 4j
print(type(z))      # <class 'complex'>
print(z.real)       # 3.0   实部是 float
print(z.imag)       # 4.0   虚部是 float
print(abs(z))       # 5.0   模长 = √(3²+4²)
print(z * 2)        # (6+8j)
print(complex(1, 2))    # (1+2j)
```

**注意虚部单位是 `j` 不是 `i`**（这是电气工程的惯例）。实部和虚部都是 `float`，即使写成 `3 + 4j` 也是 `3.0`。

### 布尔类型 bool

```python
print(True + True)          # 2      ← True 就是 1
print(True * 5)             # 5
print(True == 1)            # True
print(False == 0)           # True
print(True is 1)            # False  ← 身份不同
print(sum([True, True, False]))     # 2   统计 True 的个数
```

**哪些值被判为 `False`？** 记住这张表，考试必考：

| 值为 False | 值为 True |
|---|---|
| `False`、`None` | 任何非零数字 |
| `0`、`0.0`、`0j` | `'0'`（非空字符串） |
| `""`（空字符串） | `' '`（空格） |
| `[]`、`()`、`{}`、`set()` | `[0]`、`[[]]` |
| `range(0)` | `-1` |

```python
print(bool(""))         # False
print(bool("0"))        # True   ← 坑！非空字符串永远为 True
print(bool([]))         # False
print(bool([0]))        # True
print(bool(-1))         # True
print(bool("False"))    # True   ← 坑！字符串 "False" 也是 True
```

### 数字类型转换

| 函数 | 作用 | 例子 | 结果 |
|---|---|---|---|
| `int(x)` | 转整数，**截断**（向零取整） | `int(3.9)` / `int(-3.9)` | `3` / `-3` |
| `float(x)` | 转浮点 | `float("3.14")` | `3.14` |
| `complex(x)` | 转复数 | `complex(2)` | `(2+0j)` |
| `bool(x)` | 转布尔 | `bool(0.0)` | `False` |
| `str(x)` | 转字符串 | `str(3.14)` | `'3.14'` |
| `int(s, base)` | 按进制解析字符串 | `int("11", 2)` | `3` |

```python
print(int(3.9), int(-3.9))      # 3 -3     截断而非四舍五入
print(round(3.9), round(-3.9))  # 4 -4     这才是四舍五入
print(float("1e3"))             # 1000.0
print(int("3.9"))               # ValueError! 不能直接把小数串转 int
print(int(float("3.9")))        # 3        要分两步
print(str(3.0) + "x")           # 3.0x
```

**`round()` 用的是「银行家舍入」（四舍六入五取偶）**，不是小学的四舍五入：

```python
print(round(0.5), round(1.5), round(2.5), round(3.5))   # 0 2 2 4
print(round(2.675, 2))     # 2.67  ← 坑！不是 2.68
```

> **为什么 `round(2.675, 2)` 得 2.67？** 因为 2.675 在二进制里实际存的是 `2.67499999999999982236431605997495353221893310546875`，本来就不到 2.675，舍入自然向下。想得到 2.68 请用 `Decimal("2.675").quantize(...)`。

### 分数和高精度实数

浮点有误差时，用 `fractions.Fraction`（分数）和 `decimal.Decimal`（十进制高精度）：

```python
from fractions import Fraction

print(Fraction(1, 3) + Fraction(1, 6))      # 1/2
print(Fraction(1, 3) + Fraction(1, 6) == Fraction(1, 2))    # True
print(Fraction("0.1") + Fraction("0.2"))    # 3/10
print(float(Fraction(1, 3)))                # 0.3333333333333333

from decimal import Decimal

print(Decimal("0.1") + Decimal("0.2"))      # 0.3     ← 精确
print(Decimal("0.1") + Decimal("0.2") == Decimal("0.3"))    # True
print(Decimal(0.1))     # 0.1000000000000000055511151231257827021181583404541015625
```

> **关键**：**`Decimal` 必须用字符串构造**。`Decimal(0.1)` 会把 float 的误差原封不动带进来。

### 常用数学函数

| 函数 | 作用 | 例子 | 结果 |
|---|---|---|---|
| `abs(x)` | 绝对值（内置，不用 import） | `abs(-3.5)` | `3.5` |
| `math.sqrt(x)` | 平方根 | `math.sqrt(16)` | `4.0` |
| `math.ceil(x)` | 向上取整 | `math.ceil(1.1)` | `2` |
| `math.floor(x)` | 向下取整 | `math.floor(1.9)` | `1` |
| `math.pow(a,b)` | 幂（返回 float） | `math.pow(2,10)` | `1024.0` |
| `math.fabs(x)` | 绝对值（返回 float） | `math.fabs(-3)` | `3.0` |
| `math.gcd(a,b)` | 最大公约数 | `math.gcd(12,18)` | `6` |
| `math.factorial(n)` | 阶乘 | `math.factorial(5)` | `120` |
| `math.pi` / `math.e` | 圆周率 / 自然常数 | `math.pi` | `3.141592653589793` |
| `math.trunc(x)` | 截断取整 | `math.trunc(-3.9)` | `-3` |

```python
import math

print(math.pi, math.e)              # 3.141592653589793 2.718281828459045
print(math.sqrt(2))                 # 1.4142135623730951
print(math.ceil(1.1), math.floor(1.9))      # 2 1
print(math.ceil(-1.1), math.floor(-1.9))    # -1 -2  ← 方向不同，坑
print(math.gcd(12, 18), math.factorial(5))  # 6 120
print(divmod(7, 2), divmod(-7, 2))          # (3, 1) (-4, 1)
```

## 2.3 字符串类型

### 字符串及创建

```python
s1 = 'hello'
s2 = "hello"
s3 = '''第一行
第二行'''            # 三引号可跨行
s4 = """也可以"""
s5 = str(123)       # 从其他类型转换
print(s1, s2, s3, s5)   # hello hello 第一行\n第二行 123
print(len(s1))      # 5
```

### 字符串访问

**字符串是字符序列，支持索引和切片，索引从 0 开始，且支持负数索引（-1 是最后一个）。**

```
索引：   0   1   2   3   4   5
字符：   P   y   t   h   o   n
负索引：-6  -5  -4  -3  -2  -1
```

```python
s = "Python"
print(s[0], s[5])       # P n
print(s[-1], s[-6])     # n P
print(s[1:4])           # yth    ← 左闭右开，含 1 不含 4
print(s[:3])            # Pyt
print(s[3:])            # hon
print(s[:])             # Python（整串切片，不是副本，见下方提醒）
print(s[::2])           # Pto    步长 2
print(s[::-1])          # nohtyP 反转！最常用技巧
print(s[-3:])           # hon
print(s[10])            # IndexError: string index out of range
print(s[10:20])         # 空字符串（切片越界不报错，坑）
```

> **切片三大特点**：① 左闭右开；② 越界**不报错**（返回空串或截断）；③ 切片一般产生**新对象**，但**不可变类型做「整串切片」时 CPython 会直接返回原对象**——实测 `"Python"[:] is "Python"` 为 **True**，`(1,2,3)[:] is (1,2,3)` 也是 **True**，而列表 `[1,2,3][:] is [1,2,3]` 是 **False**（列表切片才真正复制）。
>
> ⚠️ **所以 `s[:]` 不能当字符串的「拷贝」用**——反正字符串不可变，本来也不需要拷贝。真正要复制的是列表：`new = old[:]` 或 `old.copy()`（浅拷贝，见 4.2.3）。

### 字符串运算

| 运算 | 含义 | 例子 | 结果 |
|---|---|---|---|
| `+` | 拼接 | `"ab" + "cd"` | `'abcd'` |
| `*` | 重复 | `"ab" * 3` | `'ababab'` |
| `in` | 成员判断 | `"th" in "Python"` | `True` |
| `==` | 逐字符比较 | `"abc" == "abc"` | `True` |
| `>` `<` | 按 Unicode 码比较 | `"a" > "A"` | `True` |

```python
print("ab" * 3)             # ababab
print("-" * 20)             # --------------------
print("th" in "Python")     # True
print("A" < "a")            # True    A=65, a=97
print("10" < "9")           # True    ← 坑！字符串按位比较，'1'<'9'
print(ord("A"), chr(65))    # 65 A
print(ord("中"), chr(20013))    # 20013 中
```

### 字符串函数（方法）

**调用方式 `字符串.方法(参数)`，方法不会改变原字符串（因为不可变），而是返回新字符串。**

| 方法 | 作用 | 例子 | 结果 |
|---|---|---|---|
| `upper()` / `lower()` | 全部大写 / 小写 | `"aB".upper()` | `'AB'` |
| `strip()` | 去掉两端空白 | `"  hi  ".strip()` | `'hi'` |
| `split(sep)` | 按分隔符切分成**列表** | `"a,b,c".split(",")` | `['a','b','c']` |
| `join(iter)` | 用自身连接序列 | `"-".join(['a','b'])` | `'a-b'` |
| `replace(a,b)` | 替换 | `"aaa".replace("a","b",2)` | `'bba'` |
| `find(sub)` | 找子串，**找不到返回 -1** | `"hello".find("l")` | `2` |
| `index(sub)` | 找子串，**找不到抛异常** | `"hello".index("z")` | `ValueError` |
| `count(sub)` | 统计出现次数 | `"hello".count("l")` | `2` |
| `startswith(p)` / `endswith(p)` | 前缀 / 后缀判断 | `"a.py".endswith(".py")` | `True` |
| `isdigit()` / `isalpha()` / `isalnum()` | 是否纯数字 / 字母 / 字母数字 | `"12".isdigit()` | `True` |
| `center(w, c)` / `ljust` / `rjust` | 居中 / 左右对齐填充 | `"abc".center(7,"*")` | `'**abc**'` |
| `zfill(w)` | 左侧补 0 | `"42".zfill(5)` | `'00042'` |
| `title()` / `capitalize()` | 每词首字母大写 / 首字母大写 | `"hi there".title()` | `'Hi There'` |
| `swapcase()` | 大小写互换 | `"aB".swapcase()` | `'Ab'` |

```python
print("  Hello World  ".strip())            # Hello World
print("a,b,,c".split(","))                  # ['a', 'b', '', 'c']
print("a  b   c".split())                   # ['a', 'b', 'c']  无参数按任意空白切
print("-".join(["2025", "01", "01"]))       # 2025-01-01
print("a-b-c".replace("-", "+"))            # a+b+c
print("hello".find("z"), "hello".count("l"))    # -1 2
print("abc123".isalnum(), "abc".isalpha())  # True True
print("42".zfill(5), "abc".center(7, "*"))  # 00042 **abc**
```

### 转义字符

| 转义 | 含义 | 转义 | 含义 |
|---|---|---|---|
| `\n` | 换行 | `\\` | 一个反斜杠 |
| `\t` | 制表符（Tab） | `\'` | 单引号 |
| `\r` | 回车 | `\"` | 双引号 |
| `\0` | 空字符 | `\b` | 退格 |

```python
print("a\tb")           # a	b
print("a\nb")           # a（换行）b
print("c:\\path\\new")  # c:\path\new
print(r"c:\path\new")   # c:\path\new   ← r 前缀：原始字符串，不转义
print("他说：\"你好\"")   # 他说："你好"
print("第一行", end="\n\n")
```

> **坑**：Windows 路径 `"C:\new\test"` 里的 `\n` 会被当成换行。写路径要么用 `r"C:\new\test"`，要么用正斜杠 `"C:/new/test"`。

### 字符串格式化（三种方式对比）

| 方式 | 写法 | 优点 | 缺点 | 推荐度 |
|---|---|---|---|---|
| `%` 格式化 | `"%s %d" % (a, b)` | 古老、C 程序员熟悉 | 参数多易错，类型要匹配 | ★★ |
| `str.format()` | `"{} {}".format(a, b)` | 功能全，支持索引与命名 | 冗长 | ★★★ |
| **f-string** | `f"{a} {b}"` | **最简洁、最快、可嵌入表达式** | 需 Python 3.6+ | **★★★★★** |

```python
name, score, pi = "张三", 92.5, 3.14159

# 1) % 格式化
print("%s 的成绩是 %.1f" % (name, score))        # 张三 的成绩是 92.5
print("%d%%" % 85)                              # 85%
print("%5.2f|%-5d|" % (3.14159, 42))            #  3.14|42   |

# 2) str.format()
print("{} 的成绩是 {:.1f}".format(name, score))   # 张三 的成绩是 92.5
print("{1} 和 {0}".format("A", "B"))             # B 和 A
print("{n} 得了 {s:.0f} 分".format(n=name, s=score))   # 张三 得了 92 分

# 3) f-string（推荐）
print(f"{name} 的成绩是 {score:.1f}")             # 张三 的成绩是 92.5
print(f"{3.14159:.2f}")                          # 3.14
print(f"{2 ** 10}")                              # 1024    可放表达式
print(f"{name=}")                                # name='张三'   ← 调试神器
```

f-string 的格式说明符强大，做题常用：

```python
print(f"{1234567:,}")       # 1,234,567     千分位
print(f"{0.256:.1%}")       # 25.6%         百分比
print(f"{255:08b}")         # 11111111      二进制补零到 8 位
print(f"{255:x}")           # ff            十六进制
print(f"{'hi':>10}|")       #         hi|   右对齐宽 10
print(f"{'hi':*<10}|")      # hi********|   左对齐用 * 填充
print(f"{3.14159:10.3f}|")  #      3.142|   宽 10 保留 3 位
```

## 2.4 常量和变量

### 常量

**Python 没有语法层面的常量**，只有约定：**全大写名字表示「不要改我」**。

```python
PI = 3.14159
MAX_SIZE = 100
```

想强制不可变，用 `typing.Final` 或 `enum`，但考试只考约定。

### 变量

**变量的本质是「名字 → 对象」的引用（标签）**，而不是「装值的盒子」。这是与 C 最根本的差异。

```
C：   a ┌───┐          Python：  a ──┐
        │ 5 │                        ▼
        └───┘                     ┌─────┐
                                  │  5  │  (int 对象)
                                  └─────┘
```

```python
a = 5           # 把名字 a 绑到对象 5 上
b = a           # b 也绑到同一个对象
print(a is b)   # True
a = a + 1       # 新建对象 6，把 a 绑过去；b 还在 5 上
print(a, b)     # 6 5
```

可变对象的赋值坑：

```python
x = [1, 2, 3]
y = x               # y 和 x 指向同一个列表！
y.append(4)
print(x)            # [1, 2, 3, 4]  ← 改 y 动了 x
print(x is y)       # True

# 要真正的副本，必须显式复制
y = x.copy()        # 或 y = x[:]  或 y = list(x)
y.append(5)
print(x, y)         # [1, 2, 3, 4] [1, 2, 3, 4, 5]
```

### id、is 与 ==

| 运算符/函数 | 比较什么 | 例子 |
|---|---|---|
| `==` | **值**是否相等（调用 `__eq__`） | `[1,2] == [1,2]` → `True` |
| `is` | 是否为**同一个对象**（内存地址） | `[1,2] is [1,2]` → `False` |
| `id(x)` | 对象的身份标识（CPython 里就是内存地址） | `id(a) == id(b)` |

```python
a = [1, 2]
b = [1, 2]
print(a == b)       # True   值相同
print(a is b)       # False  两个不同对象
print(id(a) == id(b))   # False

c = a
print(a is c)       # True

# 唯一必须用 is 的场景：判断 None
x = None
print(x is None)    # True   推荐
print(x == None)    # True   能跑但不推荐
```

> **为什么 `is None` 比 `== None` 好？** ① `None` 是单例，`is` 是常数时间；② 自定义类可能重写 `__eq__` 让 `x == None` 返回意外的结果；③ PEP 8 明确推荐 `is`。

### 整数缓存（小整数池）

**CPython 预先创建并缓存了 -5 到 256 的整数对象**，所以这个范围内相同值的整数是**同一个对象**。

```python
a = 256
b = 256
print(a is b)       # True    在缓存范围内

a = 257
b = 257
print(a is b)       # True    ← 见下面的解释
```

> **坑（重要）**：上面 257 的例子在同一段代码里可能仍然得到 `True`，因为**编译器会把同一个代码块里的相同常量合并成一个对象**。如果改成在交互式窗口里分两行输入，`a is b` 就是 `False`：
>
> ```
> >>> a = 257
> >>> b = 257
> >>> a is b
> False
> ```
>
> **结论：整数缓存是 CPython 的实现细节，不要依赖它。比较数值永远用 `==`，`is` 只用于 `None`、`True`、`False` 和自定义单例。**

### 多重赋值与交换

```python
a, b = 1, 2             # 同时赋值
a, b = b, a             # 交换！右边先打包成元组 (2,1)，再解包
print(a, b)             # 2 1

x = y = z = 0           # 链式赋值，三个名字指向同一个 0
print(x, y, z)          # 0 0 0

# 解包（序列解包）
first, *rest = [1, 2, 3, 4]
print(first, rest)      # 1 [2, 3, 4]

p = (10, 20)
m, n = p
print(m, n)             # 10 20

a, b = 1, 2
a, b = b, a + b
print(a, b)             # 2 3
```

### 变量删除

```python
x = 10
del x
print(x)        # NameError: name 'x' is not defined
```

`del` 删除的是**名字绑定**，不是对象本身；对象在没有任何名字引用它时才被垃圾回收。

## 2.5 运算符和表达式

### 常用运算符

| 类别 | 运算符 | 说明 |
|---|---|---|
| 算术 | `+ - * / // % **` | **`/` 结果一定是 float；`//` 是整除（向下取整）** |
| 比较 | `== != > < >= <=` | 返回 `bool`，支持链式 `1 < x < 10` |
| 逻辑 | `and or not` | 短路求值，返回的是**操作数本身** |
| 赋值 | `= += -= *= /= //= %= **=` | `+=` 对列表是**就地修改** |
| 位运算 | `& \| ^ ~ << >>` | 按二进制位操作 |
| 成员 | `in` `not in` | 判断元素是否在序列/集合/字典键中 |
| 身份 | `is` `is not` | 判断是否为同一对象 |

```python
# 除法：/ 与 // 的区别（高频考点）
print(7 / 2)        # 3.5    真除法，结果是 float
print(7 // 2)       # 3      整除，向下取整
print(-7 // 2)      # -4     ← 坑！不是 -3，是向下（往更小方向）取整
print(7 // -2)      # -4
print(7 % 2, -7 % 2, 7 % -2)    # 1 1 -1   ← 余数符号跟除数走
print(int(-7 / 2))  # -3     ← 这才是「向零截断」
print(divmod(7, 2))     # (3, 1)   同时拿到商和余
print(2 ** 3 ** 2)  # 512    ** 右结合，等于 2**(3**2)
print(10 / 2)       # 5.0    即使是整除，/ 也给 float
```

```python
# 链式比较
x = 5
print(1 < x < 10)       # True   等价 1 < x and x < 10
print(1 < x > 3)        # True

# 逻辑运算返回操作数本身（不是 True/False）
print(0 or "默认值")     # 默认值   ← 常用于设默认值
print("" or [])         # []
print(1 and 2)          # 2
print(None or 0 or "")  # （空字符串）
print(not 0, not "a")   # True False
```

```python
# 位运算
print(6 & 3)        # 2    110 & 011 = 010
print(6 | 3)        # 7    110 | 011 = 111
print(6 ^ 3)        # 5    异或
print(~6)           # -7   按位取反，等于 -(x+1)
print(1 << 3)       # 8    左移 = 乘 2³
print(16 >> 2)      # 4    右移 = 整除 2²
```

### 运算符优先级

**从高到低**（只需记住前几档）：

| 优先级 | 运算符 |
|---|---|
| 1（最高） | `()` 括号 |
| 2 | `**` 幂 |
| 3 | `+x` `-x` `~x`（一元） |
| 4 | `*` `/` `//` `%` |
| 5 | `+` `-` |
| 6 | `<<` `>>` |
| 7 | `&` |
| 8 | `^` |
| 9 | `\|` |
| 10 | 比较 `== != < > <= >=` `in` `is` |
| 11 | `not` |
| 12 | `and` |
| 13（最低） | `or` |

```python
print(2 + 3 * 4)        # 14   先乘后加
print((2 + 3) * 4)      # 20
print(2 ** 3 ** 2)      # 512  右结合
print(-2 ** 2)          # -4   ← 坑！一元负号优先级低于 **
print((-2) ** 2)        # 4
print(not 1 == 2)       # True 等价 not (1 == 2)
print(1 or 0 and 0)     # 1    and 优先于 or
```

> **建议**：**永远用括号把意图写清楚**。优先级表只在做选择题时需要背。

### 补充说明：`+=` 对可变对象是就地修改

```python
a = [1, 2]
b = a
a += [3]            # 就地修改，等价 a.extend([3])
print(a, b)         # [1, 2, 3] [1, 2, 3]   ← b 也变了！

a = [1, 2]
b = a
a = a + [3]         # 新建对象再绑定，不改原对象
print(a, b)         # [1, 2, 3] [1, 2]      ← b 没变
```

**`+=`（`__iadd__`）对列表是原地修改，`+`（`__add__`）是新建对象。** 字符串没有 `__iadd__`，所以 `s += "x"` 一定是新建对象。

## 2.6 内置函数

### 内置函数简介

内置函数**不用 import 就能直接用**，共 60 多个。考试最常用的：

| 函数 | 作用 | 例子 | 结果 |
|---|---|---|---|
| `abs(x)` | 绝对值 | `abs(-3)` | `3` |
| `max()` / `min()` | 最大 / 最小 | `max([1,5,3])` | `5` |
| `sum(iter)` | 求和 | `sum([1,2,3])` | `6` |
| `len(x)` | 长度 | `len("abc")` | `3` |
| `pow(a,b)` | 幂 | `pow(2,10)` | `1024` |
| `round(x,n)` | 四舍五入 | `round(3.14159,2)` | `3.14` |
| `divmod(a,b)` | 商和余 | `divmod(7,2)` | `(3, 1)` |
| `sorted(iter)` | 排序（返回新列表） | `sorted([3,1,2])` | `[1, 2, 3]` |
| `reversed(seq)` | 反转迭代器 | `list(reversed("abc"))` | `['c','b','a']` |
| `enumerate(iter)` | 下标+元素 | `list(enumerate("ab"))` | `[(0,'a'), (1,'b')]` |
| `zip(a,b)` | 配对 | `list(zip([1,2],"ab"))` | `[(1,'a'), (2,'b')]` |
| `map(f, iter)` | 逐元素映射 | `list(map(int, "12"))` | `[1, 2]` |
| `filter(f, iter)` | 过滤 | `list(filter(None,[0,1,2]))` | `[1, 2]` |
| `all()` / `any()` | 全部为真 / 任一为真 | `all([1,1])` / `any([0,1])` | `True` / `True` |
| `chr()` / `ord()` | 码 → 字符 / 字符 → 码 | `chr(65)` / `ord('A')` | `'A'` / `65` |
| `bin/oct/hex` | 转二/八/十六进制串 | `hex(255)` | `'0xff'` |
| `type()` / `isinstance()` | 类型判断 | `type(1)` | `<class 'int'>` |
| `id()` | 对象标识 | `id(x)` | 地址整数 |
| `input()` / `print()` | 输入 / 输出 | —— | —— |
| `help()` / `dir()` | 帮助 / 列出属性 | `dir([])` | 方法名列表 |

```python
print(max(1, 5, 3), min([1, 5, 3]))         # 5 1
print(max("hello"))                         # o    字符串按字符比较
print(sum([1, 2, 3]), sum([1,2,3], 10))     # 6 16   第二个参数是起始值
print(max(["apple", "b", "cat"], key=len))  # apple  用 key 指定比较依据
print(sorted([3, 1, 2], reverse=True))      # [3, 2, 1]
print(sorted(["bb", "a", "ccc"], key=len))  # ['a', 'bb', 'ccc']
print(list(enumerate("ab", start=1)))       # [(1, 'a'), (2, 'b')]
print(list(zip([1, 2, 3], "ab")))           # [(1, 'a'), (2, 'b')]  以短的为准
```

### 特殊内置函数

**`eval()`：把字符串当表达式求值并返回结果。**

```python
print(eval("1 + 2 * 3"))            # 7
print(eval("[1, 2, 3]"))            # [1, 2, 3]   能还原字面量
x = 10
print(eval("x * 2"))                # 20   能访问当前作用域的变量
```

**`exec()`：把字符串当语句执行，**没有返回值**。**

```python
exec("a = 1 + 1")
print(a)        # 2
exec("for i in range(3): print(i, end=' ')")   # 0 1 2
```

> **安全红线**：**`eval()` / `exec()` 会执行任意代码**，绝不能用于网络输入、用户表单。教材 2.8 节的「求复杂算术表达式的值」用 `eval()` 是为了教学演示。安全替代品是 `ast.literal_eval()`（只解析字面量，不能执行函数调用）。

**函数的「无返回值」其实是返回 `None`：**

```python
def f():
    pass

print(f())              # None
print(type(f()))        # <class 'NoneType'>
print(f() is None)      # True
```

## 2.7 程序调试

### 错误类型

| 类型 | 何时发生 | 例子 | 报错 |
|---|---|---|---|
| **语法错误** SyntaxError | 代码不符合语法，**程序根本跑不起来** | `print "hi"` | `SyntaxError: Missing parentheses` |
| **运行时错误** | 语法对，执行中出错 | `1 / 0` | `ZeroDivisionError` |
| **逻辑错误** | 能跑，结果不对 | 求平均写成 `sum / (n-1)` | 无报错，最难查 |

常见内置异常：

| 异常 | 触发场景 | 例子 |
|---|---|---|
| `ZeroDivisionError` | 除以 0 | `1 / 0` |
| `TypeError` | 类型不匹配 | `"a" + 1` |
| `ValueError` | 类型对但值不对 | `int("abc")` |
| `NameError` | 用了未定义的变量 | `print(x)` |
| `IndexError` | 索引越界 | `[1,2][5]` |
| `KeyError` | 字典键不存在 | `{"a":1}["b"]` |
| `AttributeError` | 对象没有该属性/方法 | `"a".push()` |
| `IndentationError` | 缩进错误 | 少一个空格 |
| `ImportError` | 导入失败 | `import numpy` 未安装 |
| `FileNotFoundError` | 文件不存在 | `open("没有.txt")` |

```python
print(1 / 0)        # ZeroDivisionError: division by zero
print("a" + 1)      # TypeError: can only concatenate str (not "int") to str
print(int("abc"))   # ValueError: invalid literal for int() with base 10: 'abc'
print([1, 2][5])    # IndexError: list index out of range
print({"a": 1}["b"])    # KeyError: 'b'
```

### 在 PyCharm 中调试程序

| 操作 | 快捷键 | 作用 |
|---|---|---|
| 设/取消断点 | 行号左侧单击 / `Ctrl+F8` | 程序运行到此处暂停 |
| 调试运行 | `Shift+F9` | 进入 Debug 模式 |
| 单步跳过 | `F8` | 执行下一行（不进入函数） |
| 单步进入 | `F7` | 进入函数内部 |
| 跳出 | `Shift+F8` | 跳出当前函数 |
| 运行到光标 | `Alt+F9` | 直接跑到光标行 |
| 查看变量 | Debug 面板 Variables | 看每个变量的当前值 |

**调试心法**：先看**报错的最后一行**（异常类型 + 原因），再看**倒数第二行往上的调用栈**找到自己代码的位置。初学者最常犯的错是盯着第一行 `Traceback (most recent call last)` 发呆。

## 2.8 典型案例

### 2.8.1 计算复杂算术运算表达式的值

求 y = (a + b) / (a - b) + a · b² - √|a|，输入 a、b（a ≠ b）。

```python
import math

expr = input("请输入表达式（如 (3+4)/(3-4)+3*4**2-math.sqrt(abs(3))）：")
try:
    result = eval(expr)
    print(f"结果 = {result:.4f}")
except Exception as e:
    print("表达式有误：", e)
```

**思路**：用 `eval()` 把输入当表达式求值，用 `try/except` 兜住错误输入。**注意 `math` 需要先 import**，因为 `eval` 在当前的全局命名空间里查找名字。

### 2.8.2 求几何面、几何体的（表）面积或体积

输入半径 r 和高 h，输出圆柱的底面积、侧面积、表面积、体积。

```python
import math

r = float(input("半径 r = "))      # 输入：2
h = float(input("高 h = "))        # 输入：5

base = math.pi * r ** 2            # 底面积
side = 2 * math.pi * r * h         # 侧面积
total = 2 * base + side            # 表面积
volume = base * h                  # 体积

print(f"底面积={base:.2f}, 侧面积={side:.2f}, 表面积={total:.2f}, 体积={volume:.2f}")
# 底面积=12.57, 侧面积=62.83, 表面积=87.96, 体积=62.83
```

**思路**：先写公式再写代码。**`**` 的优先级高于 `*`**，所以 `2 * math.pi * r ** 2` 等价于 `2*π*(r²)`，无需额外括号。

### 2.8.3 解一元二次方程

a x² + b x + c = 0，支持判别式小于 0 的复数解。

```python
import math

a = float(input("a = "))       # 1
b = float(input("b = "))       # 2
c = float(input("c = "))       # 5

delta = b ** 2 - 4 * a * c
print(f"判别式 Δ = {delta}")     # Δ = -16.0

if a == 0:
    print("不是一元二次方程")
elif delta >= 0:
    x1 = (-b + math.sqrt(delta)) / (2 * a)
    x2 = (-b - math.sqrt(delta)) / (2 * a)
    print(f"x1={x1:.4f}, x2={x2:.4f}")
else:
    real, imag = -b / (2 * a), math.sqrt(-delta) / (2 * a)
    print(f"x1={real:.4f}+{imag:.4f}i, x2={real:.4f}-{imag:.4f}i")
    # x1=-1.0000+2.0000i, x2=-1.0000-2.0000i
```

**思路**：分三种情况——`a == 0` 退化、`Δ ≥ 0` 实根、`Δ < 0` 共轭复根。`math.sqrt` 不接受负数，所以先取 `-delta` 再开方。

### 2.8.4 验证码验证

生成 4 位随机验证码（含字母和数字），不区分大小写地校验用户输入。

```python
import random
import string

chars = string.ascii_letters + string.digits      # a-zA-Z0-9
code = "".join(random.choice(chars) for _ in range(4))
print("验证码：", code)          # 验证码： aK7p（每次不同）

user = input("请输入验证码：")
if user.lower() == code.lower():
    print("验证通过")
else:
    print("验证失败")
```

**思路**：`random.choice()` 从序列里等概率取一个字符，循环 4 次拼接；比较时用 `.lower()` 统一大小写，实现「不区分大小写」。

## 典型例题

**例 2.1**（题型：读程序写结果——运算符与优先级）

```python
print(7 // 2, 7 / 2, -7 // 2, 7 % 3, -7 % 3)
print(2 ** 3 ** 2, -2 ** 2, (-2) ** 2)
print(1 + 2 > 2 and 3 < 4 or 5 > 6)
```

**解**：`//` 向下取整、`/` 真除法；`%` 符号跟除数；`**` 右结合且优先级高于一元负号；逻辑运算 `and` 高于 `or`，`1+2>2` 为 `True`，`3<4` 为 `True`，`True and True` 为 `True`，`or` 短路直接返回 `True`。

**答案**：

```
3 3.5 -4 1 2
512 -4 4
True
```

**例 2.2**（题型：读程序写结果——可变对象与引用）

```python
a = [1, 2, 3]
b = a
c = a[:]
b.append(4)
c.append(5)
print(a, b, c)
print(a is b, a is c)
```

**解**：`b = a` 是**别名**（同一对象），`c = a[:]` 是**浅拷贝**（新列表）。`b.append(4)` 改的是 `a` 本身。

**答案**：

```
[1, 2, 3, 4] [1, 2, 3, 4] [1, 2, 3, 5]
True False
```

**例 2.3**（题型：读程序写结果——浮点与 round）

```python
print(0.1 + 0.2)
print(0.1 + 0.2 == 0.3)
print(round(0.1 + 0.2, 1))
print(round(2.675, 2))
print(round(0.5), round(1.5), round(2.5))
```

**解**：前两个是 IEEE 754 精度问题；`round` 用银行家舍入；2.675 二进制存储实际偏小。

**答案**：

```
0.30000000000000004
False
0.3
2.67
0 2 2
```

**例 2.4**（题型：编程题——字符串处理与格式化）

输入一个身份证号（18 位），输出：出生日期（YYYY-MM-DD 格式）、性别（第 17 位奇数为男、偶数为女）、是否以 "42" 开头。

```python
idc = input("身份证号：").strip()      # 输入：420106200503151234
birth = f"{idc[6:10]}-{idc[10:12]}-{idc[12:14]}"
gender = "男" if int(idc[16]) % 2 == 1 else "女"
print(f"出生日期：{birth}")
print(f"性别：{gender}")
print(f"湖北籍：{idc.startswith('42')}")
# 出生日期：2005-03-15
# 性别：男
# 湖北籍：True
```

**解**：身份证第 7～14 位是出生日期 `YYYYMMDD`（索引 6:14），第 17 位（索引 16）判断性别。切片是「左闭右开」，所以 `idc[6:10]` 取的是第 7～10 个字符。`"42"` 是湖北省的行政区划代码前缀。

**答案**：出生日期 `2005-03-15`、性别 `男`、湖北籍 `True`。

**例 2.5**（题型：编程题——整数位操作与内置函数）

输入一个正整数 n，输出：它的位数、各位数字之和、反转后的数（如 1230 → 321）。**要求用字符串与数学两种方法各写一遍**。

```python
n = int(input("n = "))          # 输入：1230

# 方法一：字符串法（简洁）
s = str(n)
print(len(s), sum(int(ch) for ch in s), int(s[::-1]))
# 4 6 321

# 方法二：数学法（考算法思维）
m, cnt, total, rev = n, 0, 0, 0
while m > 0:
    d = m % 10
    total += d
    rev = rev * 10 + d
    cnt += 1
    m //= 10
print(cnt, total, rev)
# 4 6 321
```

**解**：字符串法用 `len`、生成器求和、`[::-1]` 反转；数学法反复 `% 10` 取末位、`// 10` 去掉末位，`rev = rev*10 + d` 逐步构造反转数。

**只有数学法需要特判 `n == 0`**：数学法的 `while n > 0` 循环对 0 一次都不执行，三位结果全是 0，输出 `0 0 0`（位数错了）；字符串法 `str(0)` 长度天然是 1、`sum` 是 0、`int("0"[::-1])` 是 0，输出 `1 0 0` 天然正确。实测对照：

```python
digits_math(0)   # (0, 0, 0)   ← 位数错，必须特判
digits_str(0)    # (1, 0, 0)   ← 天然正确
digits_math(123) # (3, 6, 321)
digits_str(123)  # (3, 6, 321)
```

**答案**：位数 4、数字和 6、反转数 321。

**例 2.6**（题型：编程题——三种格式化方式等价改写）

已知 `name = "Python"`, `ver = 3.12`, `year = 1991`，用三种方式输出 `Python 3.12 诞生于 1991 年`（版本号保留 1 位小数）。

```python
name, ver, year = "Python", 3.12, 1991
print("%s %.1f 诞生于 %d 年" % (name, ver, year))
print("{} {:.1f} 诞生于 {} 年".format(name, ver, year))
print(f"{name} {ver:.1f} 诞生于 {year} 年")
# 三行输出完全相同：Python 3.1 诞生于 1991 年
```

**解**：注意 `%.1f` 与 `{:.1f}` 都是「保留 1 位小数」，本题 `3.12` 保留 1 位后是 `3.1`——**这是普通的「四舍」**（被丢掉的那位是 2，小于 5），**与银行家舍入无关**。银行家舍入只在**恰好等于一半**时才生效，要举真正的平局例子：

```python
print(round(2.5), round(0.5), round(1.5))   # 2 0 2   ← 恰好 .5，取偶数
print(round(2.675, 2))                      # 2.67    ← 这里的锅是二进制
print(Decimal(2.675))   # 2.67499999999999982236...  ← 存储值偏小，所以舍去
print(Decimal(3.12))    # 3.12000000000000010658...  ← 存储值偏大，但它第 2 位是 2，照样舍去
```

一句话：**`round(3.12, 1) = 3.1` 的原因是「第 2 位小数 2 < 5」，不是舍入规则特殊**；银行家舍入与二进制误差是另外两件事，只在 `round(2.5)` 和 `round(2.675, 2)` 这类场合才登场。实际项目统一用 f-string。

**答案**：三种写法输出一致：`Python 3.1 诞生于 1991 年`。

## 易错点提醒

1. **用 `==` 比较浮点数。**
   - 错在哪：`if 0.1 + 0.2 == 0.3:` 永远不成立。
   - 正确做法：`math.isclose(a, b)` 或 `abs(a-b) < 1e-9`；只显示时用 `round()`。
2. **以为 `a = b` 会复制列表/字典。**
   - 错在哪：`b = a` 只是加了个别名，改 `b` 会连带改 `a`。
   - 正确做法：要独立副本用 `a.copy()`、`a[:]`、`dict(a)`；嵌套结构用 `copy.deepcopy(a)`。
3. **用 `is` 比较数值或字符串。**
   - 错在哪：`a = 1000; b = 1000; a is b` 可能为 `False`，结果依赖实现细节。
   - 正确做法：数值比较用 `==`；`is` 只用于 `None`、`True`、`False`。
4. **`input()` 的结果直接参与运算。**
   - 错在哪：`input() + 1` → `TypeError`；`input() + input()` 变成字符串拼接。
   - 正确做法：`int(input())`、`float(input())` 或 `eval(input())`。
5. **搞错 `//` 与 `%` 对负数的行为。**
   - 错在哪：以为 `-7 // 2 == -3`、`-7 % 2 == -1`。
   - 正确做法：记「**`//` 向下取整，`%` 余数符号跟除数**」→ `-7 // 2 == -4`，`-7 % 2 == 1`；要「向零截断」用 `int(a / b)` 或 `math.trunc`。
6. **忘记 `int("3.9")` 会报错。**
   - 错在哪：`ValueError: invalid literal for int() with base 10: '3.9'`。
   - 正确做法：先 `float()` 再 `int()`，或直接用 `int(float(s))`。
7. **用 `eval()` 处理不可信输入。**
   - 错在哪：`eval(input())` 可能执行任意代码，是安全漏洞。
   - 正确做法：只对字面量用 `ast.literal_eval()`；数值转换用 `int()`/`float()`。
8. **修改字符串的某个字符。**
   - 错在哪：`s[0] = 'H'` → `TypeError`，字符串不可变。
   - 正确做法：`s = 'H' + s[1:]` 或 `s.replace(...)`，本质是造新字符串。

## 本章小结

- **变量无类型，对象有类型**；赋值是给对象贴标签，`b = a` 对可变对象是别名而非复制。
- **可变**：`list`/`dict`/`set`（原地改）；**不可变**：`int`/`float`/`str`/`tuple`（改就新建对象）。
- `int` 任意精度；`float` 是 IEEE 754 双精度，`0.1 + 0.2 != 0.3`，要精确用 `Decimal`（**必须传字符串**）。
- `bool` 是 `int` 子类，`True == 1` 但 `True is not 1`；空容器 / `0` / `""` / `None` 为假，**`"0"` 和 `"False"` 都是真**。
- 字符串**不可变**，支持负索引与切片（左闭右开、越界不报错），`[::-1]` 反转；格式化**首选 f-string**。
- `==` 比值，`is` 比身份；`is` 只用于 `None`；小整数缓存 `[-5, 256]` 是实现细节，不要依赖。
- **`/` 得 float，`//` 向下取整（`-7//2 == -4`）**，`%` 符号跟除数；`**` 右结合且高于一元负号。
- `+=` 对列表是**就地修改**（`extend` 语义），`+` 是新建对象。
- 三类错误：语法错误（跑不起来）、运行时错误（抛异常）、逻辑错误（最难查）；报错先看最后一行。

> **一句话记住本章**：变量是标签不是盒子，字符串和元组不可变，浮点数不可用 `==` 比较，`is` 只配 `None`。

## 自测题

1. `print(type(True) == int, isinstance(True, int))` 输出什么？为什么？
2. 写出 `0.1 + 0.2 == 0.3` 的结果并解释原因；给出两种正确的比较方式。
3. `s = "Python"`，写出 `s[-1]`、`s[2:5]`、`s[::-1]`、`s[10:20]` 的结果。
4. 判断并说明：`a = [1,2]; b = a; b += [3]` 之后 `a` 是什么？如果换成 `b = b + [3]` 呢？
5. `print(7 // 2, -7 // 2, 7 % 3, -7 % 3)` 的输出是什么？
6. 用一行 f-string 输出 `π = 3.1416`（变量 `pi = 3.14159`，保留 4 位小数）。
7. `x = 256; y = 256; print(x is y)` 与 `x = 257; y = 257; print(x is y)` 在交互式窗口中分别输出什么？为什么不能依赖这个结果写代码？
8. 下面的代码会报什么错？怎么改？`age = input("年龄："); print(age + 1)`
9. `round(0.5)`、`round(1.5)`、`round(2.675, 2)` 分别等于多少？
10. 用一条语句把 `a, b` 的值互换，并解释其原理。

**参考答案**

1. `False True`。`bool` 是 `int` 的子类，`isinstance` 认继承关系，`type()` 做精确匹配，`type(True)` 是 `<class 'bool'>` 不等于 `int`。
2. `False`。0.1、0.2 的二进制表示是无限循环小数，双精度存储有误差，相加后误差落在第 17 位。正确比较：`math.isclose(0.1+0.2, 0.3)` 或 `abs(0.1+0.2-0.3) < 1e-9`。
3. `'n'`、`'tho'`、`'nohtyP'`、`''`（空字符串，切片越界不报错）。
4. `+=` 就地 `extend`，`a` 变成 `[1, 2, 3]`（`b` 与 `a` 是同一对象）；`b = b + [3]` 新建列表并重新绑定 `b`，`a` 保持 `[1, 2]`。
5. `3 -4 1 2`。
6. `print(f"π = {pi:.4f}")`
7. 交互式下分别是 `True` 和 `False`（256 在小整数缓存 `[-5,256]` 内；257 每次新建对象）。但在同一脚本/同一代码块中，编译器会合并相同常量，257 也可能得到 `True`。所以 `is` 的结果依赖实现与代码组织方式，比较数值必须用 `==`。
8. `TypeError: can only concatenate str (not "int") to str`。改成 `print(int(age) + 1)` 或一开始就 `age = int(input("年龄："))`。
9. `0`、`2`、`2.67`。`round` 是银行家舍入（四舍六入五取偶），且 2.675 的双精度存储略小于 2.675。
10. `a, b = b, a`。右边先构成元组 `(b, a)`，再从左到右解包赋值给 `a`、`b`，因此不借助临时变量。
