---
title: AI 公司与产品手册
aliases:
  - 公司及产品
  - AI 厂商
  - 大模型公司
tags:
  - AI
  - 大模型
  - 公司
  - 产品
  - 概念解释
created: 2026-11-18
---

# AI 公司与产品手册

> **这份笔记讲什么**：国内外主要 AI 公司、它们的最新基座模型、C 端产品和 Agent 产品，以及「普通人到底有哪几种方式用上它们」。原文的名单和分类**大方向是对的**，我按 2026-11-18 的公开信息核对了一遍，改正了 8 处错误（见 [[#七、更正清单：原文哪里错了]]）。
>
> **信息核对时间**：2026-11-18。这个领域三个月换一代，本文所有「最新」都指这个日期之前已公开发布的版本。

## 30 秒速览

1. **看一家 AI 公司只看三层**：**基座模型**（能力上限）→ **C 端产品**（普通人怎么用）→ **Agent 产品**（能不能替你干活）。同一家公司的三层经常不同步，别混着比。
2. **国内格局**：大厂（字节 / 阿里 / 腾讯 / 百度 / 华为）拼生态和算力，创业公司（DeepSeek / 月之暗面 / 智谱 / MiniMax）拼模型能力和性价比。**开源模型这条线目前由中国公司主导**。
3. **海外格局**：OpenAI（GPT-6.2）、Anthropic（Claude Opus 5 / Fable 5.2）、Google（Gemini 4 Argon）三强；Meta 从闭源转回开源（Muse Spark Open）；**xAI 已于 2026 年并入 SpaceX，更名 SpaceXAI**。
4. **用 AI 只有三种方式**：网页/App（免费但有额度限制）→ API（按量计费，要写程序）→ 本地部署（不要钱但要显卡）。想一次用多家模型，走**模型聚合平台**（阿里百炼 / 火山方舟 / 百度千帆 / 腾讯云 ADP / 硅基流动）。
5. **Agent 产品的分水岭是「能不能动你的文件」**：只能聊天的叫助手，能读写本地文件、跑命令、提交代码的才叫 Agent。

> **一句话记住**：**基座决定能力上限，产品决定你能不能用上，Agent 决定它能不能替你把活干完**——评估任何一家公司，都按这三层分别看。

---

## 目录

1. [[#一、先建立三个维度：基座 / C 端产品 / Agent 产品]]
2. [[#二、国内大厂]]
3. [[#三、国内 AI 创业公司]]
4. [[#四、海外公司]]
5. [[#五、三种使用方式与模型聚合平台]]
6. [[#六、Agent 产品线全景]]
7. [[#七、更正清单：原文哪里错了]]
8. [[#八、按场景选谁：决策表]]

---

## 一、先建立三个维度：基座 / C 端产品 / Agent 产品

原文把「公司」「基座」「C 端产品」「Agent 产品」混在一段里写，读起来容易对不上号。先立三个维度，后面每个公司都按这三层填。

| 维度 | 是什么 | 怎么看 | 例子 |
| --- | --- | --- | --- |
| **基座模型** | 公司训练的大模型本体 | 参数规模、上下文长度、模态、Agent 能力、是否开源 | DeepSeek-V4-Pro、Qwen3.8-Max |
| **C 端产品** | 普通人直接用的网页 / App | 免费额度、会员价格、是否联网、是否有 Agent 模式 | 豆包、元宝、Kimi |
| **Agent 产品** | 能操作文件和工具的「干活」形态 | 能访问什么、是 CLI 还是 GUI、要不要付费 | Claude Code、TRAE、WorkBuddy |

> **最常见的误解**：「豆包用的是 Seed 2.1」——对，但**豆包 ≠ Seed 2.1**。豆包是产品，Seed 是模型；同一个模型可能被包装成好几个产品，同一个产品也可能在不同场景调不同模型。

---

## 二、国内大厂

| 公司 | C 端产品 | 最新基座（2026-11 核对） | 特点 |
| --- | --- | --- | --- |
| **字节跳动** | 豆包、豆包工作 | **Seed 2.1 Pro / Turbo**（2026-06） | 主打 Coding 与 Agent 长链路任务；模型已在豆包、TRAE、火山引擎同步上线 |
| **阿里巴巴** | 通义千问 App、千问办公（Qwen Work） | **Qwen3.8-Max**（2026-08-03） | 2.4 万亿总参数、推理激活 950 亿，100 万 token 上下文，原生多模态 |
| **腾讯** | 腾讯元宝、ima、腾讯文档 | **混元 Hy3**（preview 2026-04，开源） | 295B 总参数 / 21B 激活，256K 上下文；主打实用与 Agent 能力 |
| **百度** | 文心一言 | **文心 5.1**（2026-05-09） | 文心 5.0（2026-01）为 2.4 万亿参数全模态；5.1 把参数压到约 1/3，预训练成本仅业界约 6% |
| **华为** | 小艺（终端侧） | 盘古系列 + **昇腾算力** | 强在国产算力底座：DeepSeek-V4 发布时，昇腾超节点全系列同步支持 |
| **科大讯飞** | 讯飞星火 App、讯飞听见 | 星火系列 | 强在语音识别、语音合成、教育硬件场景 |

> **原文这里要改一处**：原文写百度「最新基座是 ERNIE-4.5」——**这是 2025 年的版本，已经落后了**。2026 年 1 月百度发布文心 5.0，5 月发布文心 5.1。

### 字节跳动的产品线（原文最需要更新的一块）

原文写「TRAE Work：面向开发者的编程 Agent」。这个描述**在今年 10 月已经变了**：

| 时间 | 变化 |
| --- | --- |
| 2025-01 | TRAE 国际版上线（AI 编程 IDE） |
| 2025-07 | 推出 SOLO 模式（根据需求理解项目、生成代码、调工具、跑结果） |
| 2026-10-09 | **TRAE Work（办公）与 TRAE Code（编程）正式合并为一个 TRAE**，同时保留 Agent 模式与 IDE 模式 |
| 现在 | TRAE 既能做文档/表格/PPT，也能管多个 Agent、写代码、调试，一个客户端走完「需求 → 开发 → 测试 → 交付」 |

> 同时字节把 TRAE、扣子（Coze）团队整体并入豆包体系，办公能力向「豆包工作」集中，TRAE 则继续作为编程产品线发展。所以现在看字节，要区分**豆包（办公/通用）** 和 **TRAE（编程）** 两条线，且两者有部分能力重叠。

---

## 三、国内 AI 创业公司

| 公司 | C 端产品 | 最新基座（2026-11 核对） | 特点 |
| --- | --- | --- | --- |
| **DeepSeek（深度求索）** | DeepSeek 网页版 / App、DeepSeek Harness（DSH） | **DeepSeek-V4-Pro / V4-Flash**（2026-04） | 百万 token 上下文；Agentic Coding 达开源最佳；开源 + 低价是它的标签 |
| **月之暗面（Moonshot AI）** | Kimi 智能助手、Kimi Code | **Kimi K3-Max**（2026-07） | 2.8 万亿参数，原生视觉，100 万 token 上下文；主打软件工程、知识工作、深度研究 |
| **智谱 AI**（清华背景） | 智谱清言 | **GLM-5.3（Max）**（2026-08） | 开源推理模型，100 万 token 上下文；长文本推理与动态代码逻辑是相对优势 |
| **MiniMax** | MiniMax 大模型 App、MiniMax Code | **M3**（2026-05） | 自研 MSA 稀疏注意力，100 万上下文，原生多模态（图片/视频输入），**能操作电脑桌面** |
| **小米** | 小爱同学（终端侧） | **MiMo-V2.5-Pro**（2026-04，MIT 协议开源） | 100 万上下文，深度适配 Agent 与 Coding；主打「开源可商用」 |

> **原文这里要改两处**：
> 1. «Mi-Mo-V2.5-Pro» 拼写错了，正确是 **MiMo-V2.5-Pro**（小米的模型叫 MiMo）。
> 2. **小米不该放在「创业 AI 公司」里**——它是手机/硬件大厂，AI 是业务线之一。而且原文末尾还列了「6. 千问」，**千问（Qwen）是阿里的产品，不是公司**，属于分类错误。

### 关于 DeepSeek「有视力的模型」

原文写「V4-Pro、V4-Flash 及有视力的模型」。**目前公开信息里，DeepSeek-V4 系列的官方介绍集中在百万上下文、Agent 能力、世界知识、推理性能，没有明确发布「V4 视觉版」**。

- 如果你看到的是别的渠道消息，建议以 DeepSeek 官方发布为准；
- 需要多模态（看图/看视频）时，现阶段更稳的选择是 **Kimi K3（原生视觉）**、**MiniMax M3（原生多模态）**、**Qwen3.8-Max（多模态基座）**。

> 这一条我选择**标注为待核实**而不是直接写进正文——宁可少说，不要给错信息。

---

## 四、海外公司

| 公司 | 最新模型（2026-11 核对） | 主力产品 | 特点 |
| --- | --- | --- | --- |
| **OpenAI**（美国） | **GPT-6.2 Astra / Sol**（2026-11-17） | ChatGPT、ChatGPT Work、Codex | Astra 面向复杂专业任务，Sol 面向日常工作；主打「更少 token 完成同样任务」 |
| **Anthropic**（美国） | **Claude Opus 5**（2026-07-24）；**Claude Fable 5.2 / Mythos 5.2**（2026-11） | Claude、Claude Code、Claude Cowork | Opus 线强在长时运行 Agent、编程与专业工作；Fable/Mythos 是前沿研究线 |
| **Google DeepMind**（美国） | **Gemini 4 Argon**（2026-10） | Gemini App、Antigravity、Gemini CLI、AI Mode | 面向「深度工作」：编程、金融、法律、网络安全、科研；Vals Index 排名第一 |
| **SpaceXAI**（美国，原 xAI） | **Grok 4.7**（2026-09） | Grok、Grok Build、X 平台 | 接入 X 实时数据，推理与编程强，适合实时资讯类任务 |
| **Meta**（美国） | **Muse Spark 1.3**；**Muse Spark Open**（2026-10，开放权重） | Meta AI | 已从「纯闭源」转回开源：Muse Spark Open 816B 总参数 / 42B 激活，针对苹果 MLX 优化，两台 Mac Studio 可跑 |
| **Microsoft**（美国） | 主要用 OpenAI 模型 + 自研 MAI 系列 | Copilot、Copilot Studio、GitHub Copilot | 不自研通用基座的说法已过时，但**主力仍是 OpenAI 模型** |
| **NVIDIA**（美国） | **Nemotron 4**（开发中，据报道 ≥1 万亿参数） | 算力 + 开源模型 + 模型路由 | 卖铲子的同时自己做开源模型，目的是带动 GPU 需求 |

> **原文这里要改三处**：
> 1. «xAI（马斯克创办）» —— **xAI 已经不存在了**：2026-02 被 SpaceX 全资收购，2026-05 马斯克宣布解散独立公司，2026-07 正式更名 **SpaceXAI**。
> 2. «Meta……闭源：Muse Spark» 与下一行「开源生态极强、开源免费商用」**自相矛盾**。事实上 Meta 在 2026-10 发布了 **Muse Spark Open**（开放权重），已经回到开源路线。
> 3. «Microsoft 不自研基座，使用 OpenAI 模型» —— 不准确。微软自 2025 年起有自研 **MAI 系列**，只是 Copilot 主力仍调 OpenAI 模型。

### 关于「Meta（元宇宙，Facebook 母公司）」

这个括注**过时了**：

- Meta 是 2021 年由 Facebook 改名的母公司，这个沿革对；
- 但「元宇宙」早已不是它的定位，**现在的 Meta 是「AI + 社交 + 广告」**，2026 年的重点是开源模型 Muse Spark 系列和 AI 助手 Meta AI；
- 写「Facebook 母公司」没错，写「元宇宙公司」会让读者误判它的技术路线。

### 关于 Anthropic 的两条产品线（原文容易混）

原文把 Claude Code 描述为「基于 Claude Opus5」——**这是对的**，但要注意 Anthropic 现在有两条模型线：

| 模型线 | 代表 | 定位 |
| --- | --- | --- |
| **Opus 线** | Claude Opus 5 | 长时运行 Agent、编程、专业工作（Claude Code 用的就是它） |
| **Fable / Mythos 线** | Claude Fable 5.2、Claude Mythos 5.2 | 前沿科研（官方说 Fable 5.2 解决了两个理论物理未解问题） |

> 所以「旗舰最新」要分场景说：**干工程看 Opus 5，做科研看 Fable 5.2**。原文只写了 Fable，用它代表「旗舰」会漏掉 Opus 这条更常用的线。

---

## 五、三种使用方式与模型聚合平台

### 三种使用方式（原文三条，补充细节）

| 方式 | 成本 | 门槛 | 适合谁 | 注意 |
| --- | --- | --- | --- | --- |
| **1. 网页 / App** | 免费为主，部分功能要会员 | 最低 | 绝大多数人 | 免费版常有上下文长度、并发数、模型档位限制 |
| **2. 开发者 API** | 按量计费（每百万 token 多少钱） | 要会写程序 | 开发者、企业集成 | 贵的不是模型，是**长上下文和反复重试** |
| **3. 本地部署开源模型** | 不要钱，要显卡 | 要会配环境 | 隐私敏感、长期高频使用 | 显存决定能跑多大模型；量化会损失一点效果 |

### 模型聚合平台（原文列了五个，这里补全职责）

> 原文写「阿里云百炼，火山方舟（字节），百度千帆，**腾讯云百炼**，硅基流动」——**「腾讯云百炼」不存在**。百炼是**阿里云**的品牌；腾讯云对应的平台叫 **ADP（智能体开发平台）**。

| 平台 | 归属 | 能干什么 | 类比 |
| --- | --- | --- | --- |
| **百炼** | 阿里云 | 一站式调用通义及第三方模型、RAG、Agent 编排 | 模型超市 + 应用工厂 |
| **火山方舟** | 字节跳动 | 调用豆包/Seed 及第三方模型 | 同上，强在 Seed 系 |
| **千帆** | 百度 | 调用文心及第三方模型 | 同上，强在文心系 |
| **ADP（智能体开发平台）** | 腾讯云 | 企业级 AgentOps：Agent 编排、工作流、企业治理 | 偏「企业内落地」 |
| **硅基流动（SiliconFlow）** | 创业公司 | 聚合大量开源模型，按 token 计费，主打便宜 | 开源模型的「便利店」 |
| **OpenRouter** | 海外 | 一个 key 调所有海外主流模型 | 海外的等价物 |

> **为什么需要聚合平台**：一个人想同时用 GPT、Claude、Gemini、Qwen，如果每家都注册、都充值、都维护一套 SDK，成本极高。聚合平台把「多账号多密钥」收敛成「一个 key 一套接口」，代价是加了一层价差。

---

## 六、Agent 产品线全景

> 原文这一节的小标题叫「公司 AI Agent 产品（API 调用）」，但列的内容其实**不都是 API**，也有客户端和 IDE。这里按「形态」重新组织，更好对照。

### 海外

| 公司 | Agent 产品 | 形态 | 一句话 |
| --- | --- | --- | --- |
| **OpenAI** | ChatGPT（含 Agent 模式）、ChatGPT Work、**Codex**（CLI / IDE / 云端） | App + CLI + IDE | Codex 是开源终端编程 Agent；GPT-6.2 原生支持多步智能体任务 |
| **OpenAI** | Custom GPTs | 网页 | 只能问答、调有限工具，**不能操作本地电脑、不能执行终端命令** |
| **Anthropic** | **Claude Code** | CLI | 王牌编程 Agent，基于 Claude Opus 5；能读整个仓库、改多文件、执行 shell、git 提交 |
| **Anthropic** | **Claude Cowork** | 桌面 App | 办公协作 Agent：处理文档、多步骤工作流、长文档 + 工具调用，可后台长跑；已规划移动端远程调度 |
| **Google** | **Antigravity** | IDE | Agent 优先的编程环境（Gemini 4 Argon 的主要承载之一） |
| **Google** | Gemini CLI | CLI | 开源终端 Agent |
| **Google** | Gemini Deep Research | 网页 | 自动联网调研并输出报告 |
| **Google** | Vertex AI Agent Builder | 平台 | 企业级 Agent 搭建 |
| **SpaceXAI** | Grok 4.7 + **Grok Build** | App + 平台 | 原生适配 Grok Bot harness；Grok Build 面向编程 |
| **Microsoft** | **Copilot Studio** | 平台 | 低代码搭 Agent（**注意：这是微软的产品，不是 OpenAI 的**） |
| **Microsoft** | GitHub Copilot（Agent 模式） | IDE | 在编辑器里改代码、跑测试 |

> **原文这里要改两处**：
> 1. «Copilot Studio（微软，OpenAI 底层）» 被列在 **OpenAI** 名下了——它是**微软**的产品，只是底层可能调 OpenAI 模型。归属要移到 Microsoft。
> 2. «GPT-5.6 Agent» 的版本号已经旧了：GPT-5.6 是 2026-07 发布（Sol/Terra/Luna 三个版本），**2026-11-17 已发布 GPT-6.2**（Astra / Sol）。

### 国内

| 公司 | Agent 产品 | 形态 | 一句话 |
| --- | --- | --- | --- |
| **字节跳动** | **TRAE**（原 TRAE Work + TRAE Code 合并） | IDE + Agent 工作台 | 可读写本地项目、执行命令；2026-10 合并后统一入口，产品成熟度高 |
| **字节跳动** | 豆包工作 | 网页 / App | 办公场景：文档、PPT、多 Agent 协作 |
| **阿里巴巴** | **千问办公（Qwen Work）** | 桌面 + 云端 | 对接钉钉；处理本地文档、表格，自动做周报、数据汇总 |
| **腾讯** | **WorkBuddy** | 桌面 App | 办公生产力 Agent，可开箱即用；混元 Hy3 深度适配 |
| **腾讯** | CodeBuddy | IDE | 编程辅助 |
| **月之暗面** | **Kimi Agent** + **Kimi Code** | App + CLI | 超大上下文是核心优势：几十万字文档当「记忆」，适合文献调研、合同审核 |
| **深度求索** | **DeepSeek Harness（DSH）** | 桌面 / 终端 | DeepSeek 官方的 Agent 运行框架 |
| **智谱 AI** | 智谱清言（Agent 模式） | App | 基于 GLM-5.3 |
| **MiniMax** | MiniMax Code | IDE / App | 基于 M3，可操作电脑桌面 |
| **百度** | 文心一言（Agent 模式）、Comate | App + IDE | 基于文心 5.1 |

> **判断一个产品是不是「真 Agent」，看三条**：① 能不能调工具（不只是聊天）② 能不能多步自主执行 ③ 出错能不能自己纠正。**只满足第一条的是「函数调用」，不是 Agent。**

---

## 七、更正清单：原文哪里错了

| # | 原文 | 问题 | 正确说法 |
| --- | --- | --- | --- |
| 1 | 百度「最新基座是 ERNIE-4.5」 | **版本过时** | 2026-01 发布文心 5.0，2026-05 发布**文心 5.1** |
| 2 | 平台列表里的「**腾讯云百炼**」 | **不存在** | 百炼是**阿里云**的；腾讯云对应的是 **ADP（智能体开发平台）** |
| 3 | 小米「Mi-Mo-V2.5-Pro」 | **拼写错** | **MiMo-V2.5-Pro**（小米的模型叫 MiMo，MIT 协议开源） |
| 4 | 小米列在「创业 AI 公司」；末尾还有「6. 千问」 | **分类错** | 小米是硬件大厂；**千问是阿里的产品**，不是公司 |
| 5 | Meta「闭源：Muse Spark」+「开源生态极强、开源免费商用」 | **自相矛盾且已过时** | Meta 2026-10 发布 **Muse Spark Open**（开放权重，816B/A42B），已回到开源路线 |
| 6 | 「xAI（马斯克创办，美国）」 | **公司已不存在** | 2026-02 被 SpaceX 收购，2026-05 解散独立公司，2026-07 更名 **SpaceXAI** |
| 7 | 「Microsoft 不自研基座，使用 OpenAI 模型」 | **不准确** | 微软有自研 **MAI 系列**，但 Copilot 主力仍用 OpenAI 模型 |
| 8 | 「Copilot Studio」列在 OpenAI 名下；「GPT-5.6 Agent」 | **归属错 + 版本旧** | Copilot Studio 是**微软**产品；2026-11-17 已发布 **GPT-6.2**（Astra/Sol） |
| 9 | 「Google DeepMind」被列了两次（第 3 项是空的，又一个第 3 项） | 排版重复 | 已合并为一节 |
| 10 | 腾讯元宝「混元 HY3（免费）」 | 表述模糊 | 准确说法：**混元 Hy3（preview 2026-04，已开源）**，免费是因为开源可自部署，不是「产品免费」 |
| 11 | DeepSeek「及有视力的模型」 | **未核实** | 公开信息未见 V4 视觉版；需要多模态请看 Kimi K3 / MiniMax M3 / Qwen3.8-Max |
| 12 | 字节「TRAE Work：面向开发者的编程 Agent」 | **产品已合并** | 2026-10-09 TRAE Work（办公）与 TRAE Code（编程）**合并为一个 TRAE**，保留 Agent + IDE 双模式 |

---

## 八、按场景选谁：决策表

| 你的场景 | 首选 | 备选 | 理由 |
| --- | --- | --- | --- |
| 日常问答、写材料 | 豆包 / 元宝 / Kimi（国内）；ChatGPT / Gemini（海外） | 各家网页版都行 | 免费额度够用，差异主要在写作风格 |
| 写代码、改仓库 | **Claude Code**（深度重构）/ **TRAE**（国内、开箱即用） | Codex、Gemini CLI、通义灵码、CodeBuddy | 看「能不能读整个仓库 + 跑测试 + git 提交」 |
| 科研 / 难题推理 | **Claude Fable 5.2** / GPT-6.2 Astra | Gemini 4 Argon | 这一档拼的是深度推理与专业领域准确率 |
| 长文档分析（几十万字） | **Kimi K3** / Qwen3.8-Max / DeepSeek-V4 | GLM-5.3 | 拼上下文窗口（都到 100 万 token 了） |
| 实时资讯 | **Grok 4.7**（接 X 实时数据） | 各家「联网搜索」模式 | 拼数据新鲜度 |
| 办公自动化（做表、写周报） | **Qwen Work** / TRAE / WorkBuddy | Claude Cowork | 拼「能不能直接产出可编辑文件」 |
| 私有化 / 数据不出内网 | **DeepSeek-V4（开源）** / Qwen / GLM / MiMo | MiniMax M3 | 开源权重 + MIT/Apache 类协议，可自部署 |
| 只想要便宜 | 硅基流动 / 火山方舟 / 百炼的按量计费 | 本地部署 | 开源模型 + 聚合平台 = 最低单价 |
| 一次调多家模型 | **百炼 / 火山方舟 / 千帆 / ADP / 硅基流动** | OpenRouter | 一个 key 省掉多头注册 |

> **最后提醒**：这份表最多三个月就会过时。**真正稳定的判断方法**是回到那三个维度——**基座看能力上限、产品看你用不用得上、Agent 看你敢不敢把活交给它**。型号会换，这三层不会。

---

## 参考来源（2026-11-18 核对）

- [Seed 2.1 官方发布（2026-06）](https://seed.bytedance.com/zh/blog/seed2-1-officially-released-advancing-ai-productivity)
- [Qwen 3.8-Max 官方发布（2026-08）](https://www.alibabagroup.com/zh-HK/document-2021044032125272064)
- [DeepSeek-V4 发布（2026-04）](https://www.toutiao.com/article/7632220581559075370/)
- [Kimi K3 发布报道（2026-07）](https://www.c114.net.cn/industry/100894.html)
- [腾讯混元 Hy3 preview（2026-04）](https://www.tencent.com/zh-hk/%e9%a8%b0%e8%a8%8a%e6%b7%b7%e5%85%83hy3-preview%e7%99%bc%e5%b8%83%ef%bc%9a%e4%b8%bb%e6%89%93%e5%af%a6%e7%94%a8%ef%bc%8cagent%e8%83%bd%e5%8a%9b%e5%a4%a7%e5%b9%85%e6%8f%90%e5%8d%87/)
- [小米 MiMo-V2.5 开源（2026-04）](https://mp.weixin.qq.com/s/CX0mcpaE_sCTKxRBzebMvQ)
- [MiniMax M3 官方（2026-05）](https://www.minimax.cn/blog/minimax-m3)
- [GPT-6.2 Astra / Sol（2026-11-17）](https://m.ithome.com/html/1028577.htm)
- [Claude Opus 5（2026-07-24）](https://www.anthropic.com/news)
- [Claude Fable 5.2 / Mythos 5.2（2026-11）](https://www.anthropic.com/claude-fable-and-mythos-5-2)
- [Gemini 4 Argon 官方博客（2026-10）](https://blog.google/intl/ko-kr/products/introducing-gemini-4-argon-kr/)
- [Grok 4.7 发布（2026-09）](https://finance.sina.cn/2026-09-22/detail-inissiti8916820.d.html)
- [xAI 解散并入 SpaceX（2026-05）](https://finance.sina.com.cn/wm/2026-05-07/doc-inhwzvzp5212284.shtml)
- [xAI 更名 SpaceXAI（2026-07）](https://www.163.com/dy/article/L17IN8A70511BLFD.html)
- [Meta Muse Spark Open（2026-10）](https://www.36kr.com/p/4027793714991746)
- [NVIDIA Nemotron 4 报道（2026-10）](https://www.digitaltoday.co.kr/cn/list?view_type=sm&sc_word=Nemotron%203.5%20Lightning)
- [文心 5.1 发布（2026-05）](https://www.ithome.com/0/948/079.htm)
- [阿里百炼 vs 腾讯云 ADP](https://cloud.tencent.com.cn/developer/article/2771037)
- [字节 TRAE Work 与 TRAE Code 合并（2026-10）](https://eu.36kr.com/zh/p/4028282440717952)
