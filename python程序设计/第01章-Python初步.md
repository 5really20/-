# 第1章 Python初步

> 对应教材：王雷春《Python 程序设计——从基础入门到实战应用（微课版）（第2版）》第1章｜建议学时：2（讲授）+ 2（实验）｜重要度：★★☆☆☆

本章不考难题，但**环境搭建、编码规范、`input()`/`print()` 三件事是所有后续章节的地基**。考试里第 1 章通常只出 5～10 分的选择填空，实验课上却是人人踩坑的地方。

## 本章知识地图

- 1.1 概述
  - Python 的出身、设计哲学、优缺点
  - Python 的典型应用领域
- 1.2 开发环境
  - Python 解释器版本、CPython 是什么
  - IDLE / PyCharm / VS Code
  - `pip` 安装第三方库、国内镜像源
- 1.3 程序开发
  - 交互式 vs 文件式两种运行方式
  - 用 PyCharm 新建工程、运行、查看解释器
- 1.4 程序结构和编码规范
  - `.py` 与 `.pyc`、`__pycache__`
  - 缩进即语法、注释、PEP 8
- 1.5 输入、输出函数
  - `input()` 返回值一定是 `str`
  - `print()` 的 `sep` / `end` / 格式化输出

## 学习目标

- 能说清 Python 是**解释型**语言，并解释它与 C 语言「编译 → 链接 → 执行」的差别。
- 能在自己电脑上装好 Python 3.12+ 与 PyCharm，并用 `pip install` 装上一个第三方库。
- 能区分交互式（`>>>`）与文件式两种运行方式，并说出各自的适用场景。
- 能按 PEP 8 写出规范的命名、缩进与注释。
- 能正确使用 `input()` 读入数据并**主动转换类型**，能正确使用 `print()` 的 `sep`、`end` 与 `f-string` 控制输出格式。
- 能说出 `eval()` 的作用与它的安全风险。

## 1.1 概述

### Python 简介

**Python 由 Guido van Rossum（吉多·范罗苏姆）于 1989 年圣诞节期间开始设计，1991 年发布第一个公开版本。**

几个必须记住的时间点：

| 时间 | 事件 | 记忆点 |
|---|---|---|
| 1989 | Guido 开始设计 Python | 圣诞假期项目 |
| 1991 | Python 0.9.0 公开发布 | 已有函数、异常、类 |
| 2000 | Python 2.0 | 引入垃圾回收、Unicode |
| 2008 | Python 3.0 | **不兼容 2.x**，`print` 变成函数 |
| 2020-01-01 | Python 2 停止维护 | 现在全部用 Python 3 |
| 2024-10 | Python 3.13 发布 | 教材用 3.12.8 |

**Python 的名字来自英国喜剧团体 Monty Python，不是蟒蛇。** 但蟒蛇 Logo 太深入人心，嵩天教材里的 `turtle` 实例就叫「Python 蟒蛇绘制」。

**CPython 是官方解释器**，用 C 语言写的。我们平时说的「装 Python」，装的就是 CPython；除此之外还有 PyPy（快）、Jython（跑在 JVM 上）、IronPython（跑在 .NET 上），但本课程只用 CPython。

### Python 的特点

| 特点 | 含义 | 代价 |
|---|---|---|
| 解释型 | 逐行翻译执行，不用编译 | 比 C 慢（纯计算慢 10～100 倍） |
| 动态类型 | 变量不用声明类型，运行时才确定 | 类型错误要到运行才发现 |
| 强类型 | `1 + "a"` 直接报错，不做隐式转换 | 必须手动 `int()`/`str()` |
| 面向对象 | 一切皆对象，连 `int` 都是对象 | —— |
| 自动内存管理 | 有引用计数 + 垃圾回收，无 `free()` | 内存占用偏高 |
| 丰富的库 | 自带「标准库」+ 海量「第三方库」 | 要会挑库 |
| 强制缩进 | 缩进即语法，用于划分代码块 | 混用 Tab/空格会报错 |
| 胶水语言 | 能方便地调用 C/C++ 写的扩展 | —— |

> **重点**：**Python 是动态类型 + 强类型语言。** 「动态」说的是类型在运行时确定；「强类型」说的是不同类型不会偷偷互转。C 语言是静态类型 + 弱类型（`int a = 3.7;` 会被截断成 3）。这两个维度经常被搞混，是选择题常客。

### Python 的应用

- **科学计算与数据分析**：NumPy、Pandas、SciPy（教材 7.3 节）。
- **数据可视化**：Matplotlib（教材第 7 章）。
- **人工智能**：scikit-learn、PyTorch、TensorFlow（教材第 15 章）。
- **网络爬虫**：requests、BeautifulSoup、Scrapy（教材第 14 章）。
- **Web 后端**：Django、Flask、FastAPI。
- **自动化运维与办公**：os、subprocess、openpyxl、python-docx。
- **图形界面**：tkinter（教材第 12 章）。

## 1.2 开发环境

### Python 版本

**本课程统一使用 Python 3.12 及以上版本**（教材案例在 Python 3.12.8 上测试通过）。

- 命令行输入 `python --version` 或 `python -V` 查看版本。
- Windows 上如果同时装了 2.x 和 3.x，可能要用 `py -3.12` 启动。
- **不要用 Python 2 的代码去写作业**：`print "hi"` 在 Python 3 里是语法错误。

### 常用开发环境

| 环境 | 特点 | 适合 |
|---|---|---|
| IDLE | Python 自带，有 `>>>` 交互窗口 | 验证一两行代码 |
| PyCharm | 教材指定（Community Edition 2024.2.5），工程管理、调试器最强 | **本课程与实验** |
| VS Code | 轻量，插件丰富 | 日常写脚本 |
| Jupyter Notebook | 单元格式，边写边看结果 | 数据分析、机器学习 |

### Python 下载和安装

Windows 安装的关键一步：**安装向导第一页要勾选 `Add python.exe to PATH`**。不勾的话，命令行里敲 `python` 会提示「不是内部或外部命令」。

安装完成后在命令行验证：

```
> python --version
Python 3.12.8
> pip --version
pip 24.2 from ...\site-packages\pip (python 3.12)
```

### 第三方库安装

**标准库**随 Python 一起安装，直接 `import` 即可；**第三方库**必须先用 `pip` 安装。

```bash
pip install numpy                    # 装最新版
pip install numpy==1.26.4            # 装指定版本
pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple   # 用清华镜像加速
pip uninstall numpy                  # 卸载
pip list                             # 列出已安装的包
pip show numpy                       # 查看某个包的信息
```

**为什么要用镜像？** 默认源 `pypi.org` 在国外，国内下载经常超时。常见镜像：

- 清华：`https://pypi.tuna.tsinghua.edu.cn/simple`
- 阿里云：`https://mirrors.aliyun.com/pypi/simple`

永久配置镜像（一次配置，长期生效）：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

## 1.3 程序开发

### 程序运行方式

| 方式 | 操作 | 优点 | 缺点 |
|---|---|---|---|
| 交互式 | 命令行敲 `python` 进入 `>>>`，逐行输入 | 立即看到结果 | 关掉就没了，不能保存 |
| 文件式 | 把代码存成 `.py`，用 `python 文件名.py` 运行 | 可保存、可复用 | 改一次要重跑一次 |

实例如下（交互式）：

```python
>>> 2 + 3
5
>>> print("Hello")
Hello
>>> 1 / 3
0.3333333333333333
```

注意交互式下表达式会**自动打印结果**，而文件式下**不打印任何东西**，必须 `print()`：

```python
# demo.py
2 + 3            # 文件式运行：什么都不输出
print(2 + 3)     # 输出：5
```

> **坑**：初学者最常见的问题就是「我算式写了，为什么没输出？」——因为**文件式运行时表达式的值不会自动显示**，要用 `print()`。

### 使用 Python 自带工具开发 Python 程序

1. 开始菜单打开 IDLE。
2. `File → New File` 新建脚本，写入代码。
3. `F5` 运行（会提示先保存）。

### 使用 PyCharm 开发 Python 程序

标准流程：`New Project` → 选择解释器（Interpreter）→ 在项目里 `New → Python File` → 写代码 → 右键 `Run`。

**实验课必查的三件事**：

1. 右下角显示的 Python 版本是不是 3.12+。
2. `File → Settings → Project → Python Interpreter` 里，装的库是不是装到了**当前项目用的解释器**上（装了却 import 不到，99% 是装到了另一个解释器）。
3. 文件名**不要叫 `random.py`、`json.py`、`turtle.py`、`test.py`**，会把自己的文件和标准库/第三方库重名，导致 `import` 直接失败。

## 1.4 程序结构和编码规范

### 文件类型

| 文件 | 说明 |
|---|---|
| `.py` | 源代码，纯文本，可用记事本打开 |
| `.pyc` | 编译后的字节码，放在 `__pycache__` 目录 |
| `.ipynb` | Jupyter Notebook 文件 |

**Python 也有「编译」这一步**：第一次 `import` 一个模块时，解释器会把它编译成字节码缓存到 `__pycache__`，下次启动更快。所以「解释型语言没有编译过程」这句话严格来说是错的——它没有**独立的编译阶段**，但存在字节码编译。

### 代码结构

```python
# 1. 编码声明（Python 3 默认 UTF-8，通常可省略）
# -*- coding: utf-8 -*-

# 2. 模块文档字符串
"""温度转换小程序：把摄氏度转成华氏度。"""

# 3. 导入语句
import math

# 4. 常量定义（Python 没有真正的常量，靠全大写约定）
PI = 3.14159

# 5. 函数与类定义
def celsius_to_fahrenheit(c):
    return c * 1.8 + 32

# 6. 主程序入口
if __name__ == "__main__":
    print(celsius_to_fahrenheit(100))   # 212.0
```

**Python 没有 `main` 函数，也没有 `{ }`。** 程序从文件第一行开始，自上而下执行。`if __name__ == "__main__":` 只是一个约定俗成的「入口标记」，作用见第 7 章。

### 程序编码规范（PEP 8）

**PEP 8 是 Python 官方代码风格指南**，考试和实验都会扣分，必须背下来：

| 项目 | 规定 | 反例 |
|---|---|---|
| 缩进 | **4 个空格**，不要用 Tab | 2 个空格 |
| 行宽 | 每行不超过 79 字符（宽松到 100+） | 一行写 200 字符 |
| 变量/函数名 | 小写 + 下划线 `snake_case` | `myName`、`MyName` |
| 类名 | 大驼峰 `CamelCase` | `my_class` |
| 常量名 | 全大写 `MAX_SIZE` | `maxSize` |
| 运算符两侧 | 各留一个空格 | `a=1+2` |
| 逗号后 | 留一个空格 | `f(a,b)` |
| 注释 | `#` 后留一个空格 | `#注释` |

```python
# 推荐
def calc_area(radius):
    """根据半径计算圆面积。"""
    return 3.14159 * radius ** 2


# 不推荐（能跑，但会被扣分）
def CalcArea(radius):
    return 3.14159*radius**2
```

注释的三种写法：

```python
# 单行注释

"""
多行字符串常被当作多行注释使用
（严格说它是字符串，只是没被赋值，会被解释器忽略）
"""

def f(x):
    """这行是函数文档字符串 docstring，可用 help(f) 查看。"""
    return x
```

## 1.5 输入、输出函数

### input() 函数

**`input()` 从键盘读入一行，返回值永远是字符串 `str`。**（这一点和 C 的 `scanf` 完全不同，是本章最重要的坑。）

```python
name = input("请输入你的名字：")     # 输入：张三
print(type(name))                    # <class 'str'>
print("你好，" + name)               # 你好，张三

age = input("请输入年龄：")          # 输入：18
print(age + 1)                       # 报错！TypeError: can only concatenate str (not "int") to str
print(int(age) + 1)                  # 19   ← 必须先转换
```

常见转换写法：

```python
n = int(input())            # 输入整数
x = float(input())          # 输入小数
a, b = input().split()      # 输入 "3 4"，得到两个字符串
a, b = map(int, input().split())    # 输入 "3 4"，得到整数 3 和 4
nums = list(map(int, input().split()))  # 一行多个整数存成列表
```

用 `eval()` 一步到位把输入当表达式求值：

```python
x = eval(input("请输入一个数："))   # 输入：3.5
print(type(x))                      # <class 'float'>
print(x * 2)                        # 7.0

s = eval("1 + 2 * 3")
print(s)                            # 7
```

> **安全警告**：**`eval()` 会执行任意 Python 表达式**。如果输入是 `__import__('os').system('rm -rf /')`，程序真的会执行。所以 `eval()` 只能用于自己可控的输入（比如教材里的课堂练习），**永远不要用 `eval()` 处理网络或用户提交的不可信数据**。安全替代方案是 `ast.literal_eval()`。

### print() 函数

完整语法：**`print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)`**

| 参数 | 作用 | 默认值 |
|---|---|---|
| `*objects` | 要打印的一个或多个对象 | —— |
| `sep` | 多个对象之间的分隔符 | 一个空格 `' '` |
| `end` | 打印结束后追加的字符 | 换行 `'\n'` |
| `file` | 输出目标 | 标准输出 `sys.stdout` |
| `flush` | 是否立刻刷新缓冲区 | `False` |

```python
print("a", "b", "c")                  # a b c
print("a", "b", "c", sep="-")         # a-b-c
print("a", "b", sep="", end="")       # ab（不换行）
print("下一句", end="...\n")          # 下一句...

print("第一行", end=" ")
print("还在同一行")                    # 第一行 还在同一行
```

常用格式化输出（三种方式的对比见第 2 章）：

```python
name, score = "张三", 92.5
print("姓名：%s，成绩：%.1f" % (name, score))          # 姓名：张三，成绩：92.5
print("姓名：{}，成绩：{:.1f}".format(name, score))     # 姓名：张三，成绩：92.5
print(f"姓名：{name}，成绩：{score:.1f}")               # 姓名：张三，成绩：92.5  ← 推荐
```

输出到文件而不是屏幕：

```python
with open("out.txt", "w", encoding="utf-8") as f:
    print("写入文件的一行", file=f)
```

## 典型例题

**例 1.1**（题型：读程序写结果）

```python
print(1, 2, 3, sep="")
print(1, 2, 3, sep=",", end=";\n")
print("a" + "b", "c" * 2)
```

**解**：第 1 行 `sep=""` 表示无分隔符；第 2 行分隔符逗号、结尾 `;\n`；第 3 行 `+` 是字符串拼接得 `"ab"`，`*` 是重复 2 次得 `"cc"`，两者用默认空格分开。

**答案**：

```
123
1,2,3;
ab cc
```

**例 1.2**（题型：温度转换——教材 2.1 节同款，经典必考）

用户输入带符号的摄氏温度，输出华氏温度，保留 1 位小数。

```python
c = eval(input("请输入摄氏温度："))     # 输入：36.5
f = c * 1.8 + 32
print(f"华氏温度为 {f:.1f}")             # 华氏温度为 97.7
```

**解**：转换公式 华氏 = 摄氏 × 1.8 + 32。用 `eval()` 读入可以直接得到数字类型（输入 `36.5` 得到 `float`，输入 `36` 得到 `int`），再按 `:.1f` 保留 1 位小数。

**答案**：`f = C * 1.8 + 32`，输出 `97.7`。

**例 1.3**（题型：编程题——圆面积与周长）

输入半径 `r`，输出圆面积（保留 2 位小数）与周长。

```python
import math

r = float(input("半径："))     # 输入：2.5
area = math.pi * r ** 2
peri = 2 * math.pi * r
print(f"面积={area:.2f}, 周长={peri:.2f}")   # 面积=19.63, 周长=15.71
```

**解**：`math.pi` 比手写 `3.14` 精确得多（`3.141592653589793`）。`r ** 2` 是幂运算，等价于 `pow(r, 2)`。

**答案**：面积 `19.63`，周长 `15.71`。

**例 1.4**（题型：读程序写结果——input 类型陷阱）

```python
a = input("a=")     # 输入 3
b = input("b=")     # 输入 4
print(a + b)
print(int(a) + int(b))
```

**解**：`input()` 返回字符串，所以 `a + b` 是字符串拼接 `"3" + "4"`；`int()` 转换后才做加法。

**答案**：

```
34
7
```

**例 1.5**（题型：编程题——求三位数的各位数字）

输入一个三位整数，输出它的百位、十位、个位数字之和。例如输入 `258`，输出 `15`。

```python
n = int(input("三位整数："))     # 输入：258
bai = n // 100
shi = n // 10 % 10
ge = n % 10
print(f"{bai}+{shi}+{ge}={bai + shi + ge}")   # 2+5+8=15
```

**解**：**`//` 是整除（向下取整），`%` 是取余。** 百位 = `n // 100`；十位 = 先 `n // 10` 去掉个位再 `% 10`；个位 = `n % 10`。

**答案**：`2+5+8=15`。

**例 1.6**（题型：编程题——格式化输出表格）

输入商品名、单价、数量，输出一行小票，要求金额右对齐、保留 2 位小数。

```python
name = input("商品：")            # 输入：笔记本
price = float(input("单价："))    # 输入：12.5
num = int(input("数量："))        # 输入：3
total = price * num
print(f"{name:<8}{price:>8.2f}{num:>5d}{total:>10.2f}")
# 笔记本        12.50    3     37.50
```

**解**：格式说明符 `: <填充/对齐><宽度>.<精度><类型>`。`<` 左对齐、`>` 右对齐，`8.2f` 表示总宽 8 位保留 2 位小数。

**逐位算一遍**（学生核对输出时最常在这里对不上）：

| 片段 | 内容 | 字符数 | 补空格 | 结果 |
|---|---|---|---|---|
| `{name:<8}` | `笔记本` | 3 | 5（左对齐，补在右边） | `笔记本␣␣␣␣␣` |
| `{price:>8.2f}` | `12.50` | 5 | 3（右对齐，补在左边） | `␣␣␣12.50` |
| `{num:>5d}` | `3` | 1 | 4 | `␣␣␣␣3` |
| `{total:>10.2f}` | `37.50` | 5 | 5 | `␣␣␣␣␣37.50` |

> **坑：宽度参数按「字符数」算，不按「显示宽度」算。** `笔记本` 是 **3 个字符**，所以 `{name:<8}` 只补 5 个空格；但中文在等宽终端里占 **2 列**，实际显示宽度是 6 列。于是**含中文的表格用 f-string 宽度参数是对不齐的** —— 上表中 `本` 与 `12.50` 之间共 5 + 3 = **8 个空格**，看起来反而比纯英文表格更宽。
>
> **三种可行做法**：① 表格里的列名和内容统一用**英文/数字**；② 用 `\t` 制表符让终端自己对齐（中文仍可能错位）；③ 按「显示宽度」手工换算补空格 —— 写个小函数把每个中文算 2 列：
>
> ```python
> def pad(s, width):
>     """按显示宽度补空格（中文算 2 列）"""
>     w = sum(2 if ord(c) > 127 else 1 for c in s)
>     return s + " " * max(0, width - w)
>
> print(pad("笔记本", 12) + "12.50")   # 笔记本      12.50
> print(pad("Notebook", 12) + "12.50") # Notebook    12.50
> ```
>
> 做课程设计时输出成绩表、账单表几乎一定会遇到这个问题，**提前知道就不会白调半小时**。

**答案**：输出 `笔记本        12.50    3     37.50`（`本` 与 `12.50` 之间是 **8** 个空格 = `{name:<8}` 的 5 个 + `{price:>8.2f}` 的 3 个）。

## 易错点提醒

1. **`input()` 返回的是字符串，不是数字。**
   - 错在哪：`n = input(); print(n + 1)` → `TypeError`。
   - 正确做法：`n = int(input())`，或 `n = eval(input())`（仅限可信输入）。
2. **文件式运行时，表达式不会自动打印。**
   - 错在哪：脚本里写了 `2 + 3` 却没有任何输出。
   - 正确做法：要显示就 `print(2 + 3)`。交互式窗口 `>>>` 才会自动回显。
3. **缩进混用 Tab 和空格。**
   - 错在哪：`TabError: inconsistent use of tabs and spaces in indentation`。
   - 正确做法：整个文件统一 4 个空格；PyCharm 里 `Ctrl+Alt+L` 一键格式化。
4. **中文乱码 / 文件编码不匹配。**
   - 错在哪：`UnicodeDecodeError: 'gbk' codec can't decode byte ...`。
   - 正确做法：读写文件时显式写 `encoding="utf-8"`（见第 9 章）。
5. **变量名或文件名与标准库重名。**
   - 错在哪：文件叫 `random.py`，再 `import random` 时导入的是自己，`random.randint` 报 `AttributeError`。
   - 正确做法：换个名字（如 `my_random.py`），并删掉残留的 `random.pyc`。
6. **用 `python` 命令却启动了 Python 2。**
   - 错在哪：`print "hi"` 报 `SyntaxError: Missing parentheses in call to 'print'`。
   - 正确做法：用 `python3` 或 `py -3.12`；确认 `python --version` 输出 3.x。

## 本章小结

- Python 是**解释型、动态类型、强类型、面向对象、自动内存管理**的通用语言，创办者 Guido van Rossum，1991 年首发。
- 开发环境用 **Python 3.12+ + PyCharm CE**；第三方库用 `pip install` 安装，国内建议配清华镜像。
- 两种运行方式：**交互式**（`>>>`，自动回显）与**文件式**（`.py`，必须 `print`）。
- **PEP 8**：4 空格缩进、`snake_case` 变量名、`CamelCase` 类名、运算符两侧空格。
- **`input()` 永远返回 `str`**，要参与算术必须 `int()`/`float()`/`eval()` 转换。
- **`print(*objs, sep, end)`**，格式化优先用 **f-string**：`f"{x:.2f}"`。
- 与 C 的三大直观差异：不用写 `main`、不用分号、**缩进就是语法**。

> **一句话记住本章**：Python 不用声明类型也不用大括号，但 `input()` 给的是字符串，缩进错了程序就不跑。

## 自测题

1. Python 是编译型还是解释型语言？它有没有编译过程？
2. `print(1, 2, 3, sep="|", end="*")` 的输出是什么？
3. 判断：`a = input()` 后，若用户输入 `5`，则 `a` 的类型是 `int`。
4. 写出把一行 `"3 4"` 读成两个整数 `a`、`b` 的代码。
5. `eval()` 与 `int()` 有什么本质区别？`eval()` 有什么风险？
6. 下列变量名哪些不符合 PEP 8？`myName`、`my_name`、`MyClass`、`MAX_SIZE`、`count1`
7. 为什么把文件命名为 `json.py` 会导致程序出错？
8. 用一行代码输出：`姓名:张三,年龄:18`（姓名和年龄存在变量 `name`、`age` 中）。

**参考答案**

1. 解释型；有编译过程，但只编译成字节码（`.pyc`）缓存起来，没有独立的编译阶段。
2. `1|2|3*`（不换行）。
3. 错。`input()` 返回 `str`，`type(a)` 是 `<class 'str'>`。
4. `a, b = map(int, input().split())`
5. `int()` 只能把字符串/数字转成整数；`eval()` 会把字符串当**表达式求值**，可以算出 `"1+2"` 得 3，也能返回列表、字典等。风险是执行任意代码，不可用于不可信输入，应改用 `ast.literal_eval()`。
6. `myName` 不符合（应用 `my_name`）；`MyClass` 作为类名符合、作为变量名不符合。`my_name`、`MAX_SIZE`、`count1` 都符合。
7. 因为同一目录下的 `json.py` 会**遮蔽标准库的 `json` 模块**，`import json` 导入的是你自己的文件，里面没有 `dumps`/`loads` 等函数。
8. `print(f"姓名:{name},年龄:{age}")`
