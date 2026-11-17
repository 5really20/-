# 第17章 Python 工程实践与常用标准库（拓展）

> 对应教材：**拓展章**（综合运用第1~15章知识，补齐工程实践能力）｜建议学时：2（讲授）+ 4（实验）｜重要度：★★★★☆

## 本章知识地图

- **17.0 前置知识对照** —— 哪些内容已在前面章节讲过（避免重复学）
- **17.1 `pathlib`** —— 面向对象的路径操作，替代 `os.path` 字符串拼接
- **17.2 `itertools`** —— 排列组合、无限迭代器、分组、滑动窗口
- **17.3 `collections`** —— `Counter` / `defaultdict` / `deque` / `namedtuple` / `ChainMap`
- **17.4 `functools`** —— `reduce` / `partial` / `lru_cache` / `wraps` / `cmp_to_key`
- **17.5 `logging`** —— 比 `print` 更专业的日志：级别、格式、处理器、写文件
- **17.6 `argparse`** —— 命令行参数解析，把脚本变成"工具"
- **17.7 类型注解** —— 让代码自解释，配合 mypy 做静态检查
- **17.8 虚拟环境与包管理** —— `venv` / `pip` / `requirements.txt` / 国内镜像
- **17.9 JSON 与配置文件** —— 项目配置的三种存法
- **17.10 调试与断言** —— `pdb`、`breakpoint()`、`assert`、`logging.exception`
- **17.11 项目结构与打包** —— 标准目录布局、`pyproject.toml`、`PyInstaller`
- **17.12 PEP 8 与工程规范** —— 命名、导入顺序、文档字符串、类型注解规范

**这一章解决什么问题？** 前 16 章教会你**写 Python**，这一章教你**做项目**。差别在于：作业脚本跑一次就扔，项目要被人读、被人改、要在别人电脑上跑起来。本章的库（`pathlib`/`itertools`/`collections`/`functools`/`logging`/`argparse`）**全是标准库，不需要 `pip install`**，但它们是"专业代码"和"作业代码"的分水岭。

## 学习目标

- 能用 `pathlib.Path` 完成路径拼接、判断存在、读写文本、遍历目录，并说清它比 `os.path` 好在哪。
- 能用 `itertools` 的 `product`/`permutations`/`combinations` 解决排列组合问题，用 `groupby` 做分组统计。
- 能用 `Counter` 做词频统计、`defaultdict(list)` 做分组、`deque` 做队列和滑动窗口。
- 能用 `lru_cache` 给递归函数加速，用 `partial` 固化参数，用 `cmp_to_key` 处理复杂排序。
- 能用 `logging` 替代 `print` 输出日志，会设置级别、格式，并同时输出到控制台和文件。
- 能用 `argparse` 写一个带位置参数、可选参数、默认值和 `choices` 校验的命令行工具。
- 能给函数写类型注解，并知道**注解在运行时不会被检查**。
- 能创建虚拟环境、导出 `requirements.txt`、配置国内镜像源。
- 能用 `pdb`/`breakpoint()` 单步调试，知道 `assert` 会被 `python -O` 关闭。
- 能按标准布局组织一个多文件 Python 项目，并说清 `pyproject.toml` 的作用。

## 17.0 前置知识对照

**本章不重复讲已经讲过的东西**，遇到相关内容请回看对应章节：

| 内容 | 已在哪里讲过 | 本章的处理 |
|---|---|---|
| 异常处理、`try/except/finally`、自定义异常 | **第10章** | 17.10 只补 `logging.exception` 与调试技巧 |
| `unittest` 单元测试 | **第10章** | 不重复 |
| `os` / `sys` 模块 | **第7章 7.2.4、7.2.5** | 17.1 讲 `pathlib` 与 `os.path` 的取舍 |
| `datetime` / `time` | **第7章 7.2.3** | 不重复 |
| `re` 正则表达式 | **第8章** | 不重复 |
| `json` 模块 | **第9章 9.4** | 17.9 讲"配置文件"这个具体用法 |
| 模块、包、`import`、`__name__ == "__main__"` | **第7章** | 17.11 讲项目目录布局 |
| PEP 8 编码规范 | **第1章 1.4.3** | 17.12 补工程层面的规范（导入顺序、文档字符串） |
| 装饰器 | **第5章 5.5** | 17.4 讲 `functools.wraps` 为什么必须加 |

## 17.1 pathlib：面向对象的路径

**结论先行**：**新代码一律用 `pathlib.Path`，不要再手写字符串拼路径。** `os.path` 是"用字符串表示路径 + 一堆函数"，`pathlib` 是"**路径就是一个对象**，操作是它的方法"。

```python
from pathlib import Path
import tempfile, os

d = Path(tempfile.gettempdir()) / "_ch17_demo"     # ★ 用 / 拼路径，不用 +
d.mkdir(exist_ok=True)
f = d / "a.txt"
f.write_text("你好\npathlib\n", encoding="utf-8")

print(f.exists(), f.is_file())        # True True
print(f.suffix, f.stem, f.name)       # .txt a a.txt
print(f.parent.name)                  # _ch17_demo
print(f.read_text(encoding="utf-8"))  # '你好\npathlib\n'   ← 实际带换行
print(len(f.parts))                   # 8        ← 路径被拆成几段
print(f.with_suffix(".md").name)      # a.md     ← 换后缀，不改变原对象
print([p.name for p in d.glob("*.txt")])   # ['a.txt']
print(f.stat().st_size)               # 17       ← 字节数
print(len(list(d.iterdir())))         # 1
```

**`pathlib` 常用属性/方法速查**：

| 类别 | 成员 | 说明 |
|---|---|---|
| 构造 | `Path("a/b")`、`Path.cwd()`、`Path.home()` | 相对路径、当前目录、用户主目录 |
| 拼接 | `p / "sub" / "f.txt"` | **`/` 运算符重载**，跨平台自动用对分隔符 |
| 取部分 | `p.name` `p.stem` `p.suffix` `p.parent` `p.parts` | 文件名、去后缀、后缀、父目录、各段元组 |
| 变换 | `p.with_name()` `p.with_suffix()` | 返回**新对象**（Path 不可变） |
| 判断 | `p.exists()` `p.is_file()` `p.is_dir()` | 比 `os.path.exists` 更直观 |
| 读写 | `p.read_text(enc)` `p.write_text(s, enc)` `p.read_bytes()` | **一步完成 open+read+close** |
| 目录 | `p.mkdir(parents=True, exist_ok=True)` `p.rmdir()` | `parents=True` 相当于 `makedirs` |
| 遍历 | `p.iterdir()` `p.glob("*.py")` `p.rglob("*.py")` | 直接子项、模式匹配、**递归**匹配 |
| 其他 | `p.resolve()` `p.relative_to()` `p.stat()` `p.unlink()` | 绝对化、相对化、文件信息、删除 |

**与 `os.path` 的对照**（**考试爱考**）：

| 需求 | `os.path` 写法 | `pathlib` 写法 |
|---|---|---|
| 拼路径 | `os.path.join("a", "b.txt")` | `Path("a") / "b.txt"` |
| 取文件名 | `os.path.basename(p)` | `Path(p).name` |
| 取后缀 | `os.path.splitext(p)[1]` | `Path(p).suffix` |
| 取父目录 | `os.path.dirname(p)` | `Path(p).parent` |
| 判断存在 | `os.path.exists(p)` | `Path(p).exists()` |
| 读文本 | `open(p, encoding=...).read()` | `Path(p).read_text(encoding=...)` |
| 递归找文件 | `os.walk()` 手动筛 | `Path(p).rglob("*.py")` |

> **重点：`Path` 对象是不可变的。** `p.with_suffix(".md")` **不会修改 `p`**，而是返回一个新对象。这与第2章"字符串不可变"是同一类设计——**忘了接收返回值就等于什么都没做**：
> ```python
> f.with_suffix(".md")          # ❌ 白写了
> f2 = f.with_suffix(".md")     # ✅
> ```

**跨平台注意**：**Windows 路径里的 `\` 在字符串中是转义符**（第2章），所以写 `"D:\new\test.txt"` 里的 `\n`、`\t` 会变成换行和制表符。三种正确写法：`r"D:\new\test.txt"`（原始字符串）、`"D:/new/test.txt"`（正斜杠，Windows 也认）、`Path("D:/new") / "test.txt"`。

## 17.2 itertools：迭代器工具箱

`itertools` 全部返回**迭代器**（惰性求值，**不占内存**），需要列表时用 `list()` 包一下。

**三个无限迭代器**（**必须用 `islice` 或 `break` 截断，否则死循环**）：

```python
import itertools

print(list(itertools.islice(itertools.count(10, 2), 4)))    # [10, 12, 14, 16]
print(list(itertools.islice(itertools.cycle("AB"), 5)))     # ['A', 'B', 'A', 'B', 'A']
print(list(itertools.repeat("x", 3)))                       # ['x', 'x', 'x']
```

**连接与筛选**：

```python
print(list(itertools.chain([1, 2], "ab", (3,))))            # [1, 2, 'a', 'b', 3]
print(list(itertools.chain.from_iterable([[1, 2], [3]])))   # [1, 2, 3]
print(list(itertools.compress("ABCDEF", [1, 0, 1, 0, 1, 0])))   # ['A', 'C', 'E']
print(list(itertools.dropwhile(lambda x: x < 3, [1, 2, 3, 1, 4])))  # [3, 1, 4]
print(list(itertools.takewhile(lambda x: x < 3, [1, 2, 3, 1, 4])))  # [1, 2]
```

> **`dropwhile` 与 `takewhile` 的坑**：它们**只看"第一次不满足"的位置**，之后不再判断。所以 `dropwhile(x<3, [1,2,3,1,4])` 的结果是 `[3, 1, 4]`——**后面的 `1` 也被保留了**。想筛选全部元素要用 `filter`。

**分组 `groupby`**（**必须先排序**）：

```python
data = "AABBBCC"
print([(k, "".join(g)) for k, g in itertools.groupby(data)])
# [('A', 'AA'), ('B', 'BBB'), ('C', 'CC')]

# ❌ 不排序就分组会得到重复的组
print([(k, len(list(g))) for k, g in itertools.groupby("ABAB")])
# [('A', 1), ('B', 1), ('A', 1), ('B', 1)]
```

**排列组合**（**考试高频，必须分清**）：

```python
print(list(itertools.product("AB", [1, 2])))
# [('A', 1), ('A', 2), ('B', 1), ('B', 2)]        ← 笛卡尔积，可重复取

print(list(itertools.permutations("ABC", 2)))
# [('A','B'), ('A','C'), ('B','A'), ('B','C'), ('C','A'), ('C','B')]   ← 排列，顺序不同算不同

print(list(itertools.combinations("ABC", 2)))
# [('A','B'), ('A','C'), ('B','C')]                ← 组合，顺序不同算相同

print(list(itertools.combinations_with_replacement("AB", 2)))
# [('A','A'), ('A','B'), ('B','B')]                ← 可重复组合
```

| 函数 | 是否讲究顺序 | 是否允许重复 | 数量（n 个取 r 个） |
|---|---|---|---|
| `product` | 讲究 | ✅ | nʳ |
| `permutations` | 讲究 | ❌ | n!/(n−r)! |
| `combinations` | 不讲究 | ❌ | C(n, r) |
| `combinations_with_replacement` | 不讲究 | ✅ | C(n+r−1, r) |

**累积、配对、补齐**：

```python
print(list(itertools.accumulate([1, 2, 3, 4])))              # [1, 3, 6, 10]
print(list(itertools.accumulate([3, 1, 4, 1, 5], max)))      # [3, 3, 4, 4, 5]
print(list(itertools.pairwise([1, 2, 3, 4])))                # [(1, 2), (2, 3), (3, 4)]
print(list(itertools.zip_longest("ab", "123", fillvalue="-")))
# [('a', '1'), ('b', '2'), ('-', '3')]           ← 比内置 zip 会补齐而不是截断
```

> **`pairwise` 的用途**：计算相邻差值、判断"是否递增"、**滑动窗口**。Python 3.10 才有，3.9 及以前要自己用 `zip(a, a[1:])` 实现。

**"分组统计"的经典组合**（比手写字典更清晰）：

```python
import itertools, operator
rows = [("水果", "苹果"), ("水果", "香蕉"), ("蔬菜", "白菜")]
rows.sort(key=operator.itemgetter(0))       # ★ groupby 前必须排序
for k, g in itertools.groupby(rows, key=operator.itemgetter(0)):
    print(k, [x[1] for x in g])
# 水果 ['苹果', '香蕉']
# 蔬菜 ['白菜']
```

## 17.3 collections：专用容器

**这四个容器解决四类"用内置类型很别扭"的问题。**

### Counter：计数器

```python
from collections import Counter

c = Counter("abracadabra")
print(c.most_common(3))        # [('a', 5), ('b', 2), ('r', 2)]
print(c["a"], c["z"])          # 5 0      ← 不存在的键返回 0，不会 KeyError

c2 = Counter("aabbb")
print((c2 + Counter("a")).most_common())    # [('a', 3), ('b', 3)]   ← 支持加减
print(c2.total())              # 5        ← 总数（3.10+）
```

> **对比第4章的手写词频统计**：`Counter(文本)` 一行搞定，比 `d[k] = d.get(k, 0) + 1` 循环清爽得多。**考试如果允许用标准库，优先用 Counter。**

### defaultdict：带默认值的字典

```python
from collections import defaultdict

d = defaultdict(list)
d["a"].append(1)               # ★ 键不存在时自动创建空列表
print(dict(d))                 # {'a': 1} → 实际为 {'a': [1]}

groups = defaultdict(list)
for k, v in [("x", 1), ("y", 3), ("x", 2)]:
    groups[k].append(v)
print(dict(groups))            # {'x': [1, 2], 'y': [3]}
```

**与 `dict.setdefault` 的对比**：

| 写法 | 代码 | 评价 |
|---|---|---|
| `get` | `d[k] = d.get(k, 0) + 1` | 每行都要写默认值 |
| `setdefault` | `d.setdefault(k, []).append(v)` | 好，但**每次都会创建空列表**（即使不用） |
| `defaultdict` | `d[k].append(v)` | **最简洁，且只在该键首次出现时创建** |

### deque：双端队列

```python
from collections import deque

dq = deque([1, 2, 3], maxlen=4)
dq.appendleft(0); dq.append(4); dq.append(5)
print(list(dq))                # [2, 3, 4, 5]   ← maxlen 满了自动从另一端丢弃

dq2 = deque("abcde")
dq2.rotate(2)
print(list(dq2))               # ['d', 'e', 'a', 'b', 'c']
```

> **重点**：**`list.pop(0)` 是 O(n)**（要移动所有元素），**`deque.popleft()` 是 O(1)**。做队列（BFS、任务队列）**必须用 `deque`**。第4章"将列表作为队列使用"一节讲的就是这个问题。`maxlen` 还让它天然适合**滑动窗口**（保留最近 N 条）。

### namedtuple 与 ChainMap

```python
from collections import namedtuple, ChainMap

P = namedtuple("P", "x y")
p = P(1, 2)
print(p, p.x, p.y)             # P(x=1, y=2) 1 2
print(P._fields)               # ('x', 'y')
print(P._make([3, 4]))         # P(x=3, y=4)
print(p._asdict())             # {'x': 1, 'y': 2}

cm = ChainMap({"a": 1}, {"a": 2, "b": 3})
print(dict(cm))                # {'a': 1, 'b': 3}   ← 前面的优先
```

**`namedtuple` vs `dataclass`**（第16章）：`namedtuple` **不可变、内存更小、能直接当元组用**（可解包、可索引）；`dataclass` **可变、能加方法、可读性更好**。**只读的轻量记录用 `namedtuple`，其余用 `dataclass`。**

**`ChainMap` 的用途**：**多层配置查找**（命令行参数 > 环境变量 > 默认配置），查找时前面的层优先，写入只改第一层。

## 17.4 functools：函数工具

### reduce：累积

```python
from functools import reduce
import operator

print(reduce(operator.mul, [1, 2, 3, 4]))          # 24
print(reduce(lambda a, b: a + b, ["a", "b"], "init:"))   # init:ab   ← 第三参数是初值
```

> **注意**：**`reduce` 已经被"降级"了**——Python 3 把它从内置函数移到了 `functools`。**能用 `sum`/`max`/`min`/`math.prod` 就不要用 `reduce`**（可读性差）。

### partial：固化参数

```python
from functools import partial

b2 = partial(int, base=2)          # 固定 base=2
print(b2("1010"))                  # 10
```

**用途**：把"需要 3 个参数的函数"变成"只需要 1 个参数"，好传给 `map`/`sorted(key=...)`/回调。**比 `lambda` 更清晰，而且 pickle 友好**（多进程里 `lambda` 不能序列化，`partial` 可以——第13章的坑）。

### lru_cache：自动缓存

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)

print(fib(30))                     # 832040
print(fib.cache_info())            # CacheInfo(hits=28, misses=31, maxsize=None, currsize=31)
```

**效果**：朴素递归 `fib(30)` 要算 **2692537 次**（**约 269 万次**，不是"约 130 万"），加缓存后**只算 31 次**（每个 n 各一次）。**这是第5章"递归为什么慢"的标准解法。**

> **三个限制**：① **参数必须可哈希**（不能传列表/字典）；② **结果被永久缓存**（数据变了不会自动失效，要 `fib.cache_clear()`）；③ **`maxsize` 默认 128**，超出会按 LRU 淘汰。另外 `lru_cache` 是**线程安全**的。

### wraps：装饰器必加

```python
from functools import wraps

def my_deco(fn):
    @wraps(fn)                     # ★ 必须加
    def wrapper(*a, **kw):
        return fn(*a, **kw)
    return wrapper

@my_deco
def named_fn():
    """文档"""
    return 1

print(named_fn.__name__, repr(named_fn.__doc__))    # named_fn '文档'
```

> **不加 `@wraps` 会怎样？** `named_fn.__name__` 会变成 `"wrapper"`、`__doc__` 变成 `None`——**调试时看不到真实函数名，`help()` 也失效**。第5章讲装饰器时提过，这里是它的正式来源。

### cmp_to_key：老式比较函数转 key

```python
from functools import cmp_to_key

def cmp(a, b):                     # 返回负数/0/正数
    return b - a                   # 降序

print(sorted([1, 3, 2], key=cmp_to_key(cmp)))    # [3, 2, 1]
```

**用途**：**从其他语言（C/Java）移植过来的比较函数**。Python 3 **取消了 `cmp` 参数**，只能这样转。**新代码请直接用 `key=`**（第4章讲过，`key` 比 `cmp` 快得多）。

## 17.5 logging：比 print 更专业的日志

**为什么不用 `print`？** ① 无法分级（调试信息不该出现在生产日志里）；② 无法同时输出到文件；③ 无法自动带时间戳和来源；④ 上线时要一行行删 `print`。

```python
import logging, io

buf = io.StringIO()
h = logging.StreamHandler(buf)
h.setFormatter(logging.Formatter("%(levelname)s|%(name)s|%(message)s"))

log = logging.getLogger("demo")
log.setLevel(logging.DEBUG)        # ★ 必须设级别，否则默认 WARNING 以下不输出
log.addHandler(h)

log.debug("调试信息")
log.info("普通信息")
log.warning("警告")
log.error("错误")
log.critical("严重")

lines = buf.getvalue().strip().split("\n")
print(len(lines))          # 5
print(lines[0])            # DEBUG|demo|调试信息
print(lines[-1])           # CRITICAL|demo|严重
```

**五个级别**（**数字越大越严重**）：

| 级别 | 数值 | 用途 |
|---|---|---|
| `DEBUG` | 10 | 调试细节（变量值、进入哪个分支） |
| `INFO` | 20 | 正常流程（"开始处理 100 条记录"） |
| `WARNING` | 30 | **默认级别**，能跑但有问题（"配置缺失，用默认值"） |
| `ERROR` | 40 | 出错了但程序还能继续 |
| `CRITICAL` | 50 | 致命错误，程序要挂了 |

**两个必知点**：

```python
print(logging.getLogger().level)    # 30  ← 根 logger 默认 WARNING
```

> **① 默认级别是 `WARNING`**，所以 `logging.info("...")` **什么都不输出**——这是初学者最常见的"日志不打印"原因。
> **② `logging.exception()` 会自动附带堆栈**（**只能在 `except` 块里用**）：
> ```python
> try:
>     1 / 0
> except ZeroDivisionError:
>     log.exception("出错了")     # 输出里含 "Traceback" 和 "ZeroDivisionError"
> ```
> 它等价于 `log.error(msg, exc_info=True)`，是**记录异常的标准做法**（比 `print(e)` 强得多，因为保留了调用栈）。

**输出到文件的完整配置**：

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
    filename="app.log",              # 不写 filename 就输出到控制台
    encoding="utf-8",                # ★ 中文日志必须写，否则 Windows 上可能报编码错
    filemode="a",                    # 默认就是追加
)
logging.info("程序启动")             # 写入 app.log，控制台无输出
```

**格式符速查**：`%(asctime)s` 时间、`%(levelname)s` 级别名、`%(name)s` logger 名、`%(message)s` 消息、`%(filename)s`/`%(lineno)d` 文件名与行号、`%(funcName)s` 函数名。

> **工程建议**：**库代码里不要调用 `basicConfig()`**（那是应用层的事），只 `getLogger(__name__)` 然后打日志；**应用入口调用一次 `basicConfig()`**。这是官方推荐的用法。

## 17.6 argparse：把脚本变成命令行工具

**需求**：让 `python calc.py 张三 -n 5 -v` 这种调用方式生效。

```python
import argparse

p = argparse.ArgumentParser(prog="demo", description="演示工具")
p.add_argument("name", help="姓名")                                  # 位置参数（必填）
p.add_argument("-n", "--num", type=int, default=1, help="次数")       # 可选参数
p.add_argument("-v", "--verbose", action="store_true", help="详细输出")  # 开关
p.add_argument("--mode", choices=["a", "b"], default="a", help="模式")

a = p.parse_args(["张三", "-n", "5", "-v"])
print(a.name, a.num, a.verbose, a.mode)      # 张三 5 True a

a2 = p.parse_args(["李四"])                   # 只给位置参数
print(a2.name, a2.num, a2.verbose, a2.mode)  # 李四 1 False a

print(p.format_usage()[:30])                  # usage: demo [-h] [-n NUM] [-v] 
```

**核心概念**：

| 概念 | 写法 | 说明 |
|---|---|---|
| 位置参数 | `add_argument("name")` | **必填**，按顺序对应 |
| 可选参数 | `add_argument("-n", "--num")` | 短选项 + 长选项 |
| 类型转换 | `type=int` | **默认全是字符串**，必须显式转换 |
| 默认值 | `default=1` | 不传时的值 |
| 开关 | `action="store_true"` | 出现即 `True`，不出现 `False` |
| 取值范围 | `choices=["a","b"]` | 非法值**自动报错并退出** |
| 帮助 | `-h` / `--help` | **自动生成**，无需自己写 |
| 互斥 | `add_mutually_exclusive_group()` | 两个参数不能同时给 |

**两个必须知道的坑**：

- **参数非法或缺参数时，`argparse` 会直接 `sys.exit(2)`**——它抛出的是 **`SystemExit`**，不是普通异常。在测试里要 `assertRaises(SystemExit)` 捕获。
- **`type=int` 转换失败也会退出**（不是抛 `ValueError` 给调用方）。

> **对比 `sys.argv`**（第7章）：`sys.argv` 得到的是**字符串列表**，要自己判断长度、自己转换类型、自己写帮助信息。**超过 2 个参数的脚本就该用 `argparse`。**

## 17.7 类型注解

**类型注解（type annotation）让代码自解释，并让 IDE/mypy 提前发现错误。但注意：Python 运行时不会检查它。**

```python
def greet(name: str, times: int = 1) -> str:
    return (str(name) + " ") * times

print(greet.__annotations__)
# {'name': <class 'str'>, 'times': <class 'int'>, 'return': <class 'str'>}

print(greet(123, 2))        # 123 123     ← ★ 传 int 也不报错！注解只是"说明"
```

> **重点：注解不参与运行**。`greet(123, 2)` 完全正常执行——注解的作用对象是**人**和**静态检查工具（mypy、PyCharm）**，不是解释器。这与 C 语言的类型声明**完全不同**（第2章"Python 与 C 的差异"）。

**常用写法**：

```python
from typing import Optional, Union, Any, get_type_hints

def f1(x: int | None) -> list[int]: ...            # 3.10+ 简写（推荐）
def f2(x: Optional[int]) -> "list[int]": ...       # 旧写法 / 前向引用
def f3(x: Union[int, str]) -> Any: ...             # 等价于 int | str

# 变量注解
count: int = 0
names: list[str] = []
scores: dict[str, int] = {}

# 类属性注解
class Student:
    name: str
    age: int = 18

print(get_type_hints(greet))
# {'name': <class 'str'>, 'times': <class 'int'>, 'return': <class 'str'>}
```

| 类型 | 写法 | 说明 |
|---|---|---|
| 列表 | `list[int]` | 3.9+ 可直接下标 |
| 字典 | `dict[str, int]` | 键值类型 |
| 元组（定长） | `tuple[int, str]` | 每个位置一个类型 |
| 可选 | `int \| None` | 可能是 `None` |
| 联合 | `int \| str` | 两者之一 |
| 任意 | `Any` | 放弃检查 |
| 可调用 | `Callable[[int], str]` | 参数为 int，返回 str |

> **实用建议**：**给"公开函数"写注解就够了**（参数 + 返回值），不用给每个局部变量写。注解最大的收益是**IDE 自动补全变准**。想真正做静态检查要装 `mypy`：`pip install mypy` 然后 `mypy your_script.py`。

## 17.8 虚拟环境与包管理

**问题**：不同项目需要同一个库的不同版本（项目 A 要 `pandas 1.x`，项目 B 要 `pandas 2.x`），装在全局就必然冲突。

**虚拟环境（virtual environment）给每个项目一个独立的 `site-packages`。**

```python
import importlib.util, sys, venv

print(importlib.util.find_spec("venv") is not None)      # True
print(importlib.util.find_spec("pip") is not None)       # True
print(sys.prefix == sys.base_prefix)   # True   ← 相等表示"不在虚拟环境中"
print(sys.executable)                  # ...\python.exe
```

> **判断"是否在虚拟环境里"的标准写法就是 `sys.prefix != sys.base_prefix`**（在虚拟环境里两者不同）。在虚拟环境里，命令行提示符前面通常会有 `(venv)` 前缀。

**创建与使用**（在命令行执行，**不是 Python 代码**）：

```
# ① 创建（在项目根目录）
python -m venv venv

# ② 激活
venv\Scripts\activate          # Windows（CMD）
venv\Scripts\Activate.ps1      # Windows（PowerShell）
source venv/bin/activate       # macOS / Linux

# ③ 安装依赖
pip install requests pandas

# ④ 退出
deactivate
```

**`pip` 常用命令**：

| 命令 | 作用 |
|---|---|
| `pip install 包名` | 安装 |
| `pip install 包名==1.2.3` | 装指定版本 |
| `pip install -r requirements.txt` | **按清单批量安装** |
| `pip install -U 包名` | 升级（`-U` = upgrade） |
| `pip uninstall 包名` | 卸载 |
| `pip list` / `pip freeze` | 列出已装包 / **导出精确版本清单** |
| `pip show 包名` | 查看包信息（版本、位置、依赖） |
| `pip download 包名 -d ./pkgs` | 只下载不安装（离线部署用） |

**导出与还原环境**：

```
pip freeze > requirements.txt        # 导出（含所有间接依赖，带版本号）
pip install -r requirements.txt      # 还原
```

**配置国内镜像源**（解决下载慢/超时，第1章讲过）：

```
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
pip config list                       # 查看当前配置
```

常用镜像：清华 `pypi.tuna.tsinghua.edu.cn/simple`、阿里 `mirrors.aliyun.com/pypi/simple`、中科大 `pypi.mirrors.ustc.edu.cn/simple`。

> **三个工程要点**：
> ① **`venv/` 目录不要提交到 Git**（写进 `.gitignore`）——它包含绝对路径和几百 MB 文件，**别人拿到也用不了**；提交 `requirements.txt` 就够了。
> ② **`requirements.txt` 要写版本号**（`pip freeze` 会自动带上 `==`），否则"我这能跑，你那不行"。
> ③ **不要在 `requirements.txt` 里手写 `pip freeze` 之外的内容**——注释可以用 `#`。

## 17.9 JSON 与配置文件

**项目配置的三种存法**（**按复杂度选**）：

| 方式 | 例子 | 适合 | 缺点 |
|---|---|---|---|
| `.py` 模块 | `DEBUG = True` | **Python 项目内部配置（推荐）** | 不能给非 Python 程序读 |
| `.json` | `{"debug": true}` | 需要跨语言读写 | **不能写注释** |
| `.ini` / `.toml` | `[db]` / `host = "x"` | 传统配置 / 现代打包 | 语法稍多 |

**用 JSON 存配置的标准写法**：

```python
import json

cfg = {"name": "demo", "version": "1.0", "debug": True, "tags": ["a", "b"], "port": 8080}

s = json.dumps(cfg, ensure_ascii=False, indent=2, sort_keys=True)
print(len(s.split("\n")))      # 10        ← indent=2 让配置可读
open("config.json", "w", encoding="utf-8").write(s)

back = json.loads(open("config.json", encoding="utf-8").read())
print(back == cfg)             # True      ← 往返无损
```

**三个关键参数**：

| 参数 | 作用 | 不写的后果 |
|---|---|---|
| `ensure_ascii=False` | 中文原样输出 | 变成 `\u59d3\u540d` 看不懂（第9章讲过） |
| `indent=2` | 缩进美化 | 挤成一行，人没法读 |
| `sort_keys=True` | 键排序 | 每次保存顺序都变，**Git diff 全是噪音** |

**自定义对象怎么序列化？** `json` 默认只认 `dict/list/str/int/float/bool/None`：

```python
class Point:
    def __init__(self, x, y): self.x, self.y = x, y

print(json.dumps({"p": Point(1, 2)}, default=lambda o: [o.x, o.y]))
# {"p": [1, 2]}        ← default 回调负责把不认识的对象转成可序列化的形式

try:
    json.dumps({"s": {1, 2}})
except TypeError as e:
    print(e)      # Object of type set is not JSON serializable
```

> **`default` 与 `object_hook` 是一对**：`default` 负责"**编码时**把自定义对象转成基本类型"，`object_hook` 负责"**解码时**把字典转回对象"。**注意 `set`、`tuple`、`datetime`、`Decimal` 都不能直接序列化**——`tuple` 会被转成列表（**读回来是 list 不是 tuple**，这是有损的）。

**安全提醒**：**`json` 是安全的**（只解析数据），而第9章讲过 **`pickle.load` 会执行任意代码，绝不能读不可信来源的文件**。所以**配置文件一律用 JSON/TOML，不要用 pickle**。

## 17.10 调试与断言

**三种调试手段**，按"侵入性"从低到高：

**① 打印调试**（最常用，也最有效）：用 `logging.debug()` 而不是 `print`（上线时改级别即可关闭）。

**② 交互式调试器 `pdb`**：

```python
import pdb
print(hasattr(pdb, "set_trace"))    # True

# 在代码里插入断点（Python 3.7+ 的内置写法）
def buggy(a, b):
    breakpoint()                    # ★ 内置函数，等价于 import pdb; pdb.set_trace()
    return a / b
```

**`pdb` 常用命令**：

| 命令 | 缩写 | 作用 |
|---|---|---|
| `next` | `n` | 执行下一行（**不进入函数**） |
| `step` | `s` | 执行下一行（**进入函数**） |
| `continue` | `c` | 继续到下一个断点 |
| `print(x)` | `p x` | 查看变量 |
| `list` | `l` | 显示当前代码上下文 |
| `where` | `w` | 显示调用栈 |
| `quit` | `q` | 退出调试 |

> **`breakpoint()` 的三个好处**：① 不用 `import pdb`；② 可以用环境变量 `PYTHONBREAKPOINT=0` **一次性禁用所有断点**（避免把断点提交到仓库）；③ IDE 可以接管它。**PyCharm 里更简单：行号旁点一下打断点，然后 `Shift+F9` 调试、`F8` 单步跳过、`F7` 单步进入、`F9` 继续**（第1章、第2章讲过）。

**③ 断言 `assert`**：

```python
def avg(nums):
    assert len(nums) > 0, "列表不能为空"
    return sum(nums) / len(nums)

print(avg([1, 2, 3]))       # 2.0
try:
    avg([])
except AssertionError as e:
    print("断言失败:", e)   # 断言失败: 列表不能为空
```

> **⚠️ 最重要的坑**：**`python -O script.py`（优化模式）会删掉所有 `assert`**，`__debug__` 会变成 `False`。
> ```python
> print(__debug__)     # True   ← 用 -O 运行时是 False
> ```
> **所以 `assert` 只能用于"检查程序自己的逻辑错误"（内部不变式），绝对不能用于校验用户输入、检查文件是否存在、验证密码**——那些必须用 `if + raise`。第10章讲过这一点，这里是它的原理。

**记录异常的规范写法**：

```python
import logging
log = logging.getLogger(__name__)

try:
    x = 1 / 0
except ZeroDivisionError:
    log.exception("计算失败")       # ★ 保留完整堆栈，比 print(e) 强得多
```

## 17.11 项目结构与打包

**一个标准的多文件 Python 项目**：

```
myproject/
├── README.md                 说明文档
├── requirements.txt          依赖清单
├── pyproject.toml            项目元数据与构建配置
├── .gitignore                忽略 venv/、__pycache__/、*.log
├── src/
│   └── mypkg/
│       ├── __init__.py       包标识（第7章）
│       ├── core.py           业务逻辑
│       └── utils.py          工具函数
├── tests/
│   └── test_core.py          单元测试（第10章）
├── data/                     数据文件
└── main.py                   程序入口
```

**`pyproject.toml` 最小示例**（**现代 Python 项目的标准配置文件**，取代了老的 `setup.py`）：

```toml
[project]
name = "myproject"
version = "0.1.0"
description = "示例项目"
requires-python = ">=3.10"
dependencies = ["requests>=2.31", "pandas"]

[project.optional-dependencies]
dev = ["pytest", "mypy"]

[build-system]
requires = ["setuptools>=61"]
build-backend = "setuptools.build_meta"
```

**为什么用它？** ① 声明依赖（`pip install .` 时自动装）；② 声明 Python 版本要求；③ 工具配置（`[tool.black]`、`[tool.pytest.ini_options]`）集中在一个文件；④ **TOML 支持注释**，比 JSON 适合当配置文件。

**打包成 exe（`PyInstaller`，第7章 7.3.6 讲过）**：

```
pip install pyinstaller
pyinstaller -F -w -n myapp main.py     # -F 单文件、-w 不显示控制台、-n 指定名字
```

**四个常见坑**：① **必须打包成 `-F` 时启动慢**（每次解压到临时目录）；② **数据文件要用 `--add-data` 显式带上**，代码里的相对路径在 exe 里会变；③ **`-w` 后 `print` 看不到输出**（调试时先不加 `-w`）；④ **杀毒软件误报**（PyInstaller 的引导程序常被误判）。

**环境信息查询**（写跨平台代码时常用）：

```python
import os, sys
print(os.name, sys.platform)                    # nt win32
print(sys.getdefaultencoding())                 # utf-8
print(sys.getfilesystemencoding())              # utf-8
```

> **跨平台注意**：**Windows 上文本文件默认换行是 `\r\n`**（第9章 CSV 的 `newline=""` 就是处理它）；**Linux/macOS 是 `\n`**。写跨平台脚本时**统一用 `encoding="utf-8"` 和 `newline=""`**（写 CSV 时）。

## 17.12 PEP 8 与工程规范

第1章 1.4.3 讲过 PEP 8 的基础（缩进 4 空格、行长 79 等），这里补**工程层面的四条**：

**① 导入顺序**（**三组，组间空一行，组内按字母序**）：

```python
# 1. 标准库
import json
import os
from pathlib import Path

# 2. 第三方库
import numpy as np
import pandas as pd

# 3. 本地模块
from mypkg import core
from mypkg.utils import helper
```

> **为什么重要？** 一眼能看出"这个项目依赖了哪些外部库"。**禁止 `from module import *`**（污染命名空间，第7章讲过）。

**② 命名规范**：

| 对象 | 规范 | 例子 |
|---|---|---|
| 模块/包 | 全小写，可加下划线 | `data_utils.py` |
| 函数/变量 | 小写下划线（snake_case） | `get_user_name` |
| 类 | 大驼峰（PascalCase） | `StudentInfo` |
| 常量 | 全大写下划线 | `MAX_RETRY = 3` |
| "私有" | 前置一个下划线 | `_internal()` |
| 避免冲突 | 后置一个下划线 | `class_`、`id_` |

**③ 文档字符串（docstring）**：**公开的函数、类、模块都应该有**。

```python
def area(w: float, h: float) -> float:
    """计算矩形面积。

    Args:
        w: 宽，必须为正数
        h: 高，必须为正数

    Returns:
        面积值

    Raises:
        ValueError: 当 w 或 h 不为正数时
    """
    if w <= 0 or h <= 0:
        raise ValueError("宽高必须为正数")
    return w * h

print(area(3, 4))       # 12
print(area.__doc__.split("\n")[0])   # 计算矩形面积。
```

> **`help(area)` 和 IDE 的悬浮提示读的就是 `__doc__`**。写文档字符串的收益远大于成本。

**④ 用工具自动检查**（**别靠人眼**）：

| 工具 | 作用 | 命令 |
|---|---|---|
| `ruff` / `flake8` | 代码风格检查 | `ruff check .` |
| `black` | 自动格式化 | `black .` |
| `mypy` | 类型检查 | `mypy src/` |
| `pytest` | 测试框架（比 `unittest` 简洁） | `pytest` |

> **工程建议**：**写代码时不用想格式，交给 `black` 一键格式化**。`ruff` 甚至能自动修复大部分问题（`ruff check --fix .`）。这是"作业代码"和"团队代码"最直观的差别。

## 典型例题

**例 17.1**（题型：编程题 —— pathlib 批量整理文件）

**题目**：把 `data/` 目录下所有 `.txt` 文件重命名为 `backup_原名.txt`，并把 `.log` 文件的字节数汇总输出。

**解**：

```python
from pathlib import Path

def organize(data_dir: str) -> tuple[int, int]:
    d = Path(data_dir)
    if not d.is_dir():
        raise NotADirectoryError(f"不是目录：{d}")

    renamed = 0
    for f in d.glob("*.txt"):                    # 只匹配 txt
        if f.name.startswith("backup_"):         # 避免重复加前缀
            continue
        f.rename(f.with_name("backup_" + f.name))
        renamed += 1

    total_bytes = sum(f.stat().st_size for f in d.glob("*.log"))
    return renamed, total_bytes

# 演示
import tempfile
d = Path(tempfile.gettempdir()) / "_ch17_ex1"
d.mkdir(exist_ok=True)
(d / "a.txt").write_text("hello", encoding="utf-8")
(d / "b.txt").write_text("world!!", encoding="utf-8")
(d / "app.log").write_text("log line\n", encoding="utf-8")

print(organize(str(d)))                              # (2, 10)
print(sorted(p.name for p in d.glob("*.txt")))
# ['backup_a.txt', 'backup_b.txt']
```

**答案**：`(2, 10)`（重命名 2 个，日志共 **10** 字节）；目录里出现 `backup_a.txt`、`backup_b.txt`。**注意那个 10 不是 9**：字符串 `"log line\n"` 只有 9 个字符，但 `write_text(..., encoding="utf-8")` 走的是**文本模式**，Windows 上会把 `\n` 写成 **`\r\n`**，落盘就是 **10 字节**——和第 9 章 `tell()` 那个坑是同一回事。**要点**：① **`f.with_name("backup_" + f.name)` 生成新路径，`f.rename()` 执行重命名**；② **要防止重复加前缀**（否则跑两次变成 `backup_backup_a.txt`；加了 `startswith("backup_")` 判断后，实测第二次返回 `(0, 10)`）；③ **`sum(f.stat().st_size for f in ...)` 是生成器表达式**（第4章），不占内存。

**例 17.2**（题型：读程序写结果 —— itertools 组合）

```python
import itertools

print(len(list(itertools.combinations("ABCDE", 3))))
print(list(itertools.combinations_with_replacement("AB", 3)))
print([x for x in itertools.permutations([1, 2, 3], 2) if x[0] < x[1]])
print(list(itertools.islice(itertools.accumulate(itertools.count(1)), 5)))
```

**解**：
- `combinations("ABCDE", 3)`：从 5 个里取 3 个，C(5,3) = 5!/(3!2!) = **10**。
- `combinations_with_replacement("AB", 3)`：从 2 个里可重复取 3 个，C(2+3−1, 3) = C(4,3) = 4 组：`('A','A','A')`、`('A','A','B')`、`('A','B','B')`、`('B','B','B')`。
- `permutations([1,2,3], 2)` 有 6 个，筛出第一个元素小于第二个的：`(1,2)`、`(1,3)`、`(2,3)`。
- `count(1)` 从 1 开始递增，`accumulate` 做累加得 1, 3, 6, 10, 15，取前 5 个。

**答案**：
```
10
[('A', 'A', 'A'), ('A', 'A', 'B'), ('A', 'B', 'B'), ('B', 'B', 'B')]
[(1, 2), (1, 3), (2, 3)]
[1, 3, 6, 10, 15]
```
**要点**：**`combinations` 不讲究顺序、不允许重复；`permutations` 讲究顺序；`product` 讲究顺序且允许重复**——三者必须分清。

**例 17.3**（题型：编程题 —— Counter + defaultdict 做词频与分组）

**题目**：统计一段英文里出现次数最多的 3 个单词（忽略大小写和标点），并按"首字母"分组输出。

**解**：

```python
import re
from collections import Counter, defaultdict

text = "The quick brown Fox. The FOX jumps; the fox runs!"

words = re.findall(r"[a-zA-Z]+", text.lower())     # 第8章：只取字母并转小写
c = Counter(words)
print(c.most_common(3))
# [('the', 3), ('fox', 3), ('quick', 1)]

groups = defaultdict(list)
for w in sorted(set(words)):                        # 去重 + 排序，输出才稳定
    groups[w[0]].append(w)
print({k: groups[k] for k in sorted(groups)})
# {'b': ['brown'], 'f': ['fox'], 'j': ['jumps'], 'q': ['quick'], 'r': ['runs'], 't': ['the']}
```

**答案**：`[('the', 3), ('fox', 3), ('quick', 1)]` 与上面那个按首字母分组的字典。**注意 `the` 是 3 次不是 4 次**——原句里 `The`、`The`、`the` 各出现一次，`text.lower()` 之后合并成 3 次；**`fox` 也是 3 次**（`Fox`、`FOX`、`fox`），所以前两名并列。**要点**：① **`re.findall(r"[a-zA-Z]+", ...)` 一次完成"分词 + 去标点"**，比 `split()` 后再 `strip(".,;!")` 可靠；② **`Counter.most_common(n)` 直接给出前 n 名**（不用自己排序，**并列时按首次出现顺序**）；③ **`sorted(set(words))` 保证输出顺序稳定**（`set` 是无序的，第4章讲过）。

**例 17.4**（题型：编程题 —— lru_cache 加速递归）

**题目**：用递归计算"爬楼梯"问题：一次可以走 1 级或 2 级，求走 n 级有多少种走法。要求 n=35 时能秒出结果。

**解**：

```python
from functools import lru_cache
import time

def climb_slow(n):                       # 朴素递归：指数级
    return n if n <= 2 else climb_slow(n - 1) + climb_slow(n - 2)

@lru_cache(maxsize=None)
def climb(n):                           # 加缓存：线性
    return n if n <= 2 else climb(n - 1) + climb(n - 2)

t0 = time.perf_counter()
print("慢:", climb_slow(28), f"{time.perf_counter()-t0:.3f}s")
t0 = time.perf_counter()
print("快:", climb(35), f"{time.perf_counter()-t0:.6f}s")
print(climb.cache_info())
```

**答案**：`climb_slow(28)` = **514229**（本机约 **0.036 秒**，n 再大就明显卡；**317811 是 `climb_slow(27)`，别记混**）；`climb(35)` = **14930352**（**微秒级**，本机约 0.00005 秒）；`cache_info()` 显示 **`CacheInfo(hits=32, misses=35, maxsize=None, currsize=35)`**（**`misses` 是 35 不是 36**——n 从 1 到 35 各算一次，正好 35 次）。**要点**：① **`lru_cache` 把"指数级递归"变成"线性"**，因为每个子问题只算一次；② **`climb(35)` 用朴素递归需要约 3000 万次调用**，根本跑不动；③ **缓存的键是参数**，所以参数必须可哈希。

**例 17.5**（题型：编程题 —— 用 argparse + logging 写一个完整工具）

**题目**：写一个"文本行数统计工具"：接受文件路径（位置参数）、`-v` 详细模式、`--encoding` 指定编码（默认 utf-8，只能是 utf-8/gbk）；用 `logging` 输出结果，详细模式下同时输出每行的长度。

**解**：

```python
import argparse, logging
from pathlib import Path

def build_parser() -> argparse.ArgumentParser:
    p = argparse.ArgumentParser(prog="wcl", description="统计文本行数")
    p.add_argument("path", help="文件路径")
    p.add_argument("-v", "--verbose", action="store_true", help="详细输出")
    p.add_argument("--encoding", choices=["utf-8", "gbk"], default="utf-8")
    return p

def main(argv=None) -> int:
    args = build_parser().parse_args(argv)
    logging.basicConfig(level=logging.DEBUG if args.verbose else logging.INFO,
                        format="%(levelname)s %(message)s")

    f = Path(args.path)
    if not f.is_file():
        logging.error("文件不存在：%s", f)
        return 1

    lines = f.read_text(encoding=args.encoding).splitlines()
    logging.info("共 %d 行", len(lines))
    if args.verbose:
        for i, line in enumerate(lines, 1):
            logging.debug("第 %d 行：%d 个字符", i, len(line))
    return 0

if __name__ == "__main__":
    raise SystemExit(main(["demo.txt", "-v"]))     # 演示用固定参数
```

**答案**：`-v` 时输出 `INFO 共 N 行` 加每行的 `DEBUG 第 i 行：k 个字符`；不带 `-v` 只输出 `INFO` 那一行（**因为 `logging.basicConfig(level=INFO)` 会过滤掉 DEBUG**）；文件不存在时输出 `ERROR 文件不存在：...` 并返回退出码 1。**要点**：① **`main(argv=None)` 接收参数列表**，便于测试时注入；② **`return` 整数作为退出码，用 `raise SystemExit(main())` 传递**（比 `sys.exit()` 更规范）；③ **`logging.info("共 %d 行", n)` 用惰性格式化**——比 f-string 好，因为级别不够时不会白做字符串拼接；④ **`splitlines()` 比 `split("\n")` 好**，能正确处理 `\r\n` 且不会多出一个空串（第9章的坑）。

**例 17.6**（题型：读程序写结果 —— 综合）

```python
from collections import Counter
from functools import reduce
import itertools

print(Counter("aabbbcc").most_common(2))
print(reduce(lambda a, b: a * b, range(1, 6)))
print(list(itertools.islice(itertools.cycle([1, 2]), 5)))
print(list(itertools.pairwise([10, 20, 30])))
print(list(itertools.compress([1, 2, 3, 4], [1, 0, 1, 1])))
```

**解**：
- `Counter("aabbbcc")` 计数得 a:2、b:3、c:2，`most_common(2)` 取前两名：`b` 3 次，然后 `a` 和 `c` 都是 2 次——**`Counter` 在计数相同时按"首次出现的顺序"**，`a` 先出现，所以是 `[('b', 3), ('a', 2)]`。
- `reduce(mul, 1..5)` = 1×2×3×4×5 = **120**（即 5!）。
- `cycle([1,2])` 无限重复，取前 5 个：`[1, 2, 1, 2, 1]`。
- `pairwise([10,20,30])` 得相邻对：`[(10, 20), (20, 30)]`。
- `compress` 按选择器挑：位置 0、2、3 被选中 → `[1, 3, 4]`。

**答案**：
```
[('b', 3), ('a', 2)]
120
[1, 2, 1, 2, 1]
[(10, 20), (20, 30)]
[1, 3, 4]
```
**要点**：① **`most_common` 在计数相同时保持"先出现的排前面"**（Python 3.7+ 字典有序，所以是确定的）；② `cycle` 是**无限迭代器**，直接 `list()` 会死机，**必须用 `islice` 截断**；③ `compress` 的选择器长度可以比数据短（缺的按 0 处理）。

## 易错点提醒

1. **`Path` 方法忘了接收返回值** → 错：`p.with_suffix(".md")` 单独一行**什么都不做**（Path 不可变）。对：`p2 = p.with_suffix(".md")`。
2. **用 `+` 拼路径** → 错：`"data" + "/" + "a.txt"` 在 Windows 上可能得到混合分隔符，还容易漏斜杠。对：**`Path("data") / "a.txt"`**。
3. **`itertools.count()` / `cycle()` 直接 `list()`** → 错：**无限迭代器，内存爆掉（死机）**。对：用 **`islice(..., n)`** 截断，或配合 `break`。
4. **`groupby` 之前不排序** → 错：相同键会被拆成多个组（`"ABAB"` 得到 4 组）。对：**先 `sort(key=...)` 再 `groupby`**。
5. **`dropwhile` 当成 `filter` 用** → 错：`dropwhile` 只丢"开头连续满足条件"的部分，**后面再出现符合条件的元素不会被丢掉**。对：全量筛选用 `filter`。
6. **`Counter[k]` 以为会 `KeyError`** → 错：**不存在的键返回 0**（不报错）。这很方便，但**也因此掩盖了拼写错误**——要区分"计数为 0"和"键不存在"时用 `k in c`。
7. **`list.pop(0)` 当队列用** → 错：**O(n)**，数据量大时极慢。对：用 **`collections.deque`** 的 `popleft()`（O(1)）。
8. **`defaultdict` 读不存在的键** → 错：`d["x"]` **会创建这个键**（并写入默认值），导致字典被意外撑大。对：**只读时用 `d.get("x")` 或先 `if k in d`**。
9. **装饰器忘了 `@functools.wraps`** → 错：被装饰函数的 `__name__` 变成 `"wrapper"`、`__doc__` 变成 `None`。对：**`@wraps(fn)` 是装饰器的标配**。
10. **`lru_cache` 的参数里有列表/字典** → 错：`TypeError: unhashable type: 'list'`。对：**先转成元组**再传，或改成不用缓存。
11. **`logging.info()` 不输出** → 错：**根 logger 默认级别是 `WARNING`**，`DEBUG`/`INFO` 被过滤。对：**`logging.basicConfig(level=logging.INFO)`** 或 `logger.setLevel(...)`。
12. **`logging.exception()` 用在 `except` 块外** → 错：拿不到异常信息，输出 `NoneType: None`。对：**只在 `except` 块里用它**（或显式 `exc_info=True`）。
13. **`argparse` 参数不写 `type=int`** → 错：**所有参数都是字符串**，`args.num + 1` 会变成字符串拼接或报错。对：`add_argument("-n", type=int)`。
14. **以为类型注解会做运行时检查** → 错：**`greet(123)` 传错类型照样运行**。对：注解只是文档 + 静态检查依据；真要运行时校验必须自己 `isinstance` + `raise`。
15. **`assert` 用来校验用户输入** → 错：**`python -O` 会删掉所有 `assert`**，校验完全失效。对：**`if ...: raise ValueError(...)`**。
16. **把 `venv/` 提交到 Git** → 错：包含绝对路径和大量文件，**换台机器就失效**。对：`.gitignore` 里加 `venv/`，**只提交 `requirements.txt`**。
17. **`requirements.txt` 不写版本号** → 错：依赖升级后"昨天还能跑，今天报错"。对：**`pip freeze > requirements.txt`**（自动带 `==版本`）。
18. **`json.dump` 忘了 `ensure_ascii=False`** → 错：中文变成 `\u59d3\u540d`，配置文件没法看。对：写配置时固定用 `ensure_ascii=False, indent=2, sort_keys=True`。
19. **用 `pickle` 存配置文件** → 错：**`pickle.load` 会执行任意代码**，配置文件一旦被篡改就是任意代码执行漏洞（第9章）。对：**配置用 JSON/TOML**。
20. **在库代码里调用 `logging.basicConfig()`** → 错：会抢走应用层的日志配置，导致使用方无法自定义。对：**库只 `getLogger(__name__)`，`basicConfig()` 留给程序入口**。

## 本章小结

- **`pathlib.Path` 是新代码的唯一选择**：用 `/` 拼路径、用属性取各部分、`read_text`/`write_text` 一步读写、`rglob` 递归匹配；**Path 不可变，忘了接收返回值等于没写**。
- **`itertools` 全是惰性迭代器**：`count`/`cycle`/`repeat` 是**无限**的必须 `islice` 截断；**`combinations` 不讲顺序不重复、`permutations` 讲顺序、`product` 讲顺序且可重复**；**`groupby` 前必须排序**；`dropwhile`/`takewhile` 只看"第一次不满足"的位置。
- **`collections` 四件套**：`Counter` 做词频（`most_common(n)`，不存在的键返回 0）、`defaultdict(list)` 做分组（**只读时别用 `[]`，会误建键**）、`deque` 做队列和滑动窗口（**`list.pop(0)` 是 O(n)**）、`namedtuple`/`ChainMap` 做只读记录与多层配置。
- **`functools`**：`lru_cache` 把指数级递归变线性（参数必须可哈希、缓存不会自动失效）；`partial` 固化参数（比 `lambda` 更适合多进程）；**`@wraps` 是装饰器标配**；`cmp_to_key` 只为移植老代码。
- **`logging` 替代 `print`**：五个级别、**默认级别是 `WARNING`**、`logging.exception()` 自动带堆栈（**只能在 `except` 里用**）、写文件要加 `encoding="utf-8"`、**库代码不要调 `basicConfig()`**。
- **`argparse` 把脚本变成工具**：位置参数 + `-n/--num` + `type=int` + `default` + `action="store_true"` + `choices`；**参数错误时抛 `SystemExit` 而不是普通异常**；`-h` 自动生成。
- **类型注解是"文档 + 静态检查依据"，运行时完全不检查**；`int | None` 是 3.10+ 的推荐写法。
- **虚拟环境解决版本冲突**：`python -m venv venv` → 激活 → `pip install`；`sys.prefix != sys.base_prefix` 表示在虚拟环境里；**`venv/` 不进 Git，`requirements.txt` 进 Git 且必须带版本号**。
- **配置优先用 JSON/TOML，绝不用 pickle**；写 JSON 固定加 `ensure_ascii=False, indent=2, sort_keys=True`。
- **调试三件套**：`logging.debug`（可关）、`breakpoint()`/pdb（`n`/`s`/`c`/`p`/`l`/`w`/`q`）、PyCharm 的 F8/F7/F9；**`assert` 会被 `python -O` 删掉，不能用于校验用户输入**。
- **项目结构**：`src/` 放包、`tests/` 放测试、`pyproject.toml` 声明元数据与依赖、`.gitignore` 忽略 `venv/` 和 `__pycache__/`。
- **工程规范**：导入分三组（标准库/第三方/本地）、命名遵守 snake_case/PascalCase/UPPER_CASE、公开函数写文档字符串、**格式交给 `black`、检查交给 `ruff`、类型交给 `mypy`**。

> **一句话记住本章**：**路径用 `pathlib`、组合用 `itertools`、计数用 `Counter`、队列用 `deque`、缓存用 `lru_cache`、输出用 `logging`、入口用 `argparse`——标准库够用，就别急着 `pip install`。**

## 自测题

1. `pathlib` 与 `os.path` 相比，最大的设计差别是什么？`p.with_suffix(".md")` 会修改 `p` 吗？
2. 写出"从 5 个元素里取 3 个的所有组合"和"所有排列"的 `itertools` 调用，并说明两者数量的公式。
3. `itertools.count()` 直接 `list()` 会怎样？正确用法是什么？
4. `groupby` 之前为什么必须排序？不排序会得到什么结果？
5. `Counter("aab")["z"]` 的结果是什么？这会带来什么风险？
6. 为什么做队列要用 `deque` 而不是 `list`？`deque(maxlen=3)` 有什么特性？
7. `defaultdict(list)` 的 `d["x"]` 在键不存在时会发生什么？只读场景应该怎么写？
8. `lru_cache` 给递归带来多大的加速？它有三个什么限制？
9. 装饰器不加 `@functools.wraps` 会有什么后果？
10. `logging.info("hi")` 为什么默认不输出？怎么改？
11. `logging.exception()` 和 `logging.error()` 有什么区别？前者有什么使用限制？
12. `argparse` 里参数不写 `type=int` 会怎样？参数非法时抛出的是什么？
13. 类型注解在运行时会检查吗？给一个能证明你答案的例子。
14. `assert` 为什么不能用来校验用户输入？
15. 怎么判断当前 Python 是否运行在虚拟环境里？为什么 `venv/` 目录不能提交到 Git？
16. 写 JSON 配置时，`ensure_ascii=False, indent=2, sort_keys=True` 三个参数各解决什么问题？

**参考答案**

1. **`os.path` 用字符串表示路径 + 一堆函数操作它；`pathlib` 把路径封装成对象，操作是它的方法**（还能用 `/` 运算符拼路径）。**`p.with_suffix(".md")` 不会修改 `p`**——`Path` 是**不可变**的，它返回一个新对象，不接收返回值就等于什么都没做。
2. 组合：`itertools.combinations(seq, 3)`，数量 **C(5,3) = 5!/(3!·2!) = 10**。排列：`itertools.permutations(seq, 3)`，数量 **5!/(5−3)! = 60**。区别是**排列讲究顺序**（`(a,b)` 与 `(b,a)` 算两个），组合不讲究。
3. **会死机**（无限迭代器，`list()` 会一直往内存里塞元素直到耗尽内存）。正确用法：**`itertools.islice(itertools.count(), n)` 截断**，或用 `for ... in count(): ... break`，或 `zip(count(), seq)` 让它被短的那个截断。
4. 因为 **`groupby` 只合并"相邻的"相同键**。不排序时 `"ABAB"` 会得到 4 个组 `[('A',1), ('B',1), ('A',1), ('B',1)]`，而不是 2 个组。**必须先 `sort(key=...)` 再 `groupby`。**
5. 结果是 **`0`**（不报 `KeyError`）。风险是**掩盖拼写错误**：`c["a"]` 写成 `c["A"]` 也会静默返回 0，看起来"这个字母没出现"。要区分"计数为 0"和"键不存在"，用 `"z" in c` 判断。
6. 因为 **`list.pop(0)` 需要移动后面所有元素，复杂度 O(n)**；**`deque.popleft()` 是 O(1)**。`deque(maxlen=3)` 在装满后再 `append` 会**自动从另一端丢弃最老的元素**，所以天然适合做"只保留最近 N 条"的滑动窗口。
7. **会自动创建这个键**并把默认值（空列表）写进字典——即使你只是想读。**只读场景要用 `d.get("x")`**（返回 `None` 或指定默认值，不创建键），或先 `if "x" in d`。
8. 把"**指数级**递归变成**线性**"：朴素 `fib(30)` 要算 **2692537 次（约 269 万）**，加缓存后**只算 31 次**（每个 n 一次）。三个限制：① **参数必须可哈希**（不能传列表/字典）；② **缓存不会自动失效**（数据变了要手动 `cache_clear()`）；③ **`maxsize` 默认 128**，超出按 LRU 淘汰。
9. **被装饰函数的元信息被覆盖**：`__name__` 变成 `"wrapper"`、`__doc__` 变成 `None`。后果是调试时看到的函数名是错的、`help()` 显示不出文档、日志和追踪信息失去意义。**加 `@functools.wraps(fn)` 解决。**
10. 因为**根 logger 的默认级别是 `WARNING`（30）**，`INFO`（20）比它低，被过滤掉了。改法：**`logging.basicConfig(level=logging.INFO)`**（应用入口调一次），或对具体 logger 调 `logger.setLevel(logging.INFO)`。
11. **`logging.exception(msg)` 等价于 `logging.error(msg, exc_info=True)`，会自动把当前异常的完整堆栈（Traceback）附加到日志里**；`logging.error(msg)` 只记录一条错误消息。**限制：它必须在 `except` 块内调用**，否则取不到异常信息（会输出 `NoneType: None`）。
12. **不写 `type=int` 时所有参数都是字符串**，`args.num + 1` 会变成字符串拼接（或抛 `TypeError`）。参数非法（缺必填参数、`choices` 不在范围内、`type` 转换失败）时，**`argparse` 打印用法到 stderr 并抛出 `SystemExit`（退出码 2）**，不是普通异常——所以测试时要 `assertRaises(SystemExit)`。
13. **不会检查。** 例子：`def greet(name: str) -> str: return (str(name) + " ") * 2`，调用 `greet(123, 2)` **完全正常运行**。注解只对**人**和**静态检查工具（mypy、IDE）**有意义，解释器只在 `__annotations__` 里保存它。
14. 因为 **`python -O script.py`（优化模式）会把所有 `assert` 语句整个删掉**，此时校验逻辑完全失效（`__debug__` 也变成 `False`）。所以 `assert` 只能用来检查"**程序自己的逻辑不变式**"，**校验用户输入/文件是否存在必须用 `if ...: raise ValueError(...)`**。
15. **判断方法：`sys.prefix != sys.base_prefix` 就表示在虚拟环境里**（相等则不在）。`venv/` 不能提交是因为：① 里面包含**创建时的绝对路径**，换机器/换目录就失效；② 包含完整解释器副本和几百 MB 文件；③ 依赖的版本信息用 `requirements.txt` 表达就够了。**`venv/` 写进 `.gitignore`，`requirements.txt` 提交。**
16. **`ensure_ascii=False`**：让中文原样输出，而不是变成 `\u59d3\u540d`（可读性）。**`indent=2`**：让 JSON 分行缩进，人能读懂（不写就挤成一行）。**`sort_keys=True`**：让键按固定顺序输出，**避免每次保存顺序都变导致 Git diff 充满噪音**。
