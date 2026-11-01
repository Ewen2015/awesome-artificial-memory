# 研究：一份完整的记忆地图

**研究日期：** 2026-11-01 · **语言：** 简体中文 / English

这个目录是一份关于记忆的研究库，写给**做记忆系统的人**。它覆盖人脑记忆、认知架构、已经上线的 agent 记忆、研究界的架构、它们底下那一层存储，以及持久化带来的安全后果。

它不是一张链接表。它是一个论点，用八份文档讲完，带引用。

---

## 论点，一段话

每一个记忆系统——生物的也好，工程的也好——都在回答四个问题：**存什么**、**什么触发写入**、**怎么索引和检索**、**什么被忘掉**。大多数系统只认真回答了其中一个，剩下三个是悄悄默认的。好的系统知道自己回答的是哪一个。而最好的系统——人脑是唯一的存证——做了一件所有工程系统都没做的事：它把经验**编译**成程序。今天的 agent 记忆只会累积。这个差距，就是这份研究库的主题。

---

## 按你的问题选读法

**只想要鸟瞰图。** 读 [00-timeline.zh-CN.md](00-timeline.zh-CN.md)。1885 → 2026，分九段，二十分钟能读完。读完就可以停。你会知道这个领域站在哪里。

**这季度要动手做系统。** 先读 [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md)（十九个已上线产品实际在做什么，来自各家自己的文档），再读 [05-infrastructure.zh-CN.md](05-infrastructure.zh-CN.md)（将来会咬你的那些存储决策），最后读 [06-security.zh-CN.md](06-security.zh-CN.md)（会咬你用户的那些）。

**想知道研究界到底证明了什么、没证明什么。** 读 [04-research-architectures.zh-CN.md](04-research-architectures.zh-CN.md)。它把"已确立"和"只是声称"分开，并且收录了 2026 年那批总结论是负面的基准。

**从认知科学过来，想知道做 AI 的人错在哪。** [01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 第 7 节列了十二个具体错误。[02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md) 讲清楚了 1970–2000 年代那些架构做了什么、今天的系统跳过了什么。

---

## 文档清单

| # | 文档 | 它回答什么 | 行数 |
|---|---|---|---|
| 00 | [00-timeline.zh-CN.md](00-timeline.zh-CN.md) | 我们是怎么从"记忆是存储"走到"记忆是关于保留什么的决策"的？ | 218 |
| 01 | [01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) | 生物记忆到底怎么运作，尤其是遗忘？ | 447 |
| 02 | [02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md) | ACT-R、SOAR、全局工作台建模了哪些 LLM agent 没有的东西？ | 245 |
| 03 | [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) | 十九个已上线产品实际在做什么？ | 371 |
| 04 | [04-research-architectures.zh-CN.md](04-research-architectures.zh-CN.md) | 文献确立了哪些，哪些只是声称？ | 372 |
| 05 | [05-infrastructure.zh-CN.md](05-infrastructure.zh-CN.md) | 存储层会在哪里坏？ | 247 |
| 06 | [06-security.zh-CN.md](06-security.zh-CN.md) | 持久化给攻击者开了哪些门？ | 223 |
| — | [sources.zh-CN.md](sources.zh-CN.md) | 全部引用，带 DOI。 | 254 |
| — | [sources-corrections.md](sources-corrections.md) | 十个广泛流传的错误引用，及正确形式（英文）。 | 159 |

每份文档独立成立，同时在文档之间互相引用：顺序读会累积，单读一份也能用。

---

## 约定

**双语成对。** 每份文档有两个版本：`NAME.md` 是英文，`NAME.zh-CN.md` 是中文。它们不是逐句对照的翻译——中文版是按中文写出来的，所以例子和节奏在某些地方不同。论点、数字、引用完全一致。`sources.md` 和 `sources.zh-CN.md` 共用同一套书目条目，这是有意的：翻译会让条目无法核对；被翻译的是条目周围的框架和注解。

**引用方式。** 期刊条目给作者、年份、标题、卷、期、页，附 DOI。AI 论文给 arXiv 编号。产品论断引厂商自己的文档页。**没有引用的论断就是作者自己的论证，并且按论证来写**，不伪装成事实。

**"已核对"在这里指什么。** 书目信息和 arXiv 编号在 2026-11-01 对照出版社或 arXiv 核对过。产品文档在上述日期从厂商自己的页面上读取。报告的数字是作者们自己报告的数字，不是独立复现，在重要处会标注这一点。已知的错误引用集中在 [sources-corrections.md](sources-corrections.md)，而不是悄悄改掉。

**负面结果是故意留着的。** 只汇报"什么管用"的研究库是销售材料。这里有 MINTEval 的 27.9%、MPBench 的 50.46% 攻击成功率、从"已删除"向量里恢复出的 25.5% 人名、Library Drift 的 +0.0 个百分点。那些数字是另外一些数字值得相信的原因。

**日期。** 这里的一切都标了日期。记忆研究的变化速度比它里面的引用快，所以每份文档带着研究日期，读者应当假设产品格局从那以后已经变了。

---

## 这份研究库刻意不做的三件事

**不给产品排名。** [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) 里的比较是描述性的，那一章的结论是结构性的，不是对任何厂商的判决。

**不提架构方案。** 全库有八条编号的推论，但没有参考实现——因为这个领域的诚实现状是：没有人有一个被验证过的设计。

**不把生物当蓝图。** [01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 第 6 节那张"机制 → 最接近的 AI 对应物"表，标出了每个类比在哪里成立、在哪里失效，而**失效的那一半更有用**。

---

## 参与

最有价值的贡献是纠错。一个错的 DOI、一条已经不成立的产品论断、一个被刷新的基准数字——带上出处开 issue。格式见 [../CONTRIBUTING.md](../CONTRIBUTING.md)。

---

*从这里开始：[00-timeline.zh-CN.md](00-timeline.zh-CN.md) · English: [README.md](README.md) · 回到 [../README.md](../README.md)*
