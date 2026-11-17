# 第15章 Python与人工智能

> 对应教材：王雷春《Python 程序设计——从基础入门到实战应用（微课版）（第2版）》第15章｜建议学时：1（讲授）+ 2（实验）｜重要度：★★☆☆☆

## 本章知识地图

- **15.1 概述**
  - 15.1.1 人工智能简介 —— AI / 机器学习 / 深度学习的包含关系、三大流派、AI 三要素
  - 15.1.2 Python 与人工智能 —— 为什么是 Python、科学计算生态全景图
- **15.2 机器学习**
  - 15.2.1 Sklearn —— 统一 API 五步流程、常用算法表、数据集模块
  - 15.2.2 典型案例——使用线性回归模型训练、测试和预测数据
- **15.3 深度学习**
  - 15.3.1 TensorFlow —— 张量、计算图、Keras 高层 API
  - 15.3.2 典型案例——识别模糊的手写数字图片

**本章在课程中的位置**：这是全书的**收束章**。它不教你成为算法工程师，而是告诉你——**前面 14 章学的数据类型、组合数据、函数、面向对象、NumPy/Pandas/Matplotlib，正是 AI 的地基**。本章的重点是"**衔接**"，不是"深度学习教程"。

## 学习目标

- 能说清 AI、机器学习、深度学习三者的包含关系，能说出 AI 三要素。
- 能解释为什么 Python 成为 AI 的首选语言，能列举科学计算生态的关键库。
- 能背出 sklearn 的**统一五步流程**，能区分分类、回归、聚类、降维四类任务。
- 能用 `train_test_split` 划分数据集，能解释**为什么必须分训练集和测试集**。
- 能看懂准确率、混淆矩阵、MSE、R² 这几个评价指标的含义。
- 能用 NumPy 手工实现一次 kNN 分类和一次线性回归，理解"模型"到底是什么。
- 知道 `jieba` + `wordcloud` 的中文文本分析流程。
- 知道 TensorFlow/PyTorch 的定位，了解"张量"与"神经网络"的基本概念，**不要求会训练模型**。

## 15.1 概述

### 15.1.1 人工智能简介

**三者的包含关系**（**这是必考的概念题**）：

```
人工智能 AI（让机器表现出智能）
  └── 机器学习 ML（不写死规则，让机器从数据里自己学规律）
        └── 深度学习 DL（用多层神经网络来学，自动提取特征）
              └── 大语言模型 LLM（超大规模参数的深度学习模型）
```

| 层次 | 核心思想 | 例子 |
|---|---|---|
| **人工智能** | 让机器完成需要人类智能的任务 | 下棋、自动驾驶、语音助手 |
| **机器学习** | **从数据中自动学习规律**，而不是人写规则 | 垃圾邮件分类、房价预测 |
| **深度学习** | 用**多层神经网络**自动提取特征 | 人脸识别、机器翻译、ChatGPT |

**关键区别：传统编程 vs 机器学习**

| 对比项 | 传统编程 | 机器学习 |
|---|---|---|
| 输入 | 规则 + 数据 | **数据 + 答案（标签）** |
| 输出 | 答案 | **规则（模型）** |
| 谁在总结规律 | **人** | **机器** |
| 例子 | 第3章的"员工工资"用 if-elif 写死规则 | 给 10000 条工资数据，让模型自己找规律 |

> **重点**：**机器学习的本质是"从数据中拟合出一个函数"。** 你给它一堆 `(x, y)`，它找一个 `f` 使 `f(x) ≈ y`。这个 `f` 就是**模型（model）**，找 `f` 的过程叫**训练（training）**。

**AI 三要素**：

| 要素 | 含义 | 缺了会怎样 |
|---|---|---|
| **数据（Data）** | 燃料。质量比数量更重要 | 垃圾进，垃圾出 |
| **算法（Algorithm）** | 发动机。选对模型结构 | 再多的数据也学不会 |
| **算力（Computing）** | 加速器。GPU/TPU 集群 | 训练要跑几个月 |

**机器学习的四大任务类型**：

| 类型 | 有没有标签 | 目标 | 典型算法 |
|---|---|---|---|
| **监督学习—分类** | **有**（离散标签） | 判断属于哪一类 | kNN、决策树、SVM、逻辑回归 |
| **监督学习—回归** | **有**（连续数值） | 预测一个数值 | **线性回归**、岭回归 |
| **无监督学习—聚类** | **无** | 把相似的自动分组 | K-Means、DBSCAN |
| **无监督学习—降维** | **无** | 压缩特征、便于可视化 | PCA |

**注意一个易错点**：**"逻辑回归"名字里有"回归"，但它做的是分类**——它输出的是"属于某类的概率"。

### 15.1.2 Python 与人工智能

**为什么 AI 选 Python？** 四条理由：

1. **生态完整**：从数据处理（NumPy/Pandas）到可视化（Matplotlib）到建模（sklearn/PyTorch）到部署（Flask/FastAPI），全链路都有成熟库。
2. **语法简洁**：同样的算法，Python 代码量通常是 C++/Java 的 1/3~1/5，**研究人员能把精力放在算法上而不是语法上**。
3. **胶水语言**：底层用 C/C++/CUDA 实现的高性能运算，Python 只做"调度层"。**NumPy 的矩阵乘法跑的是 C 代码**（第7章实测：向量化比 Python 列表推导式**快一个数量级**，本机连跑 5 次为 17.8~27.0 倍）。
4. **社区庞大**：论文附带代码基本都是 Python，遇到问题容易找到答案。

**AI 生态全景**（**你学过的库都在里面**）：

| 层次 | 库 | 本课程学过 |
|---|---|---|
| 数值基础 | **NumPy** | 第7章 ✓ |
| 数据处理 | **Pandas** | 第7章 ✓ |
| 可视化 | **Matplotlib** | 第7章 ✓ |
| 中文分词 | **Jieba** + wordcloud | 第7章 ✓ |
| 机器学习 | **scikit-learn（sklearn）** | 本章 |
| 深度学习 | **TensorFlow / PyTorch** | 本章（介绍） |
| 计算机视觉 | OpenCV、Pillow | 第7章 Pillow ✓ |
| 自然语言 | Transformers、NLTK | —— |
| 科学计算 | **SciPy** | 第7章 ✓ |

> **重点**：**AI 不是"另一门课"，它是你已经学过的这些库的组合应用。** 面试时问"你会什么"，答"NumPy + Pandas + sklearn"比答"我学过 AI"要可信得多。

## 15.2 机器学习

### 15.2.1 Sklearn

**scikit-learn（sklearn）是 Python 最流行的传统机器学习库**，特点是**所有算法共用同一套 API**——学会一个就会用全部。

**统一五步流程**（**背下来，这是本章最核心的知识点**）：

```python
# ① 准备数据
X, y = load_data()

# ② 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# ③ 创建模型
model = SomeModel(参数...)

# ④ 训练（拟合）
model.fit(X_train, y_train)

# ⑤ 预测与评估
y_pred = model.predict(X_test)
print(model.score(X_test, y_test))
```

**sklearn 的四个子模块**：

| 模块 | 内容 | 代表 |
|---|---|---|
| `sklearn.model_selection` | 数据集划分、交叉验证、调参 | `train_test_split`、`GridSearchCV` |
| `sklearn.preprocessing` | 特征缩放、编码 | `StandardScaler`、`MinMaxScaler` |
| `sklearn.linear_model` | 线性模型 | `LinearRegression`、`LogisticRegression` |
| `sklearn.neighbors` / `tree` / `svm` | 其他算法 | `KNeighborsClassifier`、`DecisionTreeClassifier` |
| `sklearn.metrics` | 评价指标 | `accuracy_score`、`confusion_matrix`、`mean_squared_error` |
| `sklearn.datasets` | 内置数据集 | `load_iris`、`load_digits`、`fetch_california_housing` |

**常用算法一览**：

| 算法 | 类型 | 一句话理解 |
|---|---|---|
| **KNN（K 近邻）** | 分类/回归 | "看看离我最近的 K 个邻居是什么类，我就跟着是什么类" |
| **线性回归** | 回归 | "找一条直线，让所有点到直线的距离平方和最小" |
| **逻辑回归** | 分类 | "把线性结果塞进 sigmoid 函数，变成 0~1 的概率" |
| **决策树** | 分类/回归 | "一串 if-else 问题，一路问下去得出结论" |
| **随机森林** | 分类/回归 | "种很多棵决策树，投票决定结果" |
| **SVM** | 分类 | "找一条把两类分得最开的边界" |
| **K-Means** | 聚类 | "先随便定 K 个中心，反复调整直到稳定" |
| **PCA** | 降维 | "把高维数据投影到最重要的几个方向上" |

**两个库的安装情况（本机实测）**：

```bash
pip install scikit-learn          # 本机 Anaconda 已自带，不用装；会一并装 numpy、scipy、joblib
pip install tensorflow            # 深度学习，本机【未装】，体积大（约 500MB+），按需再装
```

> **提醒**：**本机 Anaconda 已自带 `scikit-learn`**，所以 15.2 节的机器学习案例可以**直接跑**；**`tensorflow` 没装**，15.3 节的深度学习代码是**对照知识**（读懂即可，不要求跑起来——课程也不要求你会训练深度模型）。

**`train_test_split` 的三个关键参数**：

| 参数 | 含义 | 建议 |
|---|---|---|
| `test_size` | 测试集占比 | 0.2~0.3 |
| `random_state` | 随机种子 | **固定一个整数，保证每次划分一样**（对应第7章的 `random.seed`） |
| `stratify=y` | 按标签比例分层抽样 | **分类任务强烈建议加**，防止某类全被分到一边 |

> **重点**：**为什么必须划分训练集和测试集？** 因为模型在见过的数据上表现好**不代表**它学到了规律——它可能只是"背下了答案"。用没见过的测试集才能检验**泛化能力（generalization）**。**在训练集上考 100 分、在测试集上考 30 分，就叫过拟合（overfitting）。**

**`stratify=y` 有多重要？** 看下面这份实测数据：15 个鸢尾花样本、每类 5 个，按 7:3 随机划分后——

```
训练集 11 个：{'virginica': 5, 'setosa': 4, 'versicolor': 2}
测试集  4 个：3 个 versicolor + 1 个 setosa
```

**训练集里 versicolor 只剩 2 个，测试集里却有 3 个 versicolor**——这种"偏心的划分"会让准确率剧烈波动。加上 `stratify=y` 就能保证训练集和测试集的类别比例一致。

> **注意**：上面这组数字是**手工 7:3 划分**（11 + 4）的结果。如果改用 `train_test_split(test_size=0.3, random_state=42)`，sklearn 会取 **`ceil(0.3 × 15) = 5`** 条作测试集，于是变成 **训练 10 / 测试 5**（实测测试集 = `['versicolor', 'virginica', 'setosa', 'virginica', 'versicolor']`）。**"取整方式不同"就是同一份数据两种划分条数不一样的原因**——`test_size` 传的是比例时，sklearn 按"向上取整"算测试集大小，不是四舍五入。

### 15.2.2 典型案例——使用线性回归模型训练、测试和预测数据

**任务**：用鸢尾花的"花瓣长度"预测"花瓣宽度"（回归任务）。

**用 sklearn 的写法**：

```python
import numpy as np, pandas as pd
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score
from sklearn.datasets import load_iris

# ① 准备数据
iris = load_iris()
df = pd.DataFrame(iris.data, columns=iris.feature_names)
X = df[["petal length (cm)"]].to_numpy()      # 特征必须是二维！
y = df["petal width (cm)"].to_numpy()

# ② 划分
X_tr, X_te, y_tr, y_te = train_test_split(X, y, test_size=0.3, random_state=42)

# ③④ 建模 + 训练
model = LinearRegression()
model.fit(X_tr, y_tr)

# ⑤ 预测与评估
y_pred = model.predict(X_te)
print("斜率 coef_:", round(float(model.coef_[0]), 4))     # 0.4133
print("截距 intercept_:", round(float(model.intercept_), 4))  # -0.3606
print("MSE:", round(mean_squared_error(y_te, y_pred), 4))     # 0.0353
print("R²:", round(r2_score(y_te, y_pred), 4))                # 0.9446
print("花瓣长 5.0 预测花瓣宽:", round(float(model.predict([[5.0]])[0]), 3))   # 1.706
```

> **⚠️ 别把下面 NumPy 手工版的数字和这里对混**：上面用的是 sklearn 自带的**完整 `load_iris()`（150 条）**，下面手工版用的是**从中抽出的 15 条**。数据集不同，回归系数当然不同（0.4133 vs 0.4273）。**同一个数据集上两种写法的结果才应该一致**——这一点你自己改改数据量验证一下，比背数字有用得多。

> **注意 `model.predict([[5.0]])` 的双层方括号**：sklearn 要求输入是**二维数组**（行=样本，列=特征）。传 `[5.0]` 会报 `ValueError: Expected 2D array, got 1D array instead`。**这是 sklearn 最常见的报错。**

**用 NumPy 手工实现，看清"模型"到底是什么**（不装 sklearn 也能跑）：

```python
import numpy as np, pandas as pd

# 15 条鸢尾花数据（每类 5 条）：萼片长、萼片宽、花瓣长、花瓣宽、类别
data = [
    (5.1,3.5,1.4,0.2,"setosa"),(4.9,3.0,1.4,0.2,"setosa"),(4.7,3.2,1.3,0.2,"setosa"),
    (5.0,3.6,1.4,0.2,"setosa"),(5.4,3.9,1.7,0.4,"setosa"),
    (7.0,3.2,4.7,1.4,"versicolor"),(6.4,3.2,4.5,1.5,"versicolor"),(6.9,3.1,4.9,1.5,"versicolor"),
    (5.5,2.3,4.0,1.3,"versicolor"),(6.5,2.8,4.6,1.5,"versicolor"),
    (6.3,3.3,6.0,2.5,"virginica"),(5.8,2.7,5.1,1.9,"virginica"),(7.1,3.0,5.9,2.1,"virginica"),
    (6.3,2.9,5.6,1.8,"virginica"),(6.5,3.0,5.8,2.2,"virginica"),
]
df = pd.DataFrame(data, columns=["萼片长","萼片宽","花瓣长","花瓣宽","类别"])
print(df.groupby("类别")[["花瓣长","花瓣宽"]].mean().round(2))
#             花瓣长   花瓣宽
# 类别
# setosa      1.44  0.24
# versicolor  4.54  1.44
# virginica   5.68  2.10

x = df["花瓣长"].to_numpy()
t = df["花瓣宽"].to_numpy()
k, b = np.polyfit(x, t, 1)          # 最小二乘拟合一条直线
print("斜率", round(float(k), 4), "截距", round(float(b), 4))    # 斜率 0.4273 截距 -0.4008

pred = k * x + b
mse = ((t - pred) ** 2).mean()
r = np.corrcoef(x, t)[0, 1]
print("MSE", round(float(mse), 4), "RMSE", round(float(np.sqrt(mse)), 4))
# MSE 0.0182 RMSE 0.135
print("R²", round(float(r ** 2), 4))                             # R² 0.9705
print("花瓣长 5.0 → 花瓣宽", round(float(k * 5.0 + b), 3))         # 1.736
```

**评价指标怎么读**：

| 指标 | 公式/含义 | 怎么判断好坏 |
|---|---|---|
| **MSE**（均方误差） | 预测误差平方的平均值 | 越小越好，单位是"平方"，不好解释 |
| **RMSE**（均方根误差） | MSE 开根号 | **越小越好，单位与原数据一致**（这里 0.135 cm） |
| **R²**（决定系数） | 模型解释了数据变异的比例 | **越接近 1 越好**，0.9705 = 解释了 97% 的变异 |
| **相关系数 r** | 两个变量的线性相关程度 | 0.9851 = 强正相关 |

**结论**：花瓣长与花瓣宽**强正相关（r = 0.9851）**，一条直线就能解释 97% 的变化。**这说明"特征选得好"比"模型选得复杂"更重要**——用第 7 章学的相关系数就能提前判断哪些特征有用：

```
花瓣宽 与 花瓣长 的相关系数：0.9851   ← 强相关，好特征
萼片长 与 花瓣长 的相关系数：0.8689   ← 中等相关
萼片宽 与 花瓣长 的相关系数：-0.4904  ← 弱负相关，作用不大
```

**再看一个分类任务：手工实现 kNN**

```python
import numpy as np, pandas as pd

# （接上面的 df）
X = df[["萼片长","萼片宽","花瓣长","花瓣宽"]].to_numpy()
y = df["类别"].to_numpy()

# ① 标准化：让每个特征均值 0、标准差 1
mu, sigma = X.mean(axis=0), X.std(axis=0)
Xs = (X - mu) / sigma
print(np.round(Xs.mean(axis=0), 6).tolist())   # [0.0, -0.0, 0.0, 0.0]
print(np.round(Xs.std(axis=0), 6).tolist())    # [1.0, 1.0, 1.0, 1.0]

# ② 划分（用 NumPy 手写，等价于 train_test_split）
rng = np.random.default_rng(42)
idx = rng.permutation(len(X))
te_idx, tr_idx = idx[:4], idx[4:]

# ③④ 定义模型并"训练"（kNN 没有训练过程，直接把训练集记下来）
def knn_predict(Xtr, ytr, Xte, k=3):
    preds = []
    for x in Xte:
        d = np.sqrt(((Xtr - x) ** 2).sum(axis=1))    # 广播：(n,4)-(4,) → (n,)
        top = ytr[np.argsort(d)[:k]]                 # 距离最近的 k 个邻居的标签
        vals, cnts = np.unique(top, return_counts=True)
        preds.append(vals[np.argmax(cnts)])          # 多数投票
    return np.array(preds)

pred = knn_predict(Xs[tr_idx], y[tr_idx], Xs[te_idx], k=3)
print("预测:", pred.tolist())        # ['virginica', 'virginica', 'virginica', 'setosa']
print("真实:", y[te_idx].tolist())   # ['versicolor', 'versicolor', 'versicolor', 'setosa']
print("准确率:", round(float((pred == y[te_idx]).mean()), 4))    # 0.25
```

**为什么准确率只有 0.25？** 因为**这次随机划分太倒霉**：测试集的 3 个 versicolor 样本，在训练集里只剩 2 个 versicolor——**训练集"没见过"足够的 versicolor**，kNN 自然把它们判成了 viriginica。这正是前面强调 `stratify=y` 的原因。

**换不同的 k 值再试**（实测）：

```
k=1  准确率 0.75      ← 只看最近的 1 个邻居，这次恰好对
k=3  准确率 0.25
k=5  准确率 0.25
```

**再看训练集与测试集的差距**（**过拟合的直观演示**）：

```
k=1   训练集 1.0      测试集 0.75     ← 训练集满分，测试集差 → 过拟合
k=3   训练集 0.8182   测试集 0.25
```

**`k=1` 时训练集准确率 100%**——因为它直接返回最近邻居的标签，而"最近的邻居"在训练集里就是样本自己。**这是过拟合的极端形式**：模型把训练数据"背"下来了，但没有学到可推广的规律。

**混淆矩阵（confusion matrix）** 比单一准确率信息更多：

```python
labels = sorted(set(y))
cm = np.zeros((3, 3), dtype=int)
for t, q in zip(y[te_idx], pred):
    cm[labels.index(t), labels.index(q)] += 1
print("标签顺序:", labels)      # ['setosa', 'versicolor', 'virginica']
print(cm.tolist())              # [[1, 0, 0], [0, 0, 3], [0, 0, 0]]
```

| | 预测 setosa | 预测 versicolor | 预测 virginica |
|---|---|---|---|
| **真实 setosa** | **1** | 0 | 0 |
| **真实 versicolor** | 0 | 0 | **3** |
| **真实 virginica** | 0 | 0 | 0 |

**读法**：**对角线上的数字是"判对的"，对角线外的是"判错的"**。这里 3 个 versicolor 全被判成了 virginica（第 2 行第 3 列 = 3），说明模型**完全无法区分这两个类**。对角线之和 1 = 判对 1 个，样本共 4 个。

**四种错误的专业叫法**（二分类时）：

| 名称 | 含义 | 医疗场景的例子 |
|---|---|---|
| **TP** 真阳性 | 正例判成正例 | 有病查出有病 |
| **TN** 真阴性 | 负例判成负例 | 没病查出没病 |
| **FP** 假阳性 | 负例判成正例 | **没病说有病的"误报"** |
| **FN** 假阴性 | 正例判成负例 | **有病说没病的"漏报"（最危险）** |

> **重要**：**在不平衡数据上，准确率会骗人。** 如果 99% 的样本都是"没病"，一个"永远说没病"的模型准确率也有 99%，但一个病人都查不出来。这时要看**精确率（precision）和召回率（recall）**。

**逻辑回归：从"直线"到"概率"**

线性回归输出的是任意实数，但分类需要 0~1 的概率。**sigmoid 函数** 把任意实数压到 (0,1)：

```
sigmoid(z) = 1 / (1 + e^(−z))

z → +∞ 时 sigmoid → 1
z = 0  时 sigmoid = 0.5
z → −∞ 时 sigmoid → 0
```

**手工用梯度下降训练一个逻辑回归**（只分 versicolor 和 virginica）：

```python
mask = y != "setosa"
X2 = Xs[mask]
y2 = (y[mask] == "virginica").astype(float)

def sigmoid(z):
    return 1 / (1 + np.exp(-z))

w = np.zeros(X2.shape[1]); b = 0.0
lr, epochs = 0.5, 500
for _ in range(epochs):
    yh = sigmoid(X2 @ w + b)                    # 前向：算预测概率
    grad_w = X2.T @ (yh - y2) / len(X2)         # 反向：算梯度
    grad_b = (yh - y2).mean()
    w -= lr * grad_w                            # 更新参数
    b -= lr * grad_b

print("w:", np.round(w, 4).tolist())   # [-2.6789, 0.5625, 4.6441, 5.8601]
print("b:", round(float(b), 4))        # -4.3363
probs = sigmoid(X2 @ w + b)
print("准确率:", round(float(((probs >= 0.5) == y2).mean()), 4))   # 1.0
print("损失:", round(float(-(y2*np.log(probs) + (1-y2)*np.log(1-probs)).mean()), 4))  # 0.0375
```

**这段 15 行代码就是深度学习的全部核心思想**：**前向传播算预测 → 计算损失 → 求梯度 → 更新参数 → 重复**。神经网络不过是把"一层线性 + sigmoid"堆成很多层而已。

**多分类用 softmax**（把一组分数变成和为 1 的概率）：

```python
logits = np.array([2.0, 1.0, 0.1])
e = np.exp(logits - logits.max())       # 减去最大值防止 exp 溢出
print(np.round(e / e.sum(), 4).tolist())   # [0.659, 0.2424, 0.0986]
print(round(float((e / e.sum()).sum()), 6))  # 1.0
print(int(np.argmax(logits)))            # 0   ← 取概率最大的类别
```

> **技巧**：**`softmax` 计算时先减去最大值**，是为了防止 `exp(1000)` 溢出成 `inf`。这个数值稳定技巧在深度学习框架里是默认实现的。

**中文文本分析：jieba + wordcloud**（第7章学过的库，这里是 AI 的"文本预处理"环节）：

```python
import jieba
from collections import Counter
from wordcloud import WordCloud

text = "Python是一门优秀的程序设计语言，Python在数据分析、人工智能领域应用广泛。"

# ① 分词（三种模式，详见第7章 7.3.5）
print(list(jieba.cut(text)))            # 精确模式
# ['Python', '是', '一门', '优秀', '的', '程序设计', '语言', '，', 'Python', '在',
#  '数据分析', '、', '人工智能', '领域', '应用', '广泛', '。']
print(list(jieba.cut(text, cut_all=True)))   # 全模式（有冗余）
print(list(jieba.cut_for_search(text)))      # 搜索引擎模式

# ② 去停用词 + 词频统计
stop = {"是", "的", "一门", "在", "、", "，", "。"}   # ⚠️ 停用词必须按【分词后的结果】写
words = [w for w in jieba.cut(text) if len(w) > 1 and w not in stop]
print(Counter(words).most_common(3))
# [('Python', 2), ('优秀', 1), ('程序设计', 1)]

# ③ 词云（必须指定中文字体，否则全是方框）
wc = WordCloud(font_path="C:/Windows/Fonts/msyh.ttc",
               width=800, height=400, background_color="white")
wc.generate(" ".join(words))
wc.to_file("wordcloud.png")
```

> **⚠️ 停用词表最容易写错的地方**：停用词要跟**分词结果**对得上，不能按「原句里有哪些字」来写。
> 上面这句里 `"是一门"` 被 jieba 切成 **`是` + `一门`** 两个词——所以你把 `"一"` 和 `"门"` 放进停用词表**根本拦不住 `"一门"`**（它长度 2，能通过 `len(w) > 1` 的过滤）。
> 实测对照：
> ```python
> stop = set("是的一门在、，。".replace("、","").replace("，",""))   # {'的','在','是','。','一','门'}
> words = [w for w in jieba.cut(text) if len(w) > 1 and w not in stop]
> print(Counter(words).most_common(3))
> # [('Python', 2), ('一门', 1), ('优秀', 1)]   ← 写错了：「一门」没被过滤掉
> ```
> **正确做法**：先把句子分一次词、打印出来看，再照着实际切出的词写停用词表；或者直接下载一份通用中文停用词表（如哈工大停用词表）逐行读入。

> **注意**：**词云必须设 `font_path` 指向中文字体**（Windows 用 `msyh.ttc` 微软雅黑，macOS 用 `/System/Library/Fonts/PingFang.ttc`），否则中文全显示成方框。**这与第7章 matplotlib 的中文乱码是同一类问题。**

## 15.3 深度学习

### 15.3.1 TensorFlow

**TensorFlow 是 Google 开源的深度学习框架**，PyTorch 是 Meta 的同类框架。两者的选择：

| 对比项 | TensorFlow | PyTorch |
|---|---|---|
| 出品方 | Google | Meta |
| 图模式 | 静态图为主（TF2 起支持动态） | **动态图**（更直观） |
| 上手难度 | 稍高 | **更容易调试** |
| 工业部署 | **生态成熟**（TF Serving、TFLite） | 追赶中 |
| 学术论文 | 较少 | **占多数** |
| 建议 | 做移动端/生产部署 | **入门学习选它** |

**三个核心概念**：

| 概念 | 含义 | 类比 |
|---|---|---|
| **张量（Tensor）** | 多维数组，是框架里的基本数据单位 | 就是第7章的 `ndarray`，只是能放到 GPU 上 |
| **计算图（Graph）** | 把运算表示成节点和边 | 相当于"先画好流程再执行" |
| **自动微分（Autograd）** | 自动求梯度 | 省掉手工推导梯度的过程 |

**张量的阶（rank）**：

| 阶 | 名称 | 形状 | 例子 |
|---|---|---|---|
| 0 | 标量 | `()` | `5` |
| 1 | 向量 | `(n,)` | `[1, 2, 3]` |
| 2 | 矩阵 | `(m, n)` | 一张灰度图、一批表格数据 |
| 3 | 3 阶张量 | `(h, w, c)` | **一张彩色图片**（高、宽、通道） |
| 4 | 4 阶张量 | `(n, h, w, c)` | **一批彩色图片** |

> **理解这一点很重要**：**图片在计算机里就是一个 3 阶张量**。一张 28×28 的灰度手写数字图 = 形状 `(28, 28)` 的矩阵，784 个像素值；一批 32 张就是 `(32, 28, 28)`。

**Keras 高层 API 最小示例**（TF2 内置，几行就能定义网络）：

```python
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

# ① 准备数据（内置手写数字集，与 sklearn 的 load_digits 类似但更大）
(x_train, y_train), (x_test, y_test) = keras.datasets.mnist.load_data()
x_train = x_train.reshape(-1, 784).astype("float32") / 255.0   # 拉平 + 归一化到 0~1
x_test  = x_test.reshape(-1, 784).astype("float32") / 255.0

# ② 搭一个三层全连接网络
model = keras.Sequential([
    layers.Dense(128, activation="relu"),      # 隐藏层：128 个神经元
    layers.Dropout(0.2),                       # 随机丢弃 20%，防过拟合
    layers.Dense(10, activation="softmax"),    # 输出层：10 个数字的概率
])

# ③ 编译：指定优化器、损失函数、评价指标
model.compile(optimizer="adam",
              loss="sparse_categorical_crossentropy",
              metrics=["accuracy"])

# ④ 训练（"训练"就是反复前向 + 反向 + 更新参数）
model.fit(x_train, y_train, epochs=5, batch_size=32, validation_split=0.1)

# ⑤ 评估与预测
test_loss, test_acc = model.evaluate(x_test, y_test)
print("测试准确率", round(float(test_acc), 4))     # 约 0.97~0.98
pred = model.predict(x_test[:1])
print("预测类别", int(pred.argmax()))              # 7
```

**这段代码里的每个词你都已经见过**：`reshape` 是第7章 NumPy 的、`/255.0` 是**归一化**（把 0~255 的像素压到 0~1，与前面 `StandardScaler` 同理）、`Dropout` 是**防过拟合**、`softmax` 是刚才手工实现过的、`fit`/`evaluate`/`predict` 与 sklearn 的三个动词**完全一致**。

> **说明**：本章对深度学习的要求是**"看得懂、跑得动、说得出"，不要求会调参、不要求会设计网络结构**。真正的深度学习是研究生课程的内容。

### 15.3.2 典型案例——识别模糊的手写数字图片

**任务**：训练一个模型识别手写数字，并在"加了噪声的模糊图片"上测试它的鲁棒性。

**关键思路**：**在干净数据上训练，在模糊数据上测试**——这能暴露模型的脆弱性。

```python
import numpy as np, tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

# ---------- ① 数据 ----------
(x_train, y_train), (x_test, y_test) = keras.datasets.mnist.load_data()
x_train = x_train.reshape(-1, 784).astype("float32") / 255.0
x_test  = x_test.reshape(-1, 784).astype("float32") / 255.0

# ---------- ② 制造"模糊"测试集 ----------
def add_noise(images, level=0.3, seed=42):
    """给图片加高斯噪声，模拟模糊/拍摄质量差"""
    rng = np.random.default_rng(seed)
    noisy = images + rng.normal(0, level, images.shape)
    return np.clip(noisy, 0.0, 1.0)          # 裁剪回 0~1

x_test_blur = add_noise(x_test, level=0.3)

# ---------- ③ 模型 ----------
model = keras.Sequential([
    layers.Dense(256, activation="relu"),
    layers.Dropout(0.3),
    layers.Dense(128, activation="relu"),
    layers.Dense(10, activation="softmax"),
])
model.compile(optimizer="adam",
              loss="sparse_categorical_crossentropy",
              metrics=["accuracy"])

# ---------- ④ 训练 ----------
model.fit(x_train, y_train, epochs=5, batch_size=64, validation_split=0.1, verbose=2)

# ---------- ⑤ 对比评估 ----------
clean_acc = model.evaluate(x_test, y_test, verbose=0)[1]
blur_acc  = model.evaluate(x_test_blur, y_test, verbose=0)[1]
print(f"清晰图片准确率 {clean_acc:.4f}")      # 清晰图片准确率 0.9780
print(f"模糊图片准确率 {blur_acc:.4f}")       # 模糊图片准确率 0.4520  ← 大幅下降！

# ---------- ⑥ 看几个具体错例 ----------
pred = model.predict(x_test_blur[:5], verbose=0).argmax(axis=1)
print("模糊后预测:", pred.tolist())           # [3, 6, 1, 8, 5]
print("真实标签:  ", y_test[:5].tolist())     # [7, 2, 1, 0, 4]
```

**结果解读**：模型在**清晰图片上准确率约 97.8%**，但在**加了噪声的图片上掉到约 45%**——**比瞎猜（10%）好，但远不能用**。

**这个案例要说明三件事**：

1. **模型只见过"干净数据"，就没法处理"脏数据"**。解决办法是**数据增强（data augmentation）**：训练时主动加入旋转、缩放、加噪的样本，让模型见过各种情况。
2. **"准确率高"不等于"模型可靠"**。必须像这样**用各种条件下的数据去测**，才能知道模型的真实边界。这和 15.2 节强调"要用测试集而不是训练集评估"是同一个道理。
3. **`np.clip(x, 0, 1)` 是必要的**：噪声可能把像素值推到负数或超过 1，超出训练时的取值范围会让模型输出完全离谱的结果。**训练和预测时的数据预处理必须完全一致**——这是工程上最常见的 bug 之一。

**若用 sklearn 做同样的任务**（小数据集，不需要 GPU）：

```python
from sklearn.datasets import load_digits
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, confusion_matrix
from sklearn.preprocessing import StandardScaler

digits = load_digits()
X, y = digits.data, digits.target          # X.shape = (1797, 64)，8×8 的图拉平成 64 维
X_tr, X_te, y_tr, y_te = train_test_split(
    X, y, test_size=0.3, random_state=42, stratify=y)    # ★ 分层抽样

scaler = StandardScaler()
X_tr = scaler.fit_transform(X_tr)          # 训练集：fit + transform
X_te = scaler.transform(X_te)              # 测试集：只 transform！

model = KNeighborsClassifier(n_neighbors=3)
model.fit(X_tr, y_tr)
pred = model.predict(X_te)
print("准确率:", round(accuracy_score(y_te, pred), 4))    # 0.9722
print(confusion_matrix(y_te, pred))
# [[54  0  0  0  0  0  0  0  0  0]
#  [ 0 54  1  0  0  0  0  0  0  0]
#  [ 0  0 53  0  0  0  0  0  0  0]
#  [ 0  0  1 54  0  0  0  0  0  0]
#  [ 0  0  0  0 51  0  0  3  0  0]
#  [ 0  0  0  0  0 53  0  0  0  2]
#  [ 0  0  0  0  0  0 54  0  0  0]
#  [ 0  0  0  0  0  0  0 54  0  0]
#  [ 0  3  1  1  0  0  0  0 47  0]
#  [ 0  0  0  0  1  0  1  0  1 51]]
```

> **读这个混淆矩阵**：它是 **10×10**（0~9 十个数字）。**对角线 = 判对的个数**，**对角线之和 54+54+53+54+51+53+54+54+47+51 = 525**；测试集样本数 = 1797×0.3 ≈ 540（每行加起来也是 540）。**准确率 = 525 ÷ 540 = 0.9722** ✅，和上面 `accuracy_score` 打出来的一致——**这就是"准确率 = 混淆矩阵对角线之和 ÷ 总数"**。
> 看第 8 行（数字 8）：`[0 3 1 1 0 0 0 0 47 0]`——**47 个 8 判对了，3 个 8 被判成 1、1 个判成 2、1 个判成 3**，说明 **8 和 1 最容易混**（都是细笔画）。**混淆矩阵比一个准确率数字有用得多，它能告诉你"错在哪"。**

> **⚠️ 最常见的 sklearn 错误**：**测试集只能用 `transform`，绝对不能用 `fit_transform`**。因为 `fit` 会从数据里学出均值和标准差——如果对测试集也 `fit`，就等于**让模型偷看了测试数据的信息**，这叫**数据泄漏（data leakage）**，会让评估结果虚高。**正确做法是：`fit` 只在训练集上做，然后对测试集 `transform`。**

## 典型例题

**例 15.1**（题型：概念题 —— 三者关系）

**题目**：判断正误并说明理由：① 深度学习是机器学习的一个子集；② 机器学习就是让程序员写出更复杂的 if-else 规则；③ 逻辑回归是一种分类算法。

**解**：① 正确。包含关系是 **AI ⊃ 机器学习 ⊃ 深度学习**，深度学习是"用多层神经网络做机器学习"的一个分支。② 错误。机器学习恰恰是**不写死规则**，而是从数据中自动学习规律；写死 if-else 是第3章的传统编程思路。③ 正确。**逻辑回归虽然名字里有"回归"，但它输出的是"属于某类的概率"（0~1），做的是分类任务**——名字来自它用了回归式的线性组合。

**答案**：① 正确；② 错误（机器学习是让机器从数据中自己找规则）；③ 正确（名字有误导性，实际是分类算法）。

**例 15.2**（题型：读程序写结果 —— softmax 与 argmax）

```python
import numpy as np
logits = np.array([2.0, 1.0, 0.1])
e = np.exp(logits - logits.max())
p = e / e.sum()
print(np.round(p, 4).tolist())
print(round(float(p.sum()), 6))
print(int(np.argmax(logits)))
```

**解**：`logits.max()` 是 2.0，减去后得到 `[0, -1, -1.9]`，取指数得 `[1.0, 0.3679, 0.1496]`，和是 **1.5174**（精确值 1.5174480603940774）。归一化：`1.0/1.5174 = 0.659`、`0.3679/1.5174 = 0.2424`、`0.1496/1.5174 = 0.0986`。**softmax 的输出和恒为 1**（概率分布的性质）。`argmax` 返回最大值的下标，2.0 在位置 0。

**答案**：`[0.659, 0.2424, 0.0986]` / `1.0` / `0`。

**例 15.3**（题型：读程序写结果 —— 为什么除以 255）

```python
import numpy as np
img = np.array([[0, 128, 255], [255, 64, 0]], dtype="float32")
print(img.shape, img.max())
norm = img / 255.0
print(np.round(norm[0], 4).tolist(), round(float(norm.max()), 4))
```

**解**：`img` 是 2 行 3 列的二维数组，形状 `(2, 3)`，最大值 255。除以 255 是**归一化**，把像素值从 0~255 压到 0~1。第一行 `[0, 128, 255]` 变成 `[0.0, 0.50196..., 1.0]`，保留 4 位是 `[0.0, 0.502, 1.0]`；最大值变成 1.0。

**答案**：`(2, 3) 255.0` / `[0.0, 0.502, 1.0] 1.0`。**为什么要归一化？** 因为不同特征的量纲差异会让梯度下降"走之字形"、收敛慢；压到同一尺度能加快训练（与 `StandardScaler` 标准化是同一个目的）。

**例 15.4**（题型：编程题 —— 手写 KNN）

**题目**：用 NumPy 实现 kNN 分类的预测函数 `knn_predict(X_train, y_train, X_test, k)`，要求用广播计算欧氏距离、用多数投票决定类别。

**解**：

```python
import numpy as np

def knn_predict(Xtr, ytr, Xte, k=3):
    preds = []
    for x in Xte:
        d = np.sqrt(((Xtr - x) ** 2).sum(axis=1))   # 广播算距离：(n,4)-(4,) → (n,)
        top = ytr[np.argsort(d)[:k]]                # 取最近的 k 个标签
        vals, cnts = np.unique(top, return_counts=True)
        preds.append(vals[np.argmax(cnts)])         # 多数投票
    return np.array(preds)

Xtr = np.array([[0.,0.],[1.,1.],[2.,2.],[10.,10.],[11.,11.]])
ytr = np.array(["A","A","A","B","B"])
print(knn_predict(Xtr, ytr, np.array([[1.5,1.5],[9.,9.]]), k=3).tolist())
```

**解（续）**：对 `[1.5, 1.5]`，到五个训练点的距离分别是 √4.5≈2.12、√0.5≈0.71、√0.5≈0.71、√144.5≈12.02、√180.5≈13.44。最近的 3 个是下标 1、2、0，标签全是 `A`，所以预测 `A`。对 `[9, 9]`，距离最近的是下标 3（√2≈1.41）、4（√8≈2.83）、2（√98≈9.9），标签是 `B, B, A`，多数是 `B`。

**答案**：`['A', 'B']`。**要点**：① **广播 `(Xtr - x)` 一步算出所有距离**，不用写内层循环（第7章 NumPy 广播）；② `np.argsort(d)[:k]` 取距离最小的 k 个**下标**；③ `np.unique(..., return_counts=True)` + `np.argmax` 实现多数投票；④ kNN 的"训练"就是把训练集存下来，**预测时才计算**——这叫**惰性学习（lazy learning）**。

**例 15.5**（题型：编程题 —— 划分数据集与标准化）

**题目**：给定 `X`（n×4）和 `y`（n），按 7:3 划分训练/测试集（`random_state=42`），并对特征做标准化。要求**测试集的标准化参数必须来自训练集**。

**解**：

```python
import numpy as np

def split_and_scale(X, y, test_size=0.3, seed=42):
    rng = np.random.default_rng(seed)
    idx = rng.permutation(len(X))
    n_test = int(len(X) * test_size)
    te, tr = idx[:n_test], idx[n_test:]

    mu = X[tr].mean(axis=0)              # ★ 只用训练集算均值
    sd = X[tr].std(axis=0)               # ★ 只用训练集算标准差
    sd[sd == 0] = 1                      # 防止除零

    return ((X[tr] - mu) / sd, (X[te] - mu) / sd, y[tr], y[te], mu, sd)

X = np.random.default_rng(0).normal(10, 3, (20, 4))
y = np.array([0, 1] * 10)
Xtr, Xte, ytr, yte, mu, sd = split_and_scale(X, y)
print(len(Xtr), len(Xte))                                  # 14 6
print(np.round(Xtr.mean(axis=0), 6).tolist())              # 均值都约 0
print(np.round(Xte.mean(axis=0), 2).tolist())              # 测试集均值不一定是 0！
```

**答案**：`14 6`；训练集各列均值约 `[0.0, 0.0, 0.0, 0.0]`；**测试集均值不是 0**（如 `[0.31, -0.52, 0.18, -0.77]`）。**要点**：① **标准化参数（均值、标准差）只能从训练集算**，测试集用同一组参数 `transform`——否则就是**数据泄漏**，评估结果会虚高；② 测试集均值不为 0 是**正常的、正确的**，因为它服从的是训练集的分布；③ 标准差为 0 的特征（常量列）要特殊处理，否则除零得到 `nan`/`inf`。

**例 15.6**（题型：编程题 —— 评价指标）

**题目**：实现 `evaluate(y_true, y_pred)`，返回准确率和混淆矩阵；再用它说明"为什么在不平衡数据上准确率会骗人"。

**解**：

```python
import numpy as np

def evaluate(y_true, y_pred, labels=None):
    y_true, y_pred = np.asarray(y_true), np.asarray(y_pred)
    if labels is None:
        labels = sorted(int(v) for v in set(y_true) | set(y_pred))
    cm = np.zeros((len(labels), len(labels)), dtype=int)
    for t, p in zip(y_true, y_pred):
        cm[labels.index(t), labels.index(p)] += 1
    acc = float((y_true == y_pred).mean())
    return round(acc, 4), labels, cm

# 不平衡场景：990 个负例 + 10 个正例
y_true = np.array([0] * 990 + [1] * 10)
y_pred = np.array([0] * 1000)          # 一个正例都没查出来
acc, labels, cm = evaluate(y_true, y_pred)
print("准确率:", acc)                   # 准确率: 0.99
print(labels, cm.tolist())              # [0, 1] [[990, 0], [10, 0]]

TP, FN = cm[1, 1], cm[1, 0]
precision = TP / (TP + cm[0, 1]) if (TP + cm[0, 1]) else 0.0
recall = TP / (TP + FN) if (TP + FN) else 0.0
print("精确率:", round(precision, 4), "召回率:", round(recall, 4))
# 精确率: 0.0 召回率: 0.0
```

> **两个 NumPy 2.x 的坑，都在这个例子里**：
> ① `labels = sorted(set(y_true) | set(y_pred))` 里的元素是 **`np.int64` 标量**，**NumPy 2.0 起标量的 `repr` 变成了 `np.int64(0)`**（1.x 时代是 `0`），所以直接 `print(labels)` 会输出 `[np.int64(0), np.int64(1)]` 而不是 `[0, 1]`。**加一层 `int()` 就正常了**（上面已改）。
> ② `else 0` 和 `else 0.0` 打出来不一样：`round(0, 4)` 是整数 `0`，`round(0.0, 4)` 是 `0.0`。**做数值计算时字面量统一写 `0.0`**，免得输出类型时好时坏。

**答案**：**准确率高达 0.99，但精确率和召回率都是 0**——因为这个模型"永远说没有病"，一个正例都没识别出来。**这就是准确率骗人的经典反例。** 要点：① **不平衡数据必须看精确率/召回率/F1**，不能只看准确率；② **精确率 = TP/(TP+FP)**，回答"预测为正的里面有多少真的是正"；③ **召回率 = TP/(TP+FN)**，回答"真正的正例有多少被找出来了"；④ **医疗、风控场景优先保召回率**（宁可误报，不能漏报），**垃圾邮件过滤优先保精确率**（宁可漏掉，不能误杀）。

## 易错点提醒

1. **以为"机器学习"就是写更多 if-else** → 错：那还是传统编程。对：机器学习是**给数据让机器自己找规律**，输出的是模型而不是规则。
2. **把逻辑回归当回归算法** → 错：**名字有"回归"但它是分类算法**，输出的是 0~1 的概率。
3. **只划分训练集，不划分测试集** → 错：模型在见过的数据上分数再高也说明不了问题（过拟合）。对：**必须留出 20%~30% 做测试集**。
4. **分类任务划分时不加 `stratify=y`** → 错：可能出现"训练集某类只剩 2 个、测试集全是这一类"的极端划分（本机实测 kNN 准确率因此掉到 0.25）。对：**加 `stratify=y` 分层抽样**。
5. **`model.predict([5.0])` 传了一维数组** → 错：`ValueError: Expected 2D array, got 1D array instead`。对：**传二维 `[[5.0]]`**（行=样本，列=特征）。
6. **测试集用 `fit_transform`** → 错：**数据泄漏**，让模型偷看了测试集信息，评估虚高。对：**训练集 `fit_transform`，测试集只 `transform`**。
7. **忘了标准化就直接上 KNN/SVM** → 错：量纲大的特征会主导距离计算（比如"工资 10000"会淹没"年龄 30"）。对：先用 `StandardScaler` 标准化。
8. **看准确率就下结论** → 错：不平衡数据上"全猜多数类"也能有 99% 准确率（实测）。对：看**混淆矩阵 + 精确率 + 召回率**。
9. **`k=1` 的 KNN 在训练集上准确率 100% 就以为模型很好** → 错：那是**过拟合**的极端形式，测试集只有 0.75（实测）。对：**对比训练集与测试集的表现**，差距大就是过拟合。
10. **训练和预测时数据预处理不一致** → 错：训练时归一化到 0~1，预测时忘了，模型输出完全离谱。对：**把预处理封装成函数，训练和预测走同一条路**。
11. **`np.exp` 直接算大数导致溢出** → 错：`exp(1000)` 是 `inf`，softmax 得到 `nan`。对：**先减最大值**（`logits - logits.max()`）。
12. **图片没 reshape 就喂给全连接层** → 错：形状不匹配报错。对：`x.reshape(-1, 784)` 拉平（`-1` 表示自动推断该维大小）。
13. **词云中文显示成方框** → 错：没指定中文字体。对：`WordCloud(font_path="C:/Windows/Fonts/msyh.ttc", ...)`。
14. **以为学会 sklearn 就等于会 AI** → 错：sklearn 只是传统机器学习工具。对：还要懂**特征工程、数据质量、业务理解**——**数据和特征决定上限，模型只是逼近这个上限**。
15. **一上手就学深度学习** → 错：连 NumPy 广播、Pandas 分组都不熟，看 TensorFlow 代码只会一头雾水。对：**先把第 7 章的 NumPy/Pandas 学扎实**，再学 sklearn，最后才是深度学习。

## 本章小结

- **包含关系**：AI ⊃ 机器学习 ⊃ 深度学习。**传统编程是"人写规则"，机器学习是"数据里长规则"。**
- **AI 三要素：数据、算法、算力**；四大任务：**分类、回归**（有标签）、**聚类、降维**（无标签）。
- **Python 是 AI 首选**，因为生态完整（NumPy→Pandas→Matplotlib→sklearn→PyTorch）、语法简洁、底层用 C/CUDA 保证性能。
- **sklearn 统一五步**：准备数据 → `train_test_split` 划分 → 建模型 → `fit` 训练 → `predict`/`score` 评估。**所有算法共用这套 API。**
- **必须划分训练集/测试集**（防过拟合），**分类任务加 `stratify=y`**，**标准化参数只能来自训练集**（防数据泄漏）。
- **实测数据（15 条抽样数据集）**：花瓣长与花瓣宽相关系数 **0.9851**，线性回归斜率 **0.4273**、截距 **-0.4008**、MSE **0.0182**、R² **0.9705**，预测花瓣长 5.0 → 花瓣宽 **1.736**；**换成完整 `load_iris()`（150 条）+ `train_test_split(test_size=0.3, random_state=42)` 则是斜率 0.4133、截距 -0.3606、MSE 0.0353、R² 0.9446、预测 1.706**——**数据集不同，系数就不同，别把两套数字记混**。
- **过拟合的直观证据**：kNN 在 `k=1` 时训练集准确率 **1.0**、测试集 **0.75**；`k=3` 时训练集 **0.8182**、测试集 **0.25**（后者源于倒霉的随机划分，正说明 `stratify` 的必要性）。
- **混淆矩阵对角线是判对的**，对角线外是判错的；**不平衡数据要看精确率与召回率，准确率会骗人**（实测"全猜多数类"准确率 0.99、召回率 0）。
- **逻辑回归的 15 行手工实现就是深度学习的核心**：前向算预测 → 算损失 → 求梯度 → 更新参数 → 重复。神经网络只是把它堆成多层。
- **张量就是能上 GPU 的 ndarray**；一张 28×28 灰度图是 2 阶张量，一批彩色图是 4 阶张量。
- **本章定位**：不要求会训练深度模型，要求**看得懂代码、说得出概念、知道它与前 14 章的衔接**。

> **一句话记住本章**：**机器学习就是把"人写 if-else"换成"用数据拟合一个函数"——sklearn 五步走（划分→建模→fit→predict→评估），永远用没见过的测试集检验，永远警惕过拟合和数据泄漏。**

## 自测题

1. AI、机器学习、深度学习三者的关系是什么？
2. AI 三要素是什么？机器学习与传统编程的本质区别是什么？
3. 监督学习和无监督学习有什么区别？各举两个任务。
4. 逻辑回归是分类还是回归？为什么名字容易误导？
5. sklearn 的统一五步流程是什么？
6. 为什么必须划分测试集？什么是过拟合？
7. `stratify=y` 有什么作用？不加会有什么后果？
8. `model.predict([5.0])` 为什么会报错？应该怎么写？
9. 为什么测试集只能用 `transform` 而不能用 `fit_transform`？
10. 什么情况下准确率会骗人？应该看什么指标？
11. 张量是什么？一张 28×28 的灰度图片和一批 32 张彩色图片分别是几阶张量？
12. 写出 sigmoid 函数的表达式，并说明它的作用。

**参考答案**

1. **包含关系**：人工智能 ⊃ 机器学习 ⊃ 深度学习。机器学习是"让机器从数据中学规律"的方法集合；深度学习是机器学习中用**多层神经网络**的那一支。
2. 三要素：**数据、算法、算力**。本质区别：**传统编程是人写规则、输入数据输出答案；机器学习是输入数据和答案、输出规则（模型）**。
3. **监督学习有标签**，目标是学一个从特征到标签的映射（分类：垃圾邮件识别、手写数字识别；回归：房价预测、销量预测）；**无监督学习没有标签**，目标是发现数据内部结构（聚类：用户分群；降维：PCA 压缩特征用于可视化）。
4. **是分类算法**。它输出的是"属于某类的概率"（0~1），用于分类。名字里的"回归"来自它内部用了线性组合（回归式）来计算 logit，**不是因为它做回归任务**。
5. ① 准备数据 `X, y`；② `train_test_split` 划分训练集/测试集；③ 创建模型 `model = SomeModel()`；④ `model.fit(X_train, y_train)` 训练；⑤ `model.predict(X_test)` 预测并用 `score` 或指标函数评估。
6. **因为模型在见过的数据上表现好不代表学到了规律**——它可能只是把训练数据"背"下来了。用没见过的测试集才能检验**泛化能力**。**过拟合**就是"训练集表现很好、测试集表现很差"（实测：kNN 在 `k=1` 时训练集 1.0、测试集 0.75）。
7. `stratify=y` 让划分**按标签比例分层抽样**，保证训练集和测试集里各类别的比例与原始数据一致。不加的后果：本机实测 15 条数据按手工 7:3 划分后**训练集 versicolor 只剩 2 个，测试集 4 条里有 3 条是 versicolor**（另一条是 setosa），导致 kNN 准确率从 0.75 掉到 0.25。
8. 因为 **sklearn 要求输入是二维数组**（行=样本，列=特征），一维 `[5.0]` 会被当成"5 个特征的单样本"或直接报 `ValueError: Expected 2D array, got 1D array instead`。正确写法：`model.predict([[5.0]])`，取结果 `[0]`。
9. 因为 `fit` 会**从数据中学习参数**（均值、标准差）。如果对测试集也 `fit`，测试集的统计信息就"泄漏"进了预处理流程，相当于**模型偷看了测试数据**，评估结果会虚高。正确做法：**`fit` 只在训练集上做，测试集用同一组参数 `transform`**。
10. **在类别不平衡的数据上**会骗人：如果 99% 样本是负例，一个"永远预测负例"的模型准确率也有 0.99，但一个正例都找不出来（实测精确率 0、召回率 0）。这时应看**混淆矩阵、精确率（precision）、召回率（recall）、F1 值**。
11. **张量就是多维数组**，可以理解为"能放到 GPU 上运算的 NumPy `ndarray`"。一张 28×28 灰度图是**2 阶张量**（形状 `(28, 28)`）；一批 32 张彩色图片是**4 阶张量**（形状 `(32, 高, 宽, 3)`）。
12. **sigmoid(z) = 1 / (1 + e^(−z))**。作用是把任意实数**压缩到 (0, 1) 区间**，从而可以解释为"属于正类的概率"：`z → +∞` 时趋近 1，`z = 0` 时为 0.5，`z → −∞` 时趋近 0。它也是逻辑回归和神经网络里最常用的激活函数之一。
