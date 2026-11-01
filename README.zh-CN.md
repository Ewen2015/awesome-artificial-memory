<h1 align="center">Awesome Artificial Memory</h1>

<p align="center">一份关于记忆的研究——生物的、认知的、工程的。</p>

<p align="center">
  <a href="00-timeline.zh-CN.md">时间线</a> ·
  <a href="03-agent-native-memory.zh-CN.md">Agent 记忆</a> ·
  <a href="sources.zh-CN.md">来源</a> ·
  <a href="README.md">English</a>
</p>

---

## 这是什么

一个关于记忆的论点，用八份文档讲完，带引用。它从 1885 年的 Ebbinghaus 开始，到 2026 年末的 agent 记忆栈结束，覆盖人的记忆、认知架构、十九个已上线产品、研究文献、它们底下那一层存储，以及持久化给攻击者开了哪些门。

它不是一张链接表。每一份文档都在回答同样四个问题——**存什么**、**什么触发写入**、**怎么索引和检索**、**什么被忘掉**——外加一条横切四者的第五轴：归谁所有，谁能看到。

## 核心想法

传统 AI 优化的是"记得多"。而人的智能和**选择性遗忘**的能力绑在一起——过去四年 agent 工程的历史，就是慢慢重新发现这件事的历史。今天每一个认真做的记忆系统都有写入策略、检索策略和删除策略。做得好的那些知道：第三个最难。

记忆的目的不是存住过去。它是预测下一步需要什么——而遗忘是让这个预测保持准确的东西。

而最好的系统做了一件所有工程系统都没做的事：人的记忆会**编译**。它把反复出现的情节变成程序，然后不再需要那些情节。今天的 agent 记忆只会累积。这个差距，就是这份研究的主题。

## 按你的问题选读法

**只想要鸟瞰图。** 从 [00-timeline.zh-CN.md](00-timeline.zh-CN.md) 开始。1885 → 2026，分九段，二十分钟。读完就可以停，你会知道这个领域站在哪里。

**这季度要动手做系统。** [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) 讲十九个已上线产品实际在做什么（来自各家自己的文档）；[05-infrastructure.zh-CN.md](05-infrastructure.zh-CN.md) 讲将来会咬你的那些存储决策；[06-security.zh-CN.md](06-security.zh-CN.md) 讲会咬你用户的那些。

**想知道研究界到底证明了什么、没证明什么。** [04-research-architectures.zh-CN.md](04-research-architectures.zh-CN.md) 把"已确立"和"只是声称"分开，并收录了 2026 年那批总结论是负面的基准。

**从认知科学过来，想知道做 AI 的人错在哪。** [01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 第 7 节列了十二个具体错误。[02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md) 讲清楚了 1970–2000 年代那些架构做了什么、今天的系统跳过了什么。

## 文档

| # | 文档 | 它回答什么 |
|---|---|---|
| 00 | [时间线](00-timeline.zh-CN.md) | 我们是怎么从"记忆是存储"走到"记忆是关于保留什么的决策"的？ |
| 01 | [人的记忆](01-human-memory.zh-CN.md) | 生物记忆到底怎么运作——以及为什么遗忘是机制而不是故障。 |
| 02 | [认知架构](02-cognitive-architectures.zh-CN.md) | ACT-R、SOAR、全局工作台建模了哪些今天的 agent 跳过的东西？ |
| 03 | [Agent 原生记忆](03-agent-native-memory.zh-CN.md) | 十九个已上线产品实际在做什么，来自各家自己的文档。 |
| 04 | [研究架构](04-research-architectures.zh-CN.md) | 文献确立了哪些、只是声称了哪些、基准在哪里给出了负面结果。 |
| 05 | [基础设施](05-infrastructure.zh-CN.md) | 存储层：索引、过滤、时间、多租户、协议。 |
| 06 | [安全](06-security.zh-CN.md) | 投毒、删除、隔离——持久化让什么成为可能。 |
| — | [来源](sources.zh-CN.md) | 全部引用，带 DOI。 |
| — | [引用更正](sources-corrections.zh-CN.md) | 十个广泛流传的错误引用，及正确形式。 |

每份文档独立成立，同时在文档之间互相引用：顺序读会累积，单读一份也能用。每份都有英文版（`NAME.md`）和中文版（`NAME.zh-CN.md`）。

## 覆盖了哪些产品

[03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) 里的对照覆盖十九个记忆系统，全部在 2026-11-01 从厂商自己的文档读取：Claude Code、ChatGPT、Copilot、Cursor、Devin、Windsurf、OpenHands、Letta、LangChain/LangGraph、LlamaIndex、Mem0、Zep、MemoryOS、MemOS、A-MEM、Roo Code、Qwen Code、Kimi、豆包。那一章的结论是结构性的，不是排名——它们各自独立地收敛到"索引加主题文件"，而且没有一个做编译。

调研期间剔除了四个产品，因为它们的文档已经迁移、消失或无法取得：理由写在该文档第 8 节，而不是用第三方文章重建。

## 约定

**双语成对。** 每份文档有两个版本。中文版是按中文写出来的，不是逐句对照翻译，所以例子和节奏在某些地方不同；论点、数字、引用完全一致。

**引用方式。** 期刊条目给作者、年份、标题、卷、期、页，附 DOI。AI 论文给 arXiv 编号。产品论断引厂商自己的文档页。**没有引用的论断就是作者的论证，按论证来写**，不伪装成事实。

**负面结果是故意留着的。** MINTEval 的 27.9%、MPBench 的 50.46% 攻击成功率、从"已删除"向量里恢复出的 25.5% 人名、Library Drift 的 +0.0 个百分点。那些数字是另外一些数字值得相信的原因。

**一切标了日期。** 每份文档带着研究日期。记忆研究的变化速度比它里面的引用快，产品格局尤其应当假设在 2026-11-01 之后已经变了。

## 刻意不做的三件事

不给产品排名，不提架构方案，不把生物当蓝图。全库有八条编号的推论，但没有参考实现——因为这个领域的诚实现状是：没有人有一个被验证过的设计。[01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 第 6 节那张"机制 → 最接近的 AI 对应物"表标出了每个类比在哪里成立、在哪里失效，而**失效的那一半更有用**。

## 参与

最有价值的贡献是纠错：一个错的 DOI、一条已经不成立的产品论断、一个被刷新的基准数字。见 [CONTRIBUTING.md](CONTRIBUTING.md)。

## 许可

MIT —— 见 [LICENSE](LICENSE)。
