# 附录B 易错点与题型总结

> 配套教材：王雷春《Python 程序设计——从基础入门到实战应用（微课版）（第2版）》｜适用于全部章节｜**期末复习与上机考试专用**

## 使用说明

- **第一部分**：按主题汇总全书 **100 条易错点**，每条格式为「**错在哪 → 正确做法**」，可以直接当错题本用。
- **第二部分**：**十大编程题型模板**，每种题型给出「识别特征 → 解题套路 → 完整模板代码 → 易错提醒」。
- **第三部分**：**上机考试检查清单**，交卷前逐条核对。
- 想查语法细节请翻 **附录A**；想复习原理请回看对应章节（每条易错点都标了章节号）。
- **返回总目录**：[00-课程总览与索引.md](00-课程总览与索引.md)。

---

## 第一部分 易错点清单（100 条）

### 一、变量、类型与对象（1~15）

1. **以为变量有类型** → 错：说"x 是整数"。对：**变量只是名字（标签），类型属于对象**；同一个 `x` 可以先指向 `1` 再指向 `"a"`（第2章 2.1）。
2. **以为 `input()` 返回数字** → 错：`n = input()` 后直接 `n + 1` 报 `TypeError`。对：**`n = int(input())`**；一行多个数用 `map(int, input().split())`（第1章 1.5）。
3. **用 `type(x) == int` 判断类型** → 错：`type(True) == int` 是 `False`。对：**`isinstance(x, int)`**（认子类关系）（第2章 2.1）。
4. **以为 `bool("False")` 是 `False`** → 错：**非空字符串一律为 `True`**。对：只有 `""`、`0`、`0.0`、`None`、`[]`、`()`、`{}`、`set()` 是假值（第2章 2.2.4）。
5. **直接比较浮点数** → 错：`0.1 + 0.2 == 0.3` 是 `False`。对：**`math.isclose(a, b)`** 或 `round(a, 6) == round(b, 6)`（第2章 2.2.2）。
6. **以为 `round` 是"四舍五入"** → 错：`round(2.675, 2)` 得 `2.67`、`round(0.5)` 得 `0`。对：知道它是**银行家舍入**（.5 取偶）且受浮点误差影响；要精确用 `Decimal`（第2章 2.2.6）。
7. **用 `int()` 当四舍五入** → 错：`int(3.9)` 得 `3`、`int(-3.9)` 得 `-3`（**向零截断**）。对：要四舍五入用 `round()`，要向下取整用 `math.floor()`（第2章 2.2.5）。
8. **以为 `//` 是"截断除法"** → 错：`-7 // 2` 是 `-4` 不是 `-3`。对：**`//` 是向下取整**，`%` 的符号**跟随除数**（`-7 % 2` 是 `1`）（第2章 2.5）。
9. **以为 `/` 返回整数** → 错：`6 / 3` 得 `2.0`。对：**`/` 永远返回 float**，要整数用 `//`（第2章 2.5）。
10. **混淆 `is` 与 `==`** → 错：用 `is` 比较数值或字符串。对：**`==` 比值，`is` 比身份（同一个对象）**；`is` 只该用于 `None`、`True`、`False` 和单例（第2章 2.4）。
11. **依赖整数缓存** → 错：以为 `a = 257; b = 257; a is b` 一定是 `False`。对：**同一代码块里编译器可能合并常量使其为 `True`，交互式逐行输入则为 `False`**——**结论是永远不要依赖它，比较数值用 `==`**（第2章 2.4）。
12. **以为字符串可以改** → 错：`s[0] = "H"` 报 `TypeError`。对：字符串**不可变**，要 `s = "H" + s[1:]`（第2章 2.3）。
13. **循环里用 `+=` 拼字符串** → 错：`s += x` 每次都创建新字符串，10 万次是 O(n²)。对：**收集到列表再 `"".join(lst)`**（第2章 2.3）。
14. **以为 `a = b` 复制了对象** → 错：改 `a` 影响 `b`。对：**`a = b` 只是给同一个对象起了两个名字**；要复制用 `b.copy()` 或 `copy.deepcopy(b)`（第2章 2.4、第4章 4.2.3）。
15. **浅拷贝当成深拷贝** → 错：`b = a.copy()` 后改 `b[0][0]`，`a` 也变了。对：**浅拷贝只复制第一层**，嵌套对象仍共享；要彻底独立用 **`copy.deepcopy()`**（第4章 4.2.3）。

### 二、运算符与表达式（16~24）

16. **`**` 的结合方向搞错** → 错：以为 `2 ** 3 ** 2` 是 `(2**3)**2 = 64`。对：**`**` 右结合，结果是 `2**(3**2) = 512`**（第2章 2.5）。
17. **`-2 ** 2` 的结果** → 错：以为得 `4`。对：**一元负号优先级低于 `**`，结果是 `-4`**；要 `4` 得写 `(-2) ** 2`（第2章 2.5）。
18. **`and`/`or` 返回布尔值** → 错：以为 `1 or 0` 是 `True`。对：**它们返回的是操作数本身**，`1 or 0` 得 `1`（第2章 2.5）。
19. **链式比较理解错** → 错：以为 `1 < 5 > 3` 是 `(1<5) > 3`。对：**Python 支持链式比较**，`1 < 5 > 3` 等价于 `1 < 5 and 5 > 3`，得 `True`（第2章 2.5）。
20. **忘记短路求值** → 错：`if lst and lst[0] > 0` 写成 `if lst[0] > 0` 导致空列表报 `IndexError`。对：利用 **`and` 短路**做保护性判断（第2章 2.5）。
21. **`+=` 对列表是就地修改** → 错：以为 `b = a; a += [3]` 后 `b` 不变。对：**列表的 `+=` 等价于 `extend`，会就地修改**，`a is b` 仍为 `True`；而 `a = a + [3]` 是新建列表（第2章 2.5）。
22. **用 `==` 比较 `float('nan')`** → 错：`nan == nan` 是 `False`。对：用 `math.isnan(x)`（第2章 2.2.2）。
23. **混淆 `%` 的两种用法** → 错：`"5" % 2`。对：**`%` 对数字是取余，对字符串是格式化**（`"%d%%" % 85` 得 `'85%'`）（第2章 2.3.4）。
24. **位运算优先级搞错** → 错：`1 | 2 & 3` 当成 `(1|2) & 3`。对：**`&` 高于 `^` 高于 `|`**，结果是 `1 | (2&3) = 3`（第2章 2.5）。

### 三、字符串（25~36）

25. **`split(",")` 与 `split()` 混用** → 错：以为 `"a,,b".split(",")` 得 `['a','b']`。对：**指定分隔符时连续分隔符会产生空串**（`['a','','b']`）；**不写参数才按任意空白切分并去掉空串**（第2章 2.3.4）。
26. **用 `split("\n")` 读行** → 错：末尾多出一个 `''`。对：用 **`splitlines()`** 或 `readlines()` + `rstrip("\n")`（第9章 9.2）。
27. **`strip()` 去中间空白** → 错：以为 `"a b".strip()` 去掉中间空格。对：**只去两端**；去中间要 `" ".join(s.split())`（第2章 2.3.4）。
28. **`join` 的元素不是字符串** → 错：`"-".join([1, 2])` 报 `TypeError`。对：**先 `map(str, lst)`**：`"-".join(map(str, lst))`（第2章 2.3.4）。
29. **`find` 与 `index` 混用** → 错：用 `index` 又忘了捕获异常。对：**`find` 找不到返回 `-1`，`index` 找不到抛 `ValueError`**；判断"包含"直接用 `in`（第2章 2.3.4）。
30. **`isdigit()` 判断负数** → 错：`"-1".isdigit()` 是 `False`。对：负数/小数要先用 `try: float(s)` 判断（第2章 2.3.4）。
31. **字符串切片越界报错** → 错：以为 `"abc"[10]` 会报错。对：**下标越界报 `IndexError`，但切片越界不报错**，`"abc"[10:20]` 得 `''`（第2章 2.3.2）。
32. **`[::-1]` 记成"反转"但写错** → 错：写成 `[::-1]` 之外的 `[::−1]`（用了全角减号）或 `[1::-1]`。对：**`s[::-1]` 是反转**，`s[::2]` 是隔一个取一个（第2章 2.3.2）。
33. **字符串比较大小** → 错：以为 `"10" < "9"` 是 `False`。对：**字符串按字典序逐位比较**，`'1' < '9'` 所以是 `True`；要按数值比必须先 `int()`（第2章 2.3.3）。
34. **f-string 里用了中文宽度** → 错：以为 `f"{name:<8}"` 能让中文对齐。对：**宽度按字符数算，中文占 1 字符但显示占 2 列**，含中文的表格对不齐（第1章 例1.6）。
35. **`%` 格式化参数个数不对** → 错：`"%d %d" % (1)` 报 `TypeError`。对：**单个参数也要写成元组** `% (1,)`（第2章 2.3.4）。
36. **忘记 `r` 前缀** → 错：写 `"\b"` 以为在正则里表示单词边界。对：**正则一律写 `r"\b"`**，否则 `\b` 是退格符（第8章 8.1）。

### 四、控制结构（37~46）

37. **`elif` 顺序颠倒** → 错：把宽泛条件写在前面，后面的分支永远进不去。对：**多分支必须从具体到宽泛**（第3章 3.3.3）。
38. **`range` 右边界理解错** → 错：以为 `range(1, 5)` 包含 5。对：**左闭右开**，`range(1, 5)` 是 `1,2,3,4`；要包含 5 写 `range(1, 6)`（第3章 3.4.2）。
39. **`range` 的第三个参数是终点** → 错：`range(1, 10, 2)` 当成"从 1 到 2"。对：**第三参数是步长**（第3章 3.4.2）。
40. **`for...else` 的语义搞错** → 错：以为 `else` 是"有 break 时执行"。对：**`else` 在循环"没有被 `break` 中断"时执行**——正好相反（第3章 3.4.3）。
41. **嵌套循环里 `break` 只跳一层** → 错：以为内层 `break` 能跳出全部循环。对：**只跳出当前这一层**；要全跳用标志变量、`return` 或 `for...else`（第3章 3.4.4）。
42. **遍历列表时删元素** → 错：`for x in lst: lst.remove(x)` 会跳过元素。对：**倒序遍历** `for i in range(len(lst)-1, -1, -1)`，或**用列表推导式生成新列表**（第3章 3.4.4）。
43. **累加器写在循环里** → 错：`total = 0` 放在循环内部，每次都被清零。对：**初始化必须在循环之前**（第3章 3.4）。
44. **用浮点数做循环条件** → 错：`while x != 1.0: x += 0.1` 可能死循环。对：**用整数计数或 `for i in range(n)`**，浮点比较用 `isclose`（第3章 3.4.1）。
45. **忘了 `while` 里更新变量** → 错：死循环。对：确认循环体内有让条件趋向结束的语句（第3章 3.4.1）。
46. **`pass` 当成"跳过"** → 错：以为 `pass` 相当于 `continue`。对：**`pass` 什么都不做**（占位符），`continue` 才跳过本次循环剩余部分（第3章 3.4.3）。

### 五、组合数据（47~62）

47. **`{}` 是空字典不是空集合** → 错：`s = {}` 想建空集合。对：**`s = set()`**（第4章 4.5.1）。
48. **`(5)` 不是元组** → 错：`t = (5)` 得到 `int`。对：**单元素元组必须写 `(5,)`**，逗号才是关键（第4章 4.3.1）。
49. **`sort()` 返回 `None`** → 错：`nums = nums.sort()` 后 `nums` 变成 `None`。对：**`sort()` 就地排序返回 `None`**；要新列表用 **`sorted(nums)`**（第4章 4.2.6）。
50. **`reverse()` 与 `reversed()` 混用** → 错：`lst = lst.reverse()`。对：**`lst.reverse()` 就地反转返回 `None`**；`reversed(lst)` 返回迭代器；`lst[::-1]` 返回新列表（第4章 4.2）。
51. **`append` 与 `extend` 混用** → 错：`lst.append([1,2])` 得到嵌套列表。对：**`append` 加一个元素，`extend` 加多个**（第4章 4.2.4）。
52. **`[[0]*3]*3` 建二维表** → 错：改一个元素三行都变（**三行是同一个列表**）。对：**`[[0]*3 for _ in range(3)]`**（第4章 4.6）。
53. **`dict.fromkeys(keys, [])`** → 错：所有键共享同一个列表。对：用**字典推导式** `{k: [] for k in keys}`（第4章 4.4.1）。
54. **字典键用了可变对象** → 错：`d[[1,2]] = 1` 报 `TypeError: unhashable type: 'list'`。对：**键必须可哈希**（`str`/`int`/`tuple`/`frozenset`）；列表不能当键，**元组在"元素全部可哈希"时可以当键**——`hash((1, [2]))` 照样报 `TypeError: unhashable type: 'list'`，只有 `hash((1, 2))` 才成功（第4章 4.4.1）。
55. **`{1: "int", True: "bool"}`** → 错：以为有两个键。对：**`1 == True` 且哈希相同，后写的会覆盖前面的**，字典只剩一个键（第4章 4.4.1）。
56. **`d[key]` 取不存在的键** → 错：报 `KeyError`。对：**`d.get(key, default)`** 安全取值（第4章 4.4.2）。
57. **`get` 与 `setdefault` 混用** → 错：以为 `get` 会建键。对：**`get` 只读不建键，`setdefault` 会建键**（第4章 4.4.2）。
58. **遍历字典时改大小** → 错：`for k in d: del d[k]` 报 `RuntimeError: dictionary changed size during iteration`。对：**遍历 `list(d)` 的副本**（第4章 4.4.2）。
59. **`list.pop(0)` 当队列** → 错：数据量大时极慢（**O(n)**）。对：用 **`collections.deque` 的 `popleft()`**（O(1)）（第4章 4.7.4、第17章 17.3）。
60. **`set` 不能下标访问** → 错：`s[0]` 报 `TypeError`。对：**集合无序无下标**，要下标先 `list(s)`（顺序不保证）（第4章 4.5.2）。
61. **`remove` 与 `discard` 混用** → 错：`set.remove(x)` 元素不存在时 `KeyError`。对：**`discard` 不报错**（第4章 4.5.3）。
62. **生成器耗尽** → 错：`g = (x for x in range(3))` 用完一次后再 `list(g)` 得 `[]`。对：**生成器是一次性的**，要重新创建（第4章 4.7.3）。

### 六、函数（63~76）

63. **`def f(a=[])` 默认参数陷阱** → 错：多次调用共享同一个列表。对：**默认值在定义时只求值一次**；用 `def f(a=None): if a is None: a = []`（第5章 5.3）。
64. **以为 Python 是值传递** → 错：说"函数里的修改传不出来"。对：**传的是对象引用**——**改内容会传出去，改名字传不出去**（第5章 5.3.1）。
65. **以为 `return a, b` 返回两个值** → 错。对：**本质是返回一个元组**，接收时是解包（第5章 5.2.2）。
66. **忘了 `return`** → 错：函数返回 `None` 却拿去运算。对：**没有 `return` 的函数返回 `None`**（第5章 5.2）。
67. **`global` 与 `nonlocal` 混用** → 错：在嵌套函数里想改外层函数的变量却用了 `global`。对：**`global` 改模块级变量，`nonlocal` 改外层函数变量**（第5章 5.6）。
68. **只读外层变量不用声明** → 错：以为读也要 `global`。对：**只有"赋值"才需要声明**；读取时 LEGB 会自动找到（第5章 5.6）。
69. **`UnboundLocalError`** → 错：函数里先用了同名变量再赋值，报 `cannot access local variable 'x' where it is not associated with a value`。对：函数内**赋值会让这个名字变成局部变量**，要改全局必须 `global`（第5章 5.6）。
70. **闭包延迟绑定** → 错：`[lambda: i for i in range(3)]` 调用后全得 `2`。对：**闭包捕获的是变量不是值**；用 `lambda i=i: i` 固定（第5章 5.4.3）。
71. **递归没有终止条件** → 错：`RecursionError: maximum recursion depth exceeded`。对：递归三要素之一是**终止条件**；默认深度上限 `sys.getrecursionlimit()` = **1000**（第5章 5.4.2）。
72. **用递归算大数** → 错：`fib(35)` 卡死。对：**朴素递归是指数级**；用 `@lru_cache` 或改成循环（第5/17章）。
73. **装饰器忘了 `@wraps`** → 错：`__name__` 变成 `'wrapper'`、`__doc__` 变 `None`。对：**装饰器里加 `@functools.wraps(fn)`**（第17章 17.4）。
74. **`lambda` 里写语句** → 错：`lambda x: print(x); return x` 语法错误。对：**`lambda` 只能是单个表达式**，不能有语句、不能有 `return`（第5章 5.4.1）。
75. **`map`/`filter` 直接当列表用** → 错：`print(map(int, "12"))` 输出 `<map object ...>`。对：**它们返回迭代器，要 `list()`**（第2章 2.6）。
76. **函数名当变量用** → 错：`len = 5` 之后 `len("abc")` 报错。对：**不要用内置函数名做变量名**（`list`、`dict`、`str`、`sum`、`id`、`type` 都常见）（第1章 1.4.3）。

### 七、面向对象（77~86）

77. **忘写 `self`** → 错：`def show():` 定义实例方法后调用报 `TypeError: show() takes 0 positional arguments but 1 was given`。对：**实例方法第一个参数必须是 `self`**（第6章 6.2）。
78. **`__init__` 里 `return`** → 错：报 `TypeError: __init__() should return None`。对：**`__init__` 不能有返回值**（第6章 6.2.2）。
79. **类属性当实例属性用** → 错：以为 `self.count += 1` 会改类属性。对：**读时先找实例再找类，写时永远写实例**；改类属性要 `类名.count += 1`（第6章 6.3.1）。
80. **私有属性真的"私有"** → 错：以为 `__x` 无法访问。对：**只是名称改写**，`obj._类名__x` 仍能访问（第6章 6.3.1）。
81. **`@property` 与 setter 不同名** → 错：setter 用了别的名字（如 getter 叫 `v`、setter 叫 `set_v`），赋值时报 `AttributeError: property 'v' of 'Y' object has no setter`——因为 `@v.setter` 返回的**新** property 被绑到了 `set_v` 这个名字上，原来的 `v` 仍是只读的。对：**`@x.setter` 必须与 getter 同名**（第6章 6.4.2）。
82. **`print(obj)` 与 `print([obj])` 输出不同** → 错：以为都调 `__str__`。对：**`print(obj)` 用 `__str__`，容器内显示用 `__repr__`**（第6章 6.4.3）。
83. **`super()` 理解成"父类"** → 错：多重继承下行为不符预期。对：**`super()` 是按 MRO 调用下一个类**（第6/16章）。
84. **忘了调 `super().__init__()`** → 错：子类实例缺少父类的属性。对：**子类 `__init__` 里先 `super().__init__(...)`**（第6章 6.5.1）。
85. **抽象类直接实例化** → 错：`Shape()` 报 `TypeError: Can't instantiate abstract class Shape without an implementation for abstract method 'area'`。对：**抽象方法检查发生在实例化时**（第6章 6.6）。
86. **`__eq__` 定义了没定义 `__hash__`** → 错：对象不可哈希，不能当字典键/放进 `set`。对：**两个一起定义，且哈希用的字段要和 `__eq__` 一致**（第16章 16.8）。

### 八、模块与标准库（87~92）

87. **自己的文件名顶掉标准库** → 错：把文件命名为 `random.py`，导致 `import random` 导入的是自己的文件。对：**不要用标准库名做文件名**；模块搜索路径里**当前目录排在标准库之前**（第7章 7.1）。
88. **`__name__` 判断漏写** → 错：模块被导入时把测试代码也执行了。对：**入口代码放在 `if __name__ == "__main__":` 里**（第7章 7.1）。
89. **`import` 与 `from ... import` 混用导致名字冲突** → 错：`from a import *` 覆盖了已有名字。对：**不用 `import *`**，明确写出导入的名字（第7章 7.1）。
90. **`os.mkdir` 建多级目录** → 错：`os.mkdir("a/b/c")` 报 `FileNotFoundError`。对：**`os.makedirs(..., exist_ok=True)`** 或 `Path(...).mkdir(parents=True, exist_ok=True)`（第7章 7.2.4、第17章 17.1）。
91. **`sys.argv` 当数字用** → 错：`sys.argv[1] + 1` 报 `TypeError`。对：**`sys.argv` 全是字符串**，要 `int(sys.argv[1])`（第7章 7.2.5）。
92. **`random` 用于安全场景** → 错：用 `random` 生成密码/令牌。对：**`random` 是可预测的伪随机**，安全用途必须用 `secrets`（第7章 7.2.2）。

### 九、文件与数据格式（93~100）

93. **`"w"` 模式清空文件** → 错：只想读却打开了 `"w"`，内容全丢。对：**`"w"` 会清空**，只读用 `"r"`，追加用 `"a"`（第9章 9.2.1）。
94. **`"a"` 与 `"r+"` 混用** → 错：想追加却用了 `"r+"`，内容被从头覆盖。对：**`"r+"` 的写指针在开头**，追加必须用 `"a"`（第9章 9.2.1）。
95. **不写 `encoding`** → 错：Windows 上默认 GBK，读 UTF-8 文件报 `UnicodeDecodeError: 'utf-8' codec can't decode byte 0xd6 in position 0`。对：**一律写 `encoding="utf-8"`**（第9章 9.2.1）。
96. **`writelines` 不补换行** → 错：`f.writelines(["a", "b"])` 得到 `'ab'`。对：**自己加 `\n`**（第9章 9.2.2）。
97. **写 CSV 不加 `newline=""`** → 错：Windows 上多出 `\r`（实测写出 `b'a,b\r\r\n'`）。对：**`open(..., "w", newline="", encoding="utf-8")`**（第9章 9.4.2）。
98. **中文 CSV 在 Excel 里乱码** → 错：用 `utf-8` 写。对：**用 `encoding="utf-8-sig"`**（带 BOM）；⚠️ 但用 `utf-8` 读它会在开头多出 `'\ufeff'`（第9章 9.4.2）。
99. **`json.dumps` 中文变 `\uXXXX`** → 错：配置文件没法看。对：**`ensure_ascii=False`**（第9章 9.4.2、第17章 17.9）。
100. **用 `pickle` 读不可信文件** → 错：以为它只是反序列化。对：**`pickle.load` 会执行任意代码**，等同任意代码执行漏洞；**配置用 JSON/TOML，不要用 pickle**（第9章 9.3.1）。

---

## 第二部分 十大编程题型模板

> **通用答题四步**：① 看清输入格式和输出格式；② 写出"输入 → 处理 → 输出"的骨架；③ 填处理逻辑；④ 用样例数据手工验证一遍。

### 题型 1：字符串处理

**识别特征**：题目给出一个字符串，要求统计、替换、切分、反转、大小写转换、判断格式。

**解题套路**：① 先决定"用什么切"（`split` / `splitlines` / 正则）；② 遍历时优先用**列表推导式**；③ 拼接用 `join`，**绝不用 `+=`**。

```python
import re

def clean_and_count(text: str) -> dict:
    """去掉标点、统一小写、统计词频，返回出现次数最多的前 3 个"""
    words = re.findall(r"[a-zA-Z]+", text.lower())      # ① 一步完成分词 + 去标点
    freq = {}
    for w in words:
        freq[w] = freq.get(w, 0) + 1                     # ② 计数的标准写法
    return dict(sorted(freq.items(), key=lambda kv: -kv[1])[:3])

print(clean_and_count("The quick brown fox. The FOX!"))
# {'the': 2, 'fox': 2, 'quick': 1}
```

**易错提醒**：`split(",")` 会产生空串；`join` 的元素必须全是字符串；比较字符串大小是字典序。

### 题型 2：列表遍历与统计

**识别特征**：给一组数据，求最大/最小/平均/中位数/满足条件的个数。

**解题套路**：① 一行读入用 `map(int, input().split())`；② 多行用列表推导式；③ 求平均值注意**空列表除零**。

```python
def stats(nums: list) -> dict:
    if not nums:
        return {"count": 0, "sum": 0, "avg": None, "max": None, "min": None}
    s = sorted(nums)
    n = len(s)
    mid = s[n // 2] if n % 2 else (s[n // 2 - 1] + s[n // 2]) / 2
    return {"count": n, "sum": sum(nums), "avg": round(sum(nums) / n, 2),
            "max": max(nums), "min": min(nums), "median": mid}

print(stats([85, 92, 78, 95, 88]))
# {'count': 5, 'sum': 438, 'avg': 87.6, 'max': 95, 'min': 78, 'median': 88}
```

**易错提醒**：**遍历时不要删元素**；`sum([])` 得 `0` 但 `sum([])/0` 会 `ZeroDivisionError`。

### 题型 3：字典统计与分组

**识别特征**："统计每个……出现次数"、"按……分组"、"找出出现最多的"。

**解题套路**：① 计数用 `d[k] = d.get(k, 0) + 1` 或 `Counter`；② 分组用 `defaultdict(list)`；③ 排序输出用 `sorted(d.items(), key=lambda kv: -kv[1])`。

```python
from collections import defaultdict, Counter

def group_by_first_letter(names: list) -> dict:
    g = defaultdict(list)
    for n in names:
        g[n[0].upper()].append(n)
    return {k: sorted(v) for k, v in sorted(g.items())}

print(group_by_first_letter(["bob", "alice", "amy", "ben"]))
# {'A': ['alice', 'amy'], 'B': ['ben', 'bob']}

print(Counter(["a", "b", "a"]).most_common(1))    # [('a', 2)]
```

**易错提醒**：`get` 不会建键但 `setdefault`/`defaultdict[]` 会；**`Counter` 计数相同时按首次出现顺序**。

### 题型 4：文件读写

**识别特征**："读入文件……"、"把结果写入文件"、"统计文件中的……"。

**解题套路**：① **一律用 `with open(...)` 且写 `encoding="utf-8"`**；② 逐行迭代（不要一次 `read()` 大文件）；③ 写 CSV 加 `newline=""`，写 JSON 加 `ensure_ascii=False`。

```python
import csv, json
from pathlib import Path

def process(in_path: str, out_path: str) -> int:
    rows = []
    with open(in_path, encoding="utf-8") as f:
        reader = csv.DictReader(f)                  # ① 按表头读成字典
        for r in reader:
            r["总分"] = int(r["Python"]) + int(r["数学"])   # ⚠️ CSV 读出来是字符串
            rows.append(r)
    rows.sort(key=lambda r: -r["总分"])

    with open(out_path, "w", encoding="utf-8-sig", newline="") as f:   # ② 两参数都要
        w = csv.DictWriter(f, fieldnames=["姓名", "Python", "数学", "总分"])
        w.writeheader()
        w.writerows(rows)
    return len(rows)
```

**易错提醒**：`"w"` 清空文件；CSV 读出的数字是**字符串**；中文 CSV 给 Excel 看要用 `utf-8-sig`。

### 题型 5：递归

**识别特征**："用递归求……"、阶乘、斐波那契、汉诺塔、树的遍历、字符串反转。

**解题套路**：① 写清**终止条件**；② 写清**递推关系**；③ 大数值加 `@lru_cache`。

```python
from functools import lru_cache

def fact(n: int) -> int:
    if n <= 1:                      # ① 终止条件
        return 1
    return n * fact(n - 1)          # ② 递推关系

@lru_cache(maxsize=None)
def fib(n: int) -> int:
    return n if n < 2 else fib(n - 1) + fib(n - 2)

def hanoi(n: int, a: str, b: str, c: str, moves: list) -> None:
    if n == 1:
        moves.append(f"{a}->{c}")
        return
    hanoi(n - 1, a, c, b, moves)    # 把上面 n-1 个移到 b
    moves.append(f"{a}->{c}")       # 把最大的移到 c
    hanoi(n - 1, b, a, c, moves)    # 把 n-1 个从 b 移到 c

print(fact(5), fib(20))             # 120 6765
m = []; hanoi(3, "A", "B", "C", m)
print(len(m), m[:3])                # 7 ['A->C', 'A->B', 'C->B']
```

**易错提醒**：递归深度默认上限 **1000**（`sys.getrecursionlimit()`）；汉诺塔 n 层的步数是 **2ⁿ − 1**。

### 题型 6：排序与查找

**识别特征**："按……排序"、"找第 k 大"、"查找是否存在"、"去重"。

**解题套路**：① **`sorted(..., key=...)` 不修改原数据**；② 多级排序用元组 key；③ 查找先考虑**集合/字典**（O(1)）而不是列表（O(n)）。

```python
def top_k(records: list, k: int) -> list:
    """records: [(姓名, 分数, 年龄)]，分数降序、同分年龄升序，取前 k"""
    return sorted(records, key=lambda r: (-r[1], r[2]))[:k]

data = [("张三", 92, 18), ("李四", 85, 20), ("王五", 92, 19)]
print(top_k(data, 2))
# [('张三', 92, 18), ('王五', 92, 19)]

# 二分查找（数据必须有序）
import bisect
nums = [1, 3, 5, 7, 9]
i = bisect.bisect_left(nums, 5)
print(i, nums[i])            # 2 5
```

**易错提醒**：**`lst.sort()` 返回 `None`**（`nums = nums.sort()` 是致命错误）；`-r[1]` 实现降序比 `reverse=True` 更灵活（能混用升降序）。

### 题型 7：生成器与推导式

**识别特征**："数据量很大"、"用生成器实现"、"求前 n 项"、"惰性求值"。

**解题套路**：① 数据量小用列表推导式，大用生成器表达式；② 自定义序列用**生成器函数 `yield`**（不必写迭代器类）。

```python
def primes(limit: int):
    """生成 limit 以内的所有素数（埃氏筛）"""
    flags = [True] * (limit + 1)
    flags[0] = flags[1] = False
    for i in range(2, int(limit ** 0.5) + 1):
        if flags[i]:
            for j in range(i * i, limit + 1, i):
                flags[j] = False
    return (i for i, ok in enumerate(flags) if ok)     # ① 返回生成器表达式

print(list(primes(30)))
# [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]

def fib_gen(n: int):
    a, b = 0, 1
    for _ in range(n):
        yield a                       # ② yield 让函数变成生成器
        a, b = b, a + b

print(list(fib_gen(8)))               # [0, 1, 1, 2, 3, 5, 8, 13]
```

**易错提醒**：**生成器只能遍历一次**；`return` 生成器对象时不要用 `[]`（那是列表推导式，会立刻算完）。

### 题型 8：类与对象

**识别特征**："定义一个类……"、"实现……的封装/继承/多态"。

**解题套路**：① 先写 `__init__` 列出属性；② 再写方法；③ 需要打印友好就写 `__str__`/`__repr__`；④ 需要比较就写 `__eq__`。

```python
class Student:
    """学生类：姓名、成绩列表、计算平均分与等级"""
    school = "湖北大学"                       # 类属性（所有实例共享）

    def __init__(self, name: str, scores: list = None):
        self.name = name
        self.scores = scores if scores is not None else []   # ★ 避免默认参数陷阱

    def add(self, score: float) -> "Student":
        if not 0 <= score <= 100:
            raise ValueError(f"成绩必须在 0~100，收到 {score}")
        self.scores.append(score)
        return self                                        # 支持链式调用

    @property
    def average(self) -> float:
        return round(sum(self.scores) / len(self.scores), 2) if self.scores else 0.0

    @property
    def level(self) -> str:
        avg = self.average
        return "优秀" if avg >= 90 else "良好" if avg >= 80 else "及格" if avg >= 60 else "不及格"

    def __str__(self) -> str:
        return f"{self.name} 平均 {self.average}（{self.level}）"

    def __repr__(self) -> str:
        return f"Student({self.name!r}, {self.scores})"

    def __eq__(self, other) -> bool:
        return isinstance(other, Student) and self.name == other.name

s = Student("张三").add(92).add(88).add(95)
print(s)                       # 张三 平均 91.67（优秀）
print(s.average, s.level)      # 91.67 优秀
print([s])                     # [Student('张三', [92, 88, 95])]   ← 用 __repr__
```

**易错提醒**：实例方法第一个参数必须是 `self`；`__init__` 不能 `return`；`@property` 的 setter 必须同名。

### 题型 9：异常处理

**识别特征**："程序要健壮"、"输入非法时提示"、"捕获异常"。

**解题套路**：① 只捕获**能处理的**异常；② 从具体到宽泛；③ 提示信息要说明**怎么改**。

```python
def input_int(prompt: str, lo: int = None, hi: int = None, retry: int = 3) -> int:
    for i in range(retry):
        try:
            v = int(input(prompt))
        except ValueError:
            print(f"不是合法整数，请重新输入（还剩 {retry - i - 1} 次）")
            continue
        if lo is not None and v < lo or hi is not None and v > hi:
            print(f"超出范围 [{lo}, {hi}]，请重新输入")
            continue
        return v
    raise ValueError("重试次数用尽")

# 测试（把 input 替换成固定序列）
import builtins
_orig = builtins.input
it = iter(["abc", "200", "50"])
builtins.input = lambda p="": next(it)
try:
    print(input_int("请输入：", 1, 100))
except ValueError as e:
    print("失败：", e)
finally:
    builtins.input = _orig
# 不是合法整数，请重新输入（还剩 2 次）
# 超出范围 [1, 100]，请重新输入
# 50
```

**易错提醒**：**不要用裸 `except:`**（会吞掉 Ctrl+C）；`finally` 里的 `return` 会覆盖 `try` 的 `return`；`assert` 不能校验用户输入。

### 题型 10：爬虫与网络请求

**识别特征**："获取网页内容"、"解析 HTML"、"调用接口拿数据"。

**解题套路**：① 先看 `robots.txt`；② 带 `User-Agent`；③ 限速 `time.sleep`；④ 用 `try/except` 兜住网络异常。

```python
import json
import time
import urllib.request
import urllib.robotparser
from urllib.error import HTTPError, URLError

UA = "Mozilla/5.0 (compatible; StudyBot/1.0)"

def can_fetch(url: str, ua: str = "*") -> bool:
    """检查 robots.txt 是否允许抓取"""
    from urllib.parse import urlparse
    p = urlparse(url)
    rp = urllib.robotparser.RobotFileParser()
    rp.set_url(f"{p.scheme}://{p.netloc}/robots.txt")
    try:
        rp.read()
    except Exception:
        return True                      # 读不到就按"未禁止"处理（教学用）
    return rp.can_fetch(ua, url)

def fetch(url: str, timeout: int = 10) -> str | None:
    """抓取网页文本；遵守 robots.txt，失败返回 None"""
    if not can_fetch(url, UA):
        print("robots.txt 不允许抓取：", url)
        return None
    req = urllib.request.Request(url, headers={"User-Agent": UA})
    try:
        with urllib.request.urlopen(req, timeout=timeout) as resp:
            charset = resp.headers.get_content_charset() or "utf-8"
            return resp.read().decode(charset, errors="replace")
    except HTTPError as e:               # ★ HTTPError 是 URLError 的子类，必须写前面
        print(f"HTTP 错误：{e.code} {e.reason}")
    except URLError as e:
        print(f"网络错误：{e.reason}")
    except TimeoutError:
        print("请求超时")
    return None

def extract_titles(html: str) -> list:
    """用标准库解析出所有 <title> 与 <a href>（无需第三方库）"""
    from html.parser import HTMLParser
    class P(HTMLParser):
        def __init__(self):
            super().__init__()
            self.in_title = False
            self.titles, self.links = [], []
        def handle_starttag(self, tag, attrs):
            if tag == "title":
                self.in_title = True
            if tag == "a":
                for k, v in attrs:
                    if k == "href":
                        self.links.append(v)
        def handle_endtag(self, tag):
            if tag == "title":
                self.in_title = False
        def handle_data(self, data):
            if self.in_title and data.strip():
                self.titles.append(data.strip())
    p = P()
    p.feed(html)
    return p.titles, p.links

# 演示（不联网，用本地 HTML 字符串）
html = "<html><head><title>测试页</title></head><body><a href='/a.html'>A</a></body></html>"
print(extract_titles(html))     # (['测试页'], ['/a.html'])
```

**⚠️ 合法合规四条底线**：① **先看 `robots.txt`**，被禁止的路径不抓；② **控制频率**（每次请求间隔 1 秒以上）；③ **不抓个人隐私、不绕过登录/付费墙**；④ **不把抓到的数据用于商业用途或二次分发**。**违反《刑法》第 285、286 条（非法获取计算机信息系统数据罪、破坏计算机信息系统罪）要负刑事责任。**

**易错提醒**：网络返回的是 `bytes`，**必须 `decode`**；`HTTPError` 是 `URLError` 的子类，**要写在前面**；`requests` 不是标准库，**考试环境可能没装**，`urllib` 一定可用。

---

## 第三部分 上机考试检查清单

### 交卷前逐条核对（12 条）

1. **输入格式对上了吗？** 题目说"一行两个整数"就用 `map(int, input().split())`；说"第一行是 n"就要先读 n 再循环 n 次。
2. **`input()` 的结果转类型了吗？** 所有从 `input()` 来的值**都是字符串**。
3. **输出格式对上了吗？** 分隔符、是否要小数位（`:.2f`）、是否要换行（`end=""`）——**多一个空格都可能判错**。
4. **边界情况试了吗？** 空输入、0、负数、单个元素、最大值。**空列表求平均会 `ZeroDivisionError`。**
5. **缩进是 4 个空格吗？** 混用 Tab 和空格会报 `TabError`。
6. **变量名撞内置函数了吗？** `list`、`dict`、`str`、`sum`、`len`、`id`、`type`、`input`、`max`、`min` 都不能当变量名。
7. **文件名撞标准库了吗？** 不要叫 `random.py`、`json.py`、`re.py`、`string.py`。
8. **`sort()` 的返回值用了吗？** `lst = lst.sort()` 会让 `lst` 变成 `None`。
9. **文件路径写对了吗？** Windows 路径要么用 `r"..."`，要么用 `/`，要么用 `pathlib`。
10. **打开文件写 `encoding="utf-8"` 了吗？** 不写会按系统默认编码（Windows 是 GBK）打开。
11. **有没有死循环？** `while` 里必须有让条件变化的语句；`itertools.count()`/`cycle()` 必须用 `islice` 截断。
12. **有没有裸 `except:`？** 它会把 `KeyboardInterrupt`（Ctrl+C）一起吞掉，程序无法中断——至少写 `except Exception:`。

### 常见报错 → 快速定位

| 报错 | 最常见原因 | 怎么改 |
|---|---|---|
| `SyntaxError: invalid syntax` | 少括号/少冒号/中文标点 | 检查上一行结尾和括号配对 |
| `IndentationError: unexpected indent` | 缩进多了或少了 | 统一用 4 个空格 |
| `NameError: name 'x' is not defined` | 变量名拼错、忘了赋值、作用域不对 | 检查拼写与定义位置 |
| `TypeError: unsupported operand type(s) for +: 'int' and 'str'` | 忘了 `int()`/`str()` 转换 | `input()` 后转类型 |
| `ValueError: invalid literal for int() with base 10: 'abc'` | `int()` 收到非数字字符串 | 先判断或 `try/except` |
| `IndexError: list index out of range` | 下标越界（常见于 `range(len(lst)+1)`） | 检查边界，记住是 0 开始 |
| `KeyError: 'x'` | 字典键不存在 | 用 `d.get(k, default)` |
| `AttributeError: 'NoneType' object has no attribute ...` | 上一个函数返回了 `None`（**常是 `sort()`**） | 检查链式调用 |
| `UnboundLocalError: cannot access local variable ...` | 函数内赋值使名字变成局部变量 | 加 `global`/`nonlocal`，或改结构 |
| `RecursionError: maximum recursion depth exceeded` | 递归没有终止条件 | 检查终止条件；必要时 `sys.setrecursionlimit()` |
| `ZeroDivisionError: division by zero` | 除数为 0（空列表求平均） | 先判空 |
| `FileNotFoundError: [Errno 2]` | 路径错 / 文件不存在 | 用 `os.path.exists` 或 `Path.exists()` 先判断 |
| `UnicodeDecodeError: 'utf-8' codec can't decode byte ...` | 编码不匹配（文件是 GBK） | 改 `encoding="gbk"` |
| `RuntimeError: dictionary changed size during iteration` | 遍历字典时增删键 | 遍历 `list(d)` 副本 |
| `TypeError: 'NoneType' object is not iterable` | 函数忘了 `return` | 检查返回值 |
| `json.decoder.JSONDecodeError` | 字符串不是合法 JSON（单引号） | JSON 只认双引号 |
| `_tkinter.TclError` | 窗口已销毁还操作控件 | 检查 `mainloop`/`destroy` 顺序 |

### 上机策略三条

- **先跑通再优化**：先把最朴素的写法写出来验证样例，再考虑用推导式/标准库美化。**能得分的代码 > 漂亮的代码**。
- **写一个函数测一次**：不要一口气写完 100 行再运行，出错时定位成本极高。
- **把输入输出格式抄进注释**：题目给的样例输入输出直接贴在文件顶部，随时对照，避免"逻辑全对但格式错"丢分。

> **一句话记住本附录**：**Python 的坑大多来自"以为它是别的语言"——以为变量有类型、以为参数是值传递、以为 `sort` 有返回值、以为 `input` 返回数字。把这四条刻进脑子，能避开一半的错误。**
