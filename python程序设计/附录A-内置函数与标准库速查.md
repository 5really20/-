# 附录A 内置函数与标准库速查

> 配套教材：王雷春《Python 程序设计——从基础入门到实战应用（微课版）（第2版）》｜适用于全部章节｜**考试与上机随时翻阅**

## 使用说明

- **本附录只给"查得到"的东西，不解释原理。** 原理请回看对应章节。
- 每张表的四列含义：**语法**（怎么调用）｜**作用**（干什么）｜**例子 → 返回**（可运行，含真实结果）｜**备注**（坑与提示）。
- 带 ★ 的是**考试与上机高频**，带 ⚠️ 的是**易错**。
- 表中所有示例的结果均为 Python 3.12 实测。
- **列名速记**：`s` 字符串、`lst` 列表、`tup` 元组、`d` 字典、`st` 集合、`f` 文件对象、`n` 数字、`seq` 任意序列、`it` 任意可迭代对象。
- **返回总目录**：[00-课程总览与索引.md](00-课程总览与索引.md)（查不到的内容，先到索引的「章节地图」定位到对应章节）。

---

## 一、内置函数速查

### 1.1 输入输出

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `input(prompt)` | 读一行输入 | `input("姓名：")` 输入 `张三` → `'张三'` | ⚠️ **永远返回字符串**，要数字必须 `int()` |
| ★ `print(*obj, sep, end, file, flush)` | 输出 | `print(1, 2, sep="-")` → `1-2` | `end` 默认 `'\n'`；`file=f` 写入文件 |
| `print(f"{x=}")` | 调试输出变量名和值 | `name="zhang"; print(f"{name=}")` → `name='zhang'` | 3.8+，调试神器 |
| `repr(obj)` | 得到"给开发者看"的字符串 | `repr("a\nb")` → `"'a\\nb'"` | 会显示引号和转义符 |
| `ascii(obj)` | 非 ASCII 字符转义 | `ascii("中")` → `"'\\u4e2d'"` | 调试编码问题用 |
| `format(v, spec)` | 按格式说明符格式化 | `format(3.14159, ".2f")` → `'3.14'` | 等价于 f-string 的 `{v:.2f}` |

### 1.2 类型与判断

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `type(obj)` | 返回对象的**类型** | `type(True) == int` → `False` | ⚠️ 不认子类关系 |
| ★ `isinstance(obj, cls)` | 判断是否属于某类型（**含子类**） | `isinstance(True, int)` → `True` | ✅ **优先用它**；第二参数可为元组 |
| `issubclass(A, B)` | A 是否是 B 的子类 | `issubclass(bool, int)` → `True` | 只对类用 |
| ★ `id(obj)` | 对象的内存地址（整数） | `a=1; id(a) == id(1)` → `True` | 判断"是不是同一个对象" |
| ★ `hash(obj)` | 哈希值 | `hash("a")` → 随机化的整数 | ⚠️ 可变对象（list/dict/set）**不可哈希** |
| `callable(obj)` | 能否被调用 | `callable(len)` → `True` | 判断是不是函数 |
| `hasattr/getattr/setattr/delattr` | 属性操作 | `hasattr("a", "upper")` → `True` | 反射四件套 |

### 1.3 类型转换

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `int(x, base)` | 转整数 | `int("1010", 2)` → `10`；`int(3.9)` → `3` | ⚠️ **截断**不是四舍五入；`int(-3.9)` → `-3` |
| ★ `float(x)` | 转浮点 | `float("3.14")` → `3.14` | `float("inf")`、`float("nan")` 也合法 |
| `complex(re, im)` | 转复数 | `complex(1, 2)` → `(1+2j)` | 虚部用 `j` 表示 |
| ★ `str(x)` | 转字符串 | `str(3.0)` → `'3.0'` | 任何对象都能转 |
| ★ `bool(x)` | 转布尔 | `bool("0")` → `True`；`bool("")` → `False` | ⚠️ **非空字符串一律 True** |
| ★ `list(seq)` | 转列表 | `list("abc")` → `['a','b','c']` | 可迭代对象都能转 |
| ★ `tuple(seq)` | 转元组 | `tuple([1,2])` → `(1, 2)` | |
| ★ `set(seq)` | 转集合（去重） | `set([1,1,2])` → `{1, 2}` | ⚠️ 无序，不能下标访问 |
| ★ `dict(**kw)` | 转字典 | `dict([("a",1)])` → `{'a': 1}` | 也接受关键字参数 |
| ★ `chr(i)` / `ord(c)` | 码位 ↔ 字符 | `ord("A")` → `65`；`chr(65)` → `'A'` | 中文也适用：`ord("中")` → `20013` |
| `bin/oct/hex(n)` | 转二/八/十六进制字符串 | `hex(255)` → `'0xff'`；`bin(10)` → `'0b1010'` | 带前缀 |
| ★ `eval(s)` | 把字符串当**表达式**求值 | `eval("1+2*3")` → `7` | ⚠️ **有安全风险**，见下 |
| `exec(s)` | 把字符串当**语句**执行 | `exec("x=1")` | 无返回值，⚠️ 同样危险 |
| `compile(src, fn, mode)` | 预编译代码 | 配合 `eval`/`exec` 用 | 重复执行同一段代码时可提速 |
| ★ `ast.literal_eval(s)` | **安全地**解析字面量 | `ast.literal_eval("[1,2]")` → `[1, 2]` | ✅ **替代 `eval` 处理用户输入** |

> **⚠️ `eval` 的安全警告**：`eval("__import__('os').system('rm -rf /')")` 会**真的执行系统命令**。**任何来自用户输入的内容都不能直接 `eval`**——解析数字用 `int/float`，解析列表/字典用 **`ast.literal_eval`**。

### 1.4 数学与统计

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `abs(x)` | 绝对值 | `abs(-5)` → `5`；`abs(3+4j)` → `5.0` | 复数返回**模长**（float） |
| ★ `round(x, n)` | 四舍五入 | `round(2.675, 2)` → `2.67`；`round(0.5)` → `0` | ⚠️ **银行家舍入**（.5 取偶），且受浮点误差影响 |
| ★ `pow(x, y, m)` | 幂（可带模） | `pow(2, 10)` → `1024`；`pow(2, 10, 100)` → `24` | 三参数版是**模幂**，加密算法常用 |
| ★ `divmod(a, b)` | 同时求商和余 | `divmod(7, 2)` → `(3, 1)` | 结果与 `//`、`%` 一致 |
| ★ `max(it, key, default)` | 最大值 | `max("hello")` → `'o'`；`max([], default=0)` → `0` | `key=` 指定比较依据 |
| ★ `min(it, key, default)` | 最小值 | `min(["apple","b"], key=len)` → `'b'` | 同上 |
| ★ `sum(it, start)` | 求和 | `sum([1,2,3])` → `6`；`sum([True,True])` → `2` | ⚠️ 只能加数字，字符串要用 `"".join` |
| `math.prod(it)` | 求积 | `math.prod([1,2,3,4])` → `24` | 3.8+，需 `import math` |
| ★ `len(obj)` | 长度/元素个数 | `len("中文")` → `2` | ⚠️ 按**字符数**不是字节数 |
| `math.isclose(a, b)` | 浮点近似相等 | `math.isclose(0.1+0.2, 0.3)` → `True` | ✅ 浮点比较的**正确做法** |

### 1.5 序列与迭代

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `range(start, stop, step)` | 生成整数序列 | `list(range(1, 10, 3))` → `[1, 4, 7]` | **左闭右开**；惰性对象，不是列表 |
| ★ `enumerate(it, start)` | 带下标的迭代 | `list(enumerate("ab", 1))` → `[(1,'a'), (2,'b')]` | ✅ 替代 `for i in range(len(...))` |
| ★ `zip(*its, strict)` | 并行迭代，**以最短为准** | `list(zip([1,2,3], "ab"))` → `[(1,'a'), (2,'b')]` | 3.10+ 可 `strict=True` 报错 |
| ★ `map(fn, *its)` | 对每个元素应用函数 | `list(map(int, "12"))` → `[1, 2]` | 返回**迭代器**，要 `list()` |
| ★ `filter(fn, it)` | 按条件筛选 | `list(filter(None, [0,1,2]))` → `[1, 2]` | `fn=None` 时筛掉所有假值 |
| ★ `sorted(it, key, reverse)` | 排序，**返回新列表** | `sorted(["bb","a"], key=len)` → `['a', 'bb']` | ⚠️ 原对象不变 |
| ★ `reversed(seq)` | 反向迭代 | `list(reversed([1,2,3]))` → `[3, 2, 1]` | 返回迭代器；`s[::-1]` 更常用 |
| ★ `any(it)` / `all(it)` | 有真 / 全真 | `any([0,1])` → `True`；`all([0,1])` → `False` | 空序列：`any([])`→`False`，`all([])`→`True` |
| `iter(obj)` / `next(it, default)` | 取迭代器 / 取下一个 | `next(iter([1,2]))` → `1` | 传 `default` 可避免 `StopIteration` |
| `slice(a, b, c)` | 切片对象 | `[1,2,3][slice(1,None)]` → `[2, 3]` | 等价于 `[1:]` |
| `frozenset(seq)` | 不可变集合 | `frozenset([1,2])` | **可哈希**，能当字典键 |

### 1.6 对象与内省

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| `dir(obj)` | 列出所有属性名 | `dir("")` 含 `'upper'` | 查"这对象能干什么" |
| `vars(obj)` | 返回 `__dict__` | `vars(module)` | 模块/类的属性字典 |
| `help(obj)` | 查看文档 | `help(str.split)` | 读的是 `__doc__` |
| `globals()` / `locals()` | 全局/局部命名空间 | `globals()["x"]` | 调试用，慎改 |
| `isinstance/type` 见 1.2 | | | |

---

## 二、字符串方法速查

> 字符串**不可变**——所有"修改"方法都返回**新字符串**，原字符串不变。

### 2.1 查找与判断

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `s.find(sub, start, end)` | 找子串，**找不到返回 -1** | `"hello".find("z")` → `-1` | ✅ 常用 |
| `s.rfind(sub)` | 从右边找 | `"aXa".rfind("a")` → `2` | |
| ★ `s.index(sub)` | 找子串，**找不到抛异常** | `"abc".index("z")` → `ValueError` | ⚠️ 与 `find` 的区别 |
| ★ `s.count(sub)` | 出现次数 | `"aab".count("a")` → `2` | |
| ★ `s.startswith(p)` / `s.endswith(p)` | 前缀 / 后缀判断 | `"a.txt".endswith(".txt")` → `True` | 可传**元组**：`.endswith((".py",".pyw"))` |
| ★ `s.isdigit()` | 是否全是数字字符 | `"12".isdigit()` → `True`；`"-1".isdigit()` → `False` | ⚠️ 负号/小数点会返回 False |
| `s.isalpha()` / `s.isalnum()` | 全字母 / 全字母数字 | `"中文".isalpha()` → `True` | |
| `s.isspace()` / `s.isupper()` / `s.islower()` | 全空白 / 全大写 / 全小写 | `"  ".isspace()` → `True` | 空字符串全返回 `False` |
| ★ `s.isnumeric()` | 是否数字（含中文数字） | `"一".isnumeric()` → `True` | 比 `isdigit` 宽 |

### 2.2 大小写与空白处理

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `s.upper()` / `s.lower()` | 全大写 / 全小写 | `"Py".upper()` → `'PY'` | 返回新字符串 |
| ★ `s.strip(chars)` | **去两端**空白（或指定字符） | `"  a  ".strip()` → `'a'` | ⚠️ 不处理中间 |
| `s.lstrip()` / `s.rstrip()` | 只去左 / 只去右 | `"a\n".rstrip("\n")` → `'a'` | 读文件时常用 `rstrip("\n")` |
| `s.title()` / `s.capitalize()` | 每词首字母大写 / 首字母大写 | `"hello world".title()` → `'Hello World'` | |
| `s.swapcase()` | 大小写互换 | `"aB".swapcase()` → `'Ab'` | |
| `s.casefold()` | 更激进的小写（多语言） | 用于**不区分大小写的比较** | 比 `lower()` 更彻底 |

### 2.3 切分、连接与替换

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `s.split(sep, maxsplit)` | 切分成**列表** | `"a,b,,c".split(",")` → `['a','b','','c']` | ⚠️ 连续分隔符产生空串 |
| ★ `s.split()` | 按**任意空白**切分 | `"a  b   c".split()` → `['a','b','c']` | ✅ **自动去空串**，与上一条不同 |
| `s.rsplit(sep, n)` | 从右往左切 | `"a-b-c".rsplit("-", 1)` → `['a-b', 'c']` | |
| ★ `s.splitlines()` | 按换行切分 | `"a\r\nb".splitlines()` → `['a', 'b']` | ✅ 比 `split("\n")` 好（不产生多余空串） |
| ★ `sep.join(seq)` | 用 sep 连接序列 | `"-".join(["a","b"])` → `'a-b'` | ⚠️ **元素必须全是字符串**；`"".join(...)` 常用于拼接 |
| ★ `s.replace(old, new, count)` | 替换 | `"aaa".replace("a","b",1)` → `'baa'` | 返回新字符串 |
| `s.partition(sep)` | 切成"前/分隔符/后"三段 | `"a=b".partition("=")` → `('a', '=', 'b')` | 找不到时返回 `(s, '', '')` |
| `s.expandtabs(n)` | 把 `\t` 换成空格 | `"a\tb".expandtabs(4)` | |

### 2.4 对齐、填充与格式化

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `s.center(w, fill)` | 居中 | `"abc".center(7, "*")` → `'**abc**'` | |
| `s.ljust(w)` / `s.rjust(w)` | 左/右对齐 | `"5".rjust(3, "0")` → `'005'` | 等价于 `f"{'5':>3}"` |
| ★ `s.zfill(w)` | 左侧补 0 | `"42".zfill(5)` → `'00042'` | ⚠️ 负号也算一位：`"-42".zfill(5)` → `'-0042'` |
| ★ `s.format(*a, **kw)` | 格式化 | `"{}岁".format(18)` → `'18岁'` | 三种格式化之一（第2章） |
| ★ `f"{v:spec}"` | **f-string**（推荐） | `f"{3.14159:.2f}"` → `'3.14'` | 见 2.5 |
| `"%s" % v` | 百分号格式化（旧） | `"%5.2f" % 3.14159` → `' 3.14'` | 维护老代码时会遇到 |

### 2.5 f-string 格式说明符速查

| 说明符 | 作用 | 例子 → 结果 | 备注 |
|---|---|---|---|
| `:.2f` | 保留 2 位小数 | `f"{3.14159:.2f}"` → `'3.14'` | 会四舍五入 |
| `:d` / `:5d` | 整数 / 宽度 5 右对齐 | `f"{42:5d}"` → `'   42'` | |
| `:<10` / `:>10` / `:^10` | 左 / 右 / 居中对齐 | `f"{'hi':>10}"` → `'        hi'` | ⚠️ 按**字符数**补，中文占 2 列会对不齐 |
| `:08b` | 二进制、补零到 8 位 | `f"{255:08b}"` → `'11111111'` | 还有 `:o` 八进制、`:x` 十六进制 |
| `:,` | 千分位分隔 | `f"{1234567:,}"` → `'1,234,567'` | 也可写 `:_` |
| `:.1%` | 百分比 | `f"{0.256:.1%}"` → `'25.6%'` | 自动乘 100 |
| `:.2e` | 科学计数法 | `f"{12345:.2e}"` → `'1.23e+04'` | |
| `:*<10` | 用 `*` 填充左对齐 | `f"{'hi':*<10}\|"` → `'hi********\|'` | 填充字符可任意 |
| `:+.2f` | 强制显示符号 | `f"{3.1:+.2f}"` → `'+3.10'` | |
| `:=+9.2f` | 符号与数字对齐 | `f"{-3.1:=+9.2f}"` | 进阶用法 |
| `!r` / `!s` / `!a` | 用 `repr`/`str`/`ascii` 转换 | `f"{'a'!r}"` → `"'a'"` | ⚠️ **写在冒号前面**；`f"{'a':!r}"` 报 `ValueError` |

---

## 三、列表方法速查

> 列表**可变**——下面带 ⚠️ 的方法**就地修改原列表且返回 `None`**。

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `lst.append(x)` | 尾部加**一个**元素 | `[1].append([2])` → `[1, [2]]` | ⚠️ 返回 `None`；加列表用 `extend` |
| ★ `lst.extend(it)` | 尾部追加**多个** | `[1].extend([2,3])` → `[1, 2, 3]` | 等价于 `lst += [2,3]` |
| ★ `lst.insert(i, x)` | 在位置 i 插入 | `[1,3].insert(1,2)` → `[1, 2, 3]` | 越界会插到首/尾，不报错 |
| ★ `lst.remove(x)` | 删除**第一个**等于 x 的元素 | `[1,2,1].remove(1)` → `[2, 1]` | ⚠️ 不存在时 `ValueError` |
| ★ `lst.pop(i=-1)` | 删除并**返回**该元素 | `[1,2,3].pop()` → `3` | ⚠️ `pop(0)` 是 **O(n)**，队列用 `deque` |
| `lst.clear()` | 清空 | `[1,2].clear()` → `[]` | |
| ★ `lst.index(x, s, e)` | 找下标 | `[1,2].index(2)` → `1` | ⚠️ 不存在时 `ValueError` |
| ★ `lst.count(x)` | 出现次数 | `[1,1,2].count(1)` → `2` | |
| ★ `lst.sort(key, reverse)` | **就地**排序 | `lst.sort()` | ⚠️ **返回 `None`**！`x = x.sort()` 是经典错误 |
| ★ `sorted(lst, key, reverse)` | 排序并**返回新列表** | `sorted([3,1])` → `[1, 3]` | ✅ 内置函数，不修改原列表 |
| ★ `lst.reverse()` | **就地**反转 | `[1,2].reverse()` | ⚠️ 返回 `None`；`lst[::-1]` 返回新列表 |
| ★ `lst.copy()` | 浅拷贝 | `a = b.copy()` | ⚠️ 嵌套对象仍是共享的 |
| `lst.__contains__` → `x in lst` | 成员判断 | `2 in [1,2]` → `True` | 列表是 **O(n)**，集合是 O(1) |
| 切片赋值 | 批量替换/删除/插入 | `lst[1:3] = [9]`；`lst[1:3] = []`；`lst[1:1] = [7]` | 功能很强，考试常考 |

**排序的 `key` 用法**：

```python
data = [("张三", 92, 18), ("李四", 85, 20), ("王五", 92, 19)]
print(sorted(data, key=lambda s: (-s[1], s[2])))
# [('张三', 92, 18), ('王五', 92, 19), ('李四', 85, 20)]   ← 分数降序、年龄升序
print(sorted(data, key=lambda s: s[1], reverse=True))
```

---

## 四、元组、字典、集合方法速查

### 4.1 元组（不可变）

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `tup.count(x)` | 出现次数 | `(1,1,2).count(1)` → `2` | 元组**只有这两个方法** |
| ★ `tup.index(x)` | 找下标 | `(1,2).index(2)` → `1` | |
| `a, b = tup` | 解包 | `a, b = (1, 2)` | 数量必须匹配 |
| `a, *rest = tup` | 星号解包 | `a, *r = (1,2,3)` → `a=1, r=[2, 3]` | ⚠️ `rest` 是**列表** |
| `(5,)` | **单元素元组** | `type((5))` → `int`；`type((5,))` → `tuple` | ⚠️ **逗号才是关键** |
| `tup + tup2` / `tup * 3` | 拼接 / 重复 | `(1,)+(2,)` → `(1, 2)` | 返回新元组 |

### 4.2 字典

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `d[key]` | 取值 | `{"a":1}["a"]` → `1` | ⚠️ 键不存在时 `KeyError` |
| ★ `d.get(k, default)` | 安全取值 | `{"a":1}.get("z", 0)` → `0` | ✅ **不报错、不建键** |
| ★ `d.setdefault(k, default)` | 取不到就**建键** | `d.setdefault("x", []).append(1)` | ⚠️ 会修改字典 |
| ★ `d[k] = v` | 增/改 | 键存在则覆盖 | |
| ★ `d.update(other)` | 批量增改 | `d.update({"a":1})` | 也接受关键字参数 |
| ★ `d.pop(k, default)` | 删除并返回 | `{"a":1}.pop("a")` → `1` | 无 `default` 且键不存在 → `KeyError` |
| `d.popitem()` | 删除并返回**最后一个**键值对 | `{"a":1}.popitem()` → `('a', 1)` | LIFO 顺序 |
| `d.clear()` | 清空 | | |
| ★ `d.keys()` / `d.values()` / `d.items()` | 三个**视图对象** | `list(d.items())` → `[('a', 1)]` | ✅ `items()` 是遍历的标准写法 |
| ★ `k in d` | 键是否存在 | `"a" in {"a":1}` → `True` | ⚠️ 判断的是**键**不是值 |
| ★ `d.copy()` | 浅拷贝 | | 嵌套对象仍共享 |
| `len(d)` | 键值对个数 | `len({"a":1})` → `1` | |
| `dict.fromkeys(seq, v)` | 用序列建字典 | `dict.fromkeys("ab", 0)` → `{'a': 0, 'b': 0}` | ⚠️ 值若是可变对象会**共享** |
| ★ `d \| d2` | 合并（3.9+） | `{"a":1} \| {"b":2}` → `{'a': 1, 'b': 2}` | 右边优先 |
| `d \|= d2` | 就地合并 | | |

**四种遍历方式**：

```python
d = {"a": 1, "b": 2}
for k in d: ...                      # 只遍历键（最快）
for k in d.keys(): ...               # 同上，显式
for v in d.values(): ...             # 只遍历值
for k, v in d.items(): ...           # ✅ 键值都要时用这个
```

### 4.3 集合

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `set()` | **空集合** | `type({})` → `dict` | ⚠️ **`{}` 是空字典**，空集合必须 `set()` |
| ★ `st.add(x)` | 加元素 | `{1}.add(2)` → `{1, 2}` | 重复添加无效果 |
| ★ `st.remove(x)` | 删除 | | ⚠️ 不存在时 `KeyError` |
| ★ `st.discard(x)` | 删除（**不报错**） | `{1}.discard(9)` → `{1}` | ✅ 比 `remove` 安全 |
| `st.pop()` | 随机删除并返回 | | ⚠️ 集合无序，"随机"不可预期 |
| `st.clear()` / `st.copy()` | 清空 / 浅拷贝 | | |
| ★ `st.update(it)` | 批量加 | `{1}.update([2,3])` | |
| ★ `a \| b` | **并集** | `{1,2} \| {2,3}` → `{1, 2, 3}` | 方法版 `a.union(b)` |
| ★ `a & b` | **交集** | `{1,2} & {2,3}` → `{2}` | `a.intersection(b)` |
| ★ `a - b` | **差集** | `{1,2} - {2,3}` → `{1}` | `a.difference(b)` |
| ★ `a ^ b` | **对称差**（只在一边） | `{1,2} ^ {2,3}` → `{1, 3}` | `a.symmetric_difference(b)` |
| `a <= b` / `a < b` | 子集 / 真子集 | `{1} <= {1,2}` → `True` | `a.issubset(b)` |
| `a >= b` / `a > b` | 超集 / 真超集 | | `a.issuperset(b)` |
| `a.isdisjoint(b)` | 是否无交集 | `{1}.isdisjoint({2})` → `True` | |

> **集合的两大用途**：① **去重**（`list(set(lst))`，但会丢顺序）；② **O(1) 成员判断**（`x in set` 远快于 `x in list`）。**要"去重且保持原顺序"**用 `dict.fromkeys(lst)`（3.7+ 字典有序）。

---

## 五、文件对象方法速查

| 语法 | 作用 | 例子 → 返回 | 备注 |
|---|---|---|---|
| ★ `open(file, mode, encoding, newline)` | 打开文件 | `open("a.txt", "r", encoding="utf-8")` | ✅ **一律用 `with` 包裹** |
| ★ `f.read(n)` | 读全部 / 读 n 个字符 | `f.read(5)` → 前 5 个字符 | 大文件别用无参 `read()` |
| ★ `f.readline()` | 读一行（含 `\n`） | → `'第一行\n'` | 到末尾返回 `''` |
| ★ `f.readlines()` | 读全部行成列表 | → `['a\n', 'b\n']` | 大文件占内存 |
| ★ `for line in f:` | **逐行迭代**（推荐） | | ✅ 最省内存，等价于 `readline` 循环 |
| ★ `f.write(s)` | 写字符串 | 返回**写入的字符数** | ⚠️ 只接受 `str`（二进制模式要 `bytes`） |
| ★ `f.writelines(seq)` | 写多行 | | ⚠️ **不会自动补换行**，要自己写 `\n` |
| `f.seek(off, whence)` | 移动指针 | `f.seek(0)` 回到开头 | `whence`：0 开头 / 1 当前 / 2 末尾 |
| `f.tell()` | 当前指针位置 | | ⚠️ **文本模式下是不透明标记，不是字节数** |
| `f.flush()` | 强制写入磁盘 | | 长时间运行的程序要定期 flush |
| `f.close()` | 关闭 | | ✅ 用 `with` 就不用手动关 |
| `f.closed` / `f.name` / `f.mode` | 是否关闭 / 文件名 / 模式 | | |

**六种打开模式**（**必考**）：

| 模式 | 含义 | 文件不存在 | 文件存在 | 指针初始位置 |
|---|---|---|---|---|
| ★ `"r"` | 只读（默认） | **`FileNotFoundError`** | 读 | 开头 |
| ★ `"w"` | 只写 | 创建 | ⚠️ **清空！** | 开头 |
| ★ `"a"` | 追加 | 创建 | 保留内容 | **末尾** |
| ★ `"x"` | 独占创建 | 创建 | ⚠️ **`FileExistsError`** | 开头 |
| `"r+"` | 读写 | 报错 | ⚠️ **不清空** | **开头**（写会覆盖开头） |
| `"w+"` / `"a+"` | 读写（清空 / 追加） | 创建 | 清空 / 保留 | 开头 / 末尾 |
| `"rb"` / `"wb"` / `"ab"` | 二进制模式 | 同上去掉编码参数 | | 读写 `bytes` |

> **⚠️ 三个高频坑**：① **`"w"` 会清空文件**，是最危险的模式；② **`"a"` 不等于 `"r+"`**——`"r+"` 的写指针在**开头**会覆盖内容，追加必须用 `"a"`；③ **文本模式一定要写 `encoding="utf-8"`**，否则 Windows 上默认用 GBK，读 UTF-8 文件会 `UnicodeDecodeError`。

**CSV 与 JSON 关键参数**：

| 语法 | 作用 | 例子 | 备注 |
|---|---|---|---|
| ★ `open(..., newline="")` | 写 CSV 必须加 | `open("a.csv","w",newline="",encoding="utf-8")` | ⚠️ 不加会多出 `\r`：实测 `b'a,b\r\r\n'` |
| ★ `encoding="utf-8-sig"` | 让 Excel 正确识别中文 | 写入时带 BOM（`b'\xef\xbb\xbf'`） | ⚠️ 用 `utf-8` 读会多出 `\ufeff` |
| ★ `csv.reader(f)` / `csv.writer(f)` | 读写 CSV 行 | 每行是**列表** | 数字读出是**字符串** |
| ★ `csv.DictReader(f)` | 按表头读成字典 | `{'姓名': '张三', 'Python': '92'}` | ✅ 比下标可读 |
| ★ `json.dumps(obj, ensure_ascii=False)` | 对象 → JSON 字符串 | 不加则中文变 `\uXXXX` | 写文件常用 `indent=2`、`sort_keys=True` |
| ★ `json.loads(s)` | JSON 字符串 → 对象 | | ⚠️ 只认**双引号**，单引号报 `JSONDecodeError` |
| `json.dump(obj, f)` / `json.load(f)` | 直接读写文件 | | 少了手工 `read/write` |
| `pickle.dump/load` | 二进制序列化**任意对象** | | ⚠️⚠️ **`load` 会执行任意代码，绝不读不可信文件** |

---

## 六、常用标准库速查

### 6.1 math（数学）

| 语法 | 作用 | 例子 → 返回 |
|---|---|---|
| `math.pi` / `math.e` | 常量 | `3.141592653589793` |
| `math.ceil(x)` / `math.floor(x)` | 向上 / 向下取整 | `ceil(-1.1)` → `-1`；`floor(-1.9)` → `-2` |
| `math.trunc(x)` | 向零截断 | `trunc(-3.9)` → `-3` |
| `math.sqrt(x)` | 平方根 | `sqrt(16)` → `4.0` |
| `math.gcd(a, b)` | 最大公约数 | `gcd(12, 18)` → `6` |
| `math.factorial(n)` | 阶乘 | `factorial(5)` → `120` |
| `math.hypot(x, y)` | 欧氏距离 | `hypot(3, 4)` → `5.0` |
| `math.isclose(a, b)` | 近似相等 | `isclose(0.1+0.2, 0.3)` → `True` |
| `math.log(x, base)` / `math.log10` / `math.exp` | 对数 / 指数 | `log(8, 2)` → `3.0` |

### 6.2 random（随机数）

| 语法 | 作用 | 例子 → 返回 |
|---|---|---|
| ★ `random.seed(n)` | 设置种子（**结果可复现**） | `seed(1)` 后 `random()` → `0.13436424411240122` |
| `random.random()` | `[0, 1)` 浮点 | `0.13436424411240122` |
| ★ `random.randint(a, b)` | `[a, b]` **闭区间**整数 | `randint(1, 6)` → 掷骰子 |
| `random.randrange(a, b, step)` | 半开区间整数 | `randrange(0, 10, 2)` → 偶数 |
| `random.uniform(a, b)` | 区间浮点 | `uniform(1, 10)` → `3.2956212316547955` |
| ★ `random.choice(seq)` | 随机取一个 | `choice("abc")` → `'c'` |
| ★ `random.sample(seq, k)` | **不重复**取 k 个 | `sample(range(10), 3)` → `[1, 4, 9]` |
| ★ `random.shuffle(lst)` | **就地**打乱 | `shuffle([1,2,3,4,5])` → `[3, 4, 5, 1, 2]` |
| `random.choices(seq, weights, k)` | 带权重、可重复取 | `choices(["A","B"], weights=[1,3], k=10)` |
| `random.gauss(mu, sigma)` | 正态分布 | `gauss(100, 15)` → `96.16` |

> **⚠️ 上表"例子"列里的具体数字必须先 `random.seed(1)` 才会出现**（`random.random()` = `0.13436424411240122`、`uniform(1,10)` = `3.2956212316547955`、`randrange(0,10,2)` = `2`、`choice("abc")` = `'c'`、`sample(range(10),3)` = `[1, 4, 9]`、`shuffle([1,2,3,4,5])` = `[3, 4, 5, 1, 2]`、`choices(["A","B"],weights=[1,3],k=10)` = `['A','B','B','B','B','B','B','B','A','A']`）。**不设种子时每次运行结果都不同**，只能保证范围（`random()` 落在 `[0,1)`、`randint(1,6)` 落在 `[1,6]`）；表中 `gauss` 的 `96.16` 来自 **`seed(7)`**。

> **⚠️ 安全用途（密码、令牌）必须用 `secrets` 模块**，`random` 是可预测的伪随机。

### 6.3 datetime（日期时间）

| 语法 | 作用 | 例子 → 返回 |
|---|---|---|
| ★ `datetime.now()` | 当前日期时间 | `2025-09-01 10:30:00.123456` |
| ★ `date(y, m, d)` / `datetime(y, m, d, H, M)` | 构造 | `date(2025, 1, 1)` |
| ★ `dt.strftime(fmt)` | 格式化成字符串 | `date(2025,1,1).strftime("%Y-%m-%d %A")` → `'2025-01-01 Wednesday'` |
| ★ `datetime.strptime(s, fmt)` | 字符串解析成对象 | `strptime("2025-09-01 10:30","%Y-%m-%d %H:%M")` |
| `dt.date()` / `dt.time()` | 取出日期 / 时间部分 | |
| `dt.weekday()` / `dt.isoweekday()` | 星期（0=周一 / 1=周一） | `date(2025,1,1).weekday()` → `2`；`isoweekday()` → `3` |
| `dt.isocalendar()` | ISO 年/周/星期 | `IsoCalendarDate(year=2025, week=36, weekday=1)` |
| `dt1 - dt2` | 时间差 | `(date(2025,12,31)-date(2025,1,1)).days` → `364` |
| `dt + timedelta(days=100)` | 日期加减 | `date(2025,1,1) + timedelta(days=100)` → `2025-04-11` |
| `time.time()` | 时间戳（秒） | `1756000000.123` |
| `time.perf_counter()` | **高精度计时**（性能测试用） | 差值即耗时 |
| `time.sleep(s)` | 暂停 s 秒 | 爬虫限速用 |

**格式符速记**：`%Y` 四位年 / `%y` 两位年 / `%m` 月 / `%d` 日 / `%H` 时(24) / `%M` 分 / `%S` 秒 / `%A` 星期全名 / `%a` 星期缩写 / `%j` 一年第几天 / `%p` AM/PM。⚠️ **`%y` 的两位年有隐含规则**：`69~99` 解析成 `1969~1999`，`00~68` 解析成 `2000~2068`（实测 `strptime("25","%y")` → `2025-01-01`、`strptime("69","%y")` → `1969-01-01`），**所以年份一律用 `%Y` 更安全**。⚠️ **`date` 和 `datetime` 比较要看运算符**：`==` 返回 `False`（**不报错**，因为两者本就是不同类型），但 `<` / `>` 抛 `TypeError`；要比较先把 `datetime` 用 `.date()` 转成 `date`。

### 6.4 os / sys / pathlib

| 语法 | 作用 | 例子 → 返回 |
|---|---|---|
| `os.getcwd()` | 当前工作目录 | |
| `os.listdir(p)` | 列出目录内容 | |
| `os.mkdir(p)` / `os.makedirs(p, exist_ok=True)` | 建单级 / **多级**目录 | `exist_ok=True` 避免已存在报错 |
| `os.remove(f)` / `os.rmdir(d)` | 删文件 / 删空目录 | |
| `os.rename(a, b)` | 重命名/移动 | |
| `os.walk(p)` | **递归遍历目录树** | 产出 `(dirpath, dirnames, filenames)` |
| `os.path.join(a, b)` | 拼路径 | `os.path.join("a", "b.txt")` → `'a\\b.txt'` |
| `os.path.basename(p)` / `dirname(p)` | 取文件名 / 目录 | `basename(r"D:\a\b.txt")` → `'b.txt'` |
| `os.path.splitext(p)` | 拆主名与后缀 | `splitext("data.csv")` → `('data', '.csv')` |
| `os.path.exists(p)` / `isfile` / `isdir` | 判断 | |
| `os.name` / `os.sep` | 系统名 / 分隔符 | `'nt'` / `'\\'` |
| ★ `sys.version_info[:2]` | Python 版本 | `(3, 12)` |
| ★ `sys.platform` | 平台 | `'win32'` |
| `sys.maxsize` | 最大整数 | `9223372036854775807` |
| `sys.argv` | 命令行参数列表 | ⚠️ **全是字符串**，`argv[0]` 是脚本名 |
| `sys.path` | 模块搜索路径 | 可 `insert(0, ...)` 加自己的目录 |
| `sys.setrecursionlimit(n)` | 递归深度上限 | 默认 **1000** |
| `sys.exit(code)` | 退出程序 | 抛 `SystemExit` |
| ★ `Path(p)` / `p / "sub"` | 构造 / 拼路径 | `pathlib`，见第17章 |
| ★ `p.read_text(enc)` / `p.write_text(s, enc)` | 一步读写文本 | |
| ★ `p.glob("*.py")` / `p.rglob("*.py")` | 模式匹配 / **递归**匹配 | |
| `p.exists()` / `is_file()` / `is_dir()` / `stat().st_size` | 判断与信息 | |

### 6.5 re（正则表达式）

| 语法 | 作用 | 例子 → 返回 |
|---|---|---|
| ★ `re.match(p, s)` | **从开头**匹配（不要求到末尾） | `match(r"\d+", "abc123")` → `None` |
| ★ `re.search(p, s)` | 全文找**第一个** | `search(r"\d+", "abc123def456").group()` → `'123'` |
| ★ `re.fullmatch(p, s)` | **整串完全匹配** | ✅ **校验格式一律用它** |
| ★ `re.findall(p, s)` | 找**所有**，返回字符串列表 | `findall(r"\d+", "a12b345")` → `['12', '345']` |
| `re.finditer(p, s)` | 返回 Match 对象迭代器 | 可取 `span()`、`start()` |
| ★ `re.sub(p, repl, s, count)` | 替换 | `sub(r"\d+", "#", "a1b22")` → `'a#b#'` |
| ★ `re.split(p, s)` | 按模式切分 | `split(r"[,;\s]+", "a,b; c")` → `['a','b','c']` |
| `re.compile(p, flags)` | 预编译（重复使用时提速） | |
| ★ `m.group(n)` / `m.groups()` / `m.groupdict()` | 取分组 | `groups()` → `('2025','09','01')` |
| `m.start()` / `m.end()` / `m.span()` | 位置 | `span()` → `(1, 3)` |

**标志位**：`re.I` 忽略大小写｜`re.M` 多行（`^`/`$` 匹配每行）｜`re.S` 让 `.` 匹配换行｜`re.X` 允许注释与空白。

**常用模式**：

| 用途 | 模式 |
|---|---|
| 手机号 | `^1[3-9]\d{9}$` |
| 邮箱 | `^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$` |
| 身份证（18 位） | `^\d{17}[\dXx]$` |
| 日期 | `^\d{4}-\d{2}-\d{2}$`（⚠️ 还需 `strptime` 校验真实性） |
| 整数 / 小数 | `^-?\d+$` / `^-?\d+\.\d+$` |
| 中文 | `[\u4e00-\u9fa5]+` |
| HTML 标签 | `<[^>]+>`（**去标签用**）；`<a href="([^"]+)"`（**取属性**） |
| 用户名（字母开头，4~16 位） | `^[A-Za-z][A-Za-z0-9_]{3,15}$`（⚠️ **别用 `\w`**，见坑④） |
| 密码（字母+数字，8 位以上） | `^(?=.*[A-Za-z])(?=.*\d)\S{8,}$` |

**⚠️ 四个必知坑**：① **一律加 `r` 前缀**（`r"\b"` 才是"单词边界"，不加会被当成退格符）；② **`$` 允许末尾有一个换行**——`re.match(r"^\d+$", "123\n")` 能匹配，**严格校验要用 `fullmatch`**；③ **`findall` 有分组时返回值会变**：`findall(r"(\d)(\d)", "12 34")` → `[('1','2'), ('3','4')]`；④ **`\w`、`\d`、`\s` 在 `str` 模式下是 Unicode 语义，不是 ASCII**——`re.fullmatch(r"\w+", "a中文中")` 返回 `True`（**中文也能通过用户名校验！**），`re.findall(r"\d+", "٣")` 能匹配阿拉伯数字，`re.findall(r"\s", "\u3000")` 能匹配全角空格。**做格式校验时把 `\w` 展开成 `[A-Za-z0-9_]`、`\d` 展开成 `[0-9]`**，或加标志位 `re.ASCII`（`re.compile(r"\w").flags` 是 `32`，即 `re.UNICODE`）。

### 6.6 itertools / collections / functools（速记）

| 语法 | 作用 | 例子 → 返回 |
|---|---|---|
| `itertools.count(a, step)` | **无限**递增 | `islice(count(10,2), 4)` → `[10, 12, 14, 16]` |
| `itertools.cycle(seq)` | **无限**循环 | `islice(cycle("AB"), 5)` → `['A','B','A','B','A']` |
| `itertools.repeat(x, n)` | 重复 | `repeat("x", 3)` → `['x','x','x']` |
| `itertools.chain(*its)` | 串联多个迭代器 | `chain([1,2], "ab")` → `[1, 2, 'a', 'b']` |
| `itertools.islice(it, n)` | 截断无限迭代器 | ✅ 配合 `count`/`cycle` 必用 |
| `itertools.product(a, b)` | 笛卡尔积（有序可重复） | 4 组 |
| `itertools.permutations(s, r)` | 排列（有序不重复） | 6 组 |
| `itertools.combinations(s, r)` | 组合（无序不重复） | 3 组 |
| `itertools.groupby(it, key)` | 分组（⚠️ **必须先排序**） | |
| `itertools.accumulate(it, fn)` | 累积 | `accumulate([1,2,3,4])` → `[1, 3, 6, 10]` |
| `itertools.pairwise(it)` | 相邻配对 | `pairwise([1,2,3])` → `[(1,2), (2,3)]` |
| `itertools.zip_longest(a, b, fillvalue)` | 补齐式 zip | |
| ★ `Counter(seq)` | 计数器 | `most_common(2)` → `[('a', 5), ('b', 2)]` |
| ★ `defaultdict(factory)` | 带默认值的字典 | `d["x"].append(1)` |
| ★ `deque(seq, maxlen)` | 双端队列 | `popleft()` 是 **O(1)** |
| `namedtuple("P", "x y")` | 轻量只读记录 | `P(1,2).x` → `1` |
| `ChainMap(d1, d2)` | 多层查找 | 前面的优先 |
| ★ `functools.lru_cache` | 自动缓存（递归加速） | `fib(30)` → `832040`，只算 31 次 |
| `functools.partial(fn, k=v)` | 固化参数 | `partial(int, base=2)("1010")` → `10` |
| ★ `functools.wraps(fn)` | 装饰器必加，保留元信息 | 否则 `__name__` 变 `'wrapper'` |
| `functools.reduce(fn, it, init)` | 累积（已移入 functools） | `reduce(mul, [1,2,3,4])` → `24` |

### 6.7 logging 与 argparse（速记）

| 语法 | 作用 | 备注 |
|---|---|---|
| ★ `logging.basicConfig(level, format, filename, encoding)` | 一次性配置 | **只在程序入口调一次** |
| ★ `logging.getLogger(name)` | 取 logger | 库代码用 `getLogger(__name__)` |
| ★ `log.debug/info/warning/error/critical(msg)` | 五个级别 | ⚠️ **默认级别是 `WARNING`**，`info` 不输出 |
| ★ `log.exception(msg)` | 记录异常**并带堆栈** | ⚠️ **只能在 `except` 块里用** |
| `%(asctime)s %(levelname)s %(name)s %(message)s` | 常用格式符 | 还有 `%(filename)s`、`%(lineno)d` |
| ★ `argparse.ArgumentParser(prog, description)` | 创建解析器 | `-h` 自动生成 |
| ★ `add_argument("name")` | 位置参数（必填） | |
| ★ `add_argument("-n","--num", type=int, default=1)` | 可选参数 | ⚠️ **默认是字符串**，要写 `type` |
| `action="store_true"` | 开关 | 出现即 `True` |
| `choices=[...]` | 取值范围校验 | 非法值**自动退出** |
| ★ `parse_args(argv)` | 解析 | ⚠️ 出错抛 **`SystemExit`** |

---

## 七、正则元字符速查

| 元字符 | 含义 | 例子 |
|---|---|---|
| `.` | 任意字符（**默认不含换行**） | `a.c` 匹配 `abc` |
| `\d` / `\D` | 数字 / 非数字 | ⚠️ `str` 模式含 Unicode 数字（`\d+` 能匹配 `٣`）；ASCII 近似 `[0-9]` |
| `\w` / `\W` | 字母数字下划线 / 非 | ⚠️ `str` 模式**含中文**（`\w+` 能匹配 `中文abc`）；ASCII 近似 `[A-Za-z0-9_]` |
| `\s` / `\S` | 空白 / 非空白 | ⚠️ 还含全角空格 `\u3000`；ASCII 近似 `[ \t\n\r\f\v]` |
| `[...]` | 字符类 | `[aeiou]` → `['e','o','o']` |
| `[^...]` | **取反** | `[^aeiou ]` |
| `*` / `+` / `?` | 0+ / 1+ / 0 或 1 次 | `ab*`、`ab+`、`ab?` |
| `{n}` / `{n,}` / `{n,m}` | 恰好 / 至少 / 之间 | `a{2,3}` |
| `*?` / `+?` / `??` | **非贪婪** | `<.*?>` 匹配单个标签 |
| `^` / `$` | 行首 / 行尾 | ⚠️ `$` 允许末尾换行 |
| `\b` / `\B` | 单词边界 / 非边界 | `\bcat\b` 排除 `category` |
| `\A` / `\Z` | 串首 / 串尾（严格） | |
| `(...)` | 捕获分组 | `(\d{4})-(\d{2})` |
| `(?:...)` | **非捕获**分组 | 只分组不占编号 |
| `(?P<name>...)` | **命名**分组 | `group("y")` |
| `\1` / `\2` | **反向引用** | `(a)(b)\2\1` 匹配 `abba` |
| `a\|b` | 或 | `cat\|dog` |
| `(?=...)` / `(?!...)` | 前瞻 / 否定前瞻 | `\d+(?=元)` |
| `\` | 转义 | ⚠️ 需转义的 **14** 个字符：`. ^ $ * + ? { } [ ] \ \| ( )` |

---

## 八、异常类型速查

### 8.1 常见内置异常

| 异常 | 触发场景 | 典型消息 |
|---|---|---|
| ★ `SyntaxError` | 语法错误（**运行前就报**） | `invalid syntax` |
| `IndentationError` | 缩进错误（是 `SyntaxError` 子类） | `unexpected indent` |
| ★ `NameError` | 用了未定义的变量 | `name 'x' is not defined` |
| ★ `TypeError` | 类型不匹配 | `unsupported operand type(s) for +: 'int' and 'str'` |
| ★ `ValueError` | 类型对但**值不对** | `invalid literal for int() with base 10: 'abc'` |
| ★ `KeyError` | 字典键不存在 | `'z'` |
| ★ `IndexError` | 下标越界 | `list index out of range` |
| ★ `AttributeError` | 属性/方法不存在 | `'tuple' object has no attribute 'id'` |
| ★ `FileNotFoundError` | 文件不存在（`OSError` 子类） | `[Errno 2] No such file or directory: 'a.txt'` |
| `FileExistsError` | 用 `"x"` 模式打开已存在的文件 | `[Errno 17] File exists` |
| `PermissionError` | 权限不足 / 文件被占用 | `[WinError 32] 另一个程序正在使用此文件` |
| ★ `ZeroDivisionError` | 除以 0 | `division by zero` |
| `UnboundLocalError` | 局部变量未赋值就使用（`NameError` 子类） | `cannot access local variable 'cnt' where it is not associated with a value` |
| `UnicodeDecodeError` | 解码失败（编码不匹配） | `'utf-8' codec can't decode byte 0xd6 in position 0` |
| `JSONDecodeError` | JSON 格式错 | `Expecting property name enclosed in double quotes` |
| `StopIteration` | 迭代器耗尽（`for` 内部正常捕获） | |
| ★ `RecursionError` | 递归超限 | `maximum recursion depth exceeded` |
| `AssertionError` | `assert` 失败 | |
| `KeyboardInterrupt` | 用户按 Ctrl+C（**不该被兜底捕获**） | |
| `SystemExit` | `sys.exit()` / `argparse` 出错 | |

### 8.2 异常处理语法

| 语法 | 作用 | 备注 |
|---|---|---|
| `try / except E as e` | 捕获并处理 | 多个 `except` **从具体到宽泛**排列 |
| `except (A, B) as e` | 一次捕获多种 | |
| `try / except / else` | `else` 在**没异常**时执行 | |
| `try / finally` | **无论如何都执行** | 清理资源 |
| `try / except / else / finally` | 完整四子句 | ⚠️ **`finally` 里的 `return` 会覆盖 `try` 的 return** |
| `raise E("msg")` | 主动抛异常 | |
| `raise E("msg") from e` | 保留原始异常链 | 推荐写法 |
| `raise` | **重新抛出**当前异常 | 只能在 `except` 里用 |
| `assert cond, "msg"` | 断言 | ⚠️ **`python -O` 会全部删掉**，不能校验用户输入 |
| `class MyErr(Exception)` | 自定义异常 | 继承 `Exception`，名字以 `Error` 结尾 |

**⚠️ 不要用裸 `except:`**——它会连 `KeyboardInterrupt`（Ctrl+C）和 `SystemExit` 一起吞掉，程序无法中断。**至少写 `except Exception:`**。

---

## 九、运算符与转义速查

### 9.1 运算符优先级（从高到低）

| 优先级 | 运算符 | 说明 |
|---|---|---|
| 1 | `**` | 幂（**右结合**：`2**3**2` = `512`） |
| 2 | `+x` `-x` `~x` | 一元正负、按位取反 |
| 3 | `*` `/` `//` `%` | 乘除、整除、取余 |
| 4 | `+` `-` | 加减 |
| 5 | `<<` `>>` | 移位 |
| 6 | `&` | 按位与 |
| 7 | `^` | 按位异或 |
| 8 | `\|` | 按位或 |
| 9 | `==` `!=` `<` `<=` `>` `>=` `in` `not in` `is` `is not` | 比较、成员、身份 |
| 10 | `not x` | 逻辑非 |
| 11 | `and` | 逻辑与（**短路**） |
| 12 | `or` | 逻辑或（**短路**） |
| 13 | `if...else`（条件表达式）、`lambda` | 最低 |

> **口诀**：**幂 > 一元 > 算术 > 移位 > 位与 > 异或 > 位或 > 比较 > not > and > or**。拿不准就**加括号**。

### 9.2 转义字符

| 转义 | 含义 | 转义 | 含义 |
|---|---|---|---|
| `\n` | 换行 | `\t` | 制表符 |
| `\r` | 回车 | `\b` | 退格 |
| `\\` | 反斜杠 | `\'` / `\"` | 单/双引号 |
| `\0` | 空字符 | `\f` / `\v` | 换页 / 垂直制表 |
| `\xhh` | 十六进制字符 | `\ooo` | 八进制字符 |
| `\uXXXX` | Unicode（4 位） | `\N{name}` | 按 Unicode 名 |
| ★ `r"..."` | **原始字符串**（不转义） | `f"..."` | f-string |

### 9.3 常用数值运算结果（易错）

| 表达式 | 结果 | 说明 |
|---|---|---|
| `0.1 + 0.2` | `0.30000000000000004` | IEEE754 浮点误差 |
| `0.1 + 0.2 == 0.3` | `False` | 比较用 `math.isclose` |
| `7 // 2` / `-7 // 2` | `3` / `-4` | **向下取整**，不是向零截断 |
| `-7 % 2` / `7 % -2` | `1` / `-1` | 余数符号**跟随除数** |
| `int(-7/2)` | `-3` | `int()` 是**向零截断** |
| `round(0.5)` / `round(1.5)` / `round(2.5)` | `0` / `2` / `2` | **银行家舍入**（.5 取偶） |
| `round(2.675, 2)` | `2.67` | 浮点表示误差导致 |
| `2 ** 100` | `1267650600228229401496703205376` | int **任意精度** |
| `1e16 + 1` | `1e+16` | 浮点精度只有约 15~17 位 |
| `True == 1` / `True is 1` | `True` / `False` | `bool` 是 `int` 子类 |
| `"10" < "9"` | `True` | **字符串按字典序**比较 |
| `[1,2] == [1,2]` / `[1,2] is [1,2]` | `True` / `False` | `==` 比值，`is` 比身份 |

---

## 十、上机考试高频模板索引

| 题型 | 关键写法 | 详见 |
|---|---|---|
| 输入一行多个数 | `a, b = map(int, input().split())` | 第1章 1.5 |
| 输入 n 行 | `[input().strip() for _ in range(n)]` | 第4章 |
| 读文件全部行 | `with open(p, encoding="utf-8") as f: lines = f.read().splitlines()` | 第9章 |
| 写文件 | `with open(p, "w", encoding="utf-8") as f: f.write(s)` | 第9章 |
| 词频统计 | `Counter(re.findall(r"[a-zA-Z]+", text.lower())).most_common(3)` | 第17章 |
| 按值排序字典 | `sorted(d.items(), key=lambda kv: -kv[1])` | 第4章 |
| 多级排序 | `sorted(data, key=lambda x: (-x[1], x[2]))` | 第4章 |
| 分组 | `defaultdict(list)` 或排序后 `groupby` | 第17章 |
| 去重保序 | `list(dict.fromkeys(lst))` | 第4章 |
| 二维矩阵转置 | `list(zip(*m))` | 第4章 |
| 递归加速 | `@lru_cache(maxsize=None)` | 第5/17章 |
| 生成器省内存 | `(x*x for x in range(10**6))` | 第4章 |
| 类与对象 | `class A: def __init__(self, x): self.x = x` | 第6章 |
| 异常处理 | `try: ... except ValueError as e: ...` | 第10章 |
| 校验格式 | `re.fullmatch(pattern, s) is not None` | 第8章 |
| 编码转换 | `open(p, encoding="gbk")` / `"utf-8-sig"` | 第9章 |

> **最后提醒**：上机时**先让代码跑起来，再考虑优化**；**每写一个函数就测一次**；**读题时把"输入格式"和"输出格式"抄到注释里**——这三条能避免绝大多数丢分。
