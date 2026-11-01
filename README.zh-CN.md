<h1 align="center">Awesome Artificial Memory</h1>

<p align="center">一份关于记忆的研究库——生物的、认知的、工程的。</p>

<p align="center">
  <a href="research/README.zh-CN.md">研究</a> ·
  <a href="research/00-timeline.zh-CN.md">时间线</a> ·
  <a href="README.md">English</a>
</p>

---

## 核心想法

传统 AI 优化的是"记得多"。而人的智能和**选择性遗忘**的能力绑在一起——过去四年 agent 工程的历史，就是慢慢重新发现这件事的历史。今天每一个认真做的记忆系统都有写入策略、检索策略和删除策略。做得好的那些知道：第三个最难。

记忆的目的不是存住过去。它是预测下一步需要什么——而遗忘是让这个预测保持准确的东西。

## 这里有什么

这个仓库分两层。

**精选列表**（下面这些）是原本的收藏：工具、框架、关键论文，保持简短，逐条核对过。

**[`research/`](research/README.zh-CN.md) 里的研究库**是主体——八份文档，每份约七千字，中英双语，从 Ebbinghaus 一路讲到 2026 年的 agent 记忆栈。

| 文档 | 它回答什么 |
|---|---|
| [00 · 时间线](research/00-timeline.zh-CN.md) | 我们是怎么从"记忆是存储"走到"记忆是关于保留什么的决策"的？1885 → 2026。 |
| [01 · 人的记忆](research/01-human-memory.zh-CN.md) | 生物记忆怎么运作——以及为什么遗忘是机制而不是故障。 |
| [02 · 认知架构](research/02-cognitive-architectures.zh-CN.md) | ACT-R、SOAR、全局工作台建模了哪些今天的 agent 跳过的东西。 |
| [03 · Agent 原生记忆](research/03-agent-native-memory.zh-CN.md) | 十九个已上线产品实际在做什么，来自各家自己的文档。 |
| [04 · 研究架构](research/04-research-architectures.zh-CN.md) | 文献确立了哪些、只是声称了哪些、基准在哪里给出了负面结果。 |
| [05 · 基础设施](research/05-infrastructure.zh-CN.md) | 存储层：索引、过滤、时间、多租户、协议。 |
| [06 · 安全](research/06-security.zh-CN.md) | 投毒、删除、隔离——持久化让什么成为可能。 |
| [来源](research/sources.zh-CN.md) | 全部引用带 DOI，另附[十条常见错误引用的更正](research/sources-corrections.md)。 |

想要鸟瞰图就从时间线开始。这一季度要动手做事，就从
[03](research/03-agent-native-memory.zh-CN.md) 开始。

## 🧠 记忆与遗忘的神经科学

巩固与主动遗忘的生物学和心理学机制。完整论述（60 余条引用）见
[01-human-memory.zh-CN.md](research/01-human-memory.zh-CN.md)。

*   **[书籍]** [*Why We Forget and How to Remember Better: The Science Behind Memory*](https://www.bc.edu/bc-web/bcnews/science-tech-and-health/psychology/the-science-behind-memory.html) — Budson, A. E., & Kensinger, E. A. (2023)，牛津大学出版社。
    *   记忆是一个主动的、循环的过程——一座不断重建的建筑，不是一份静态的过去记录。
    *   讲"遗忘为什么是适应性的而不是缺陷"最好用的一本书。
*   **[论文]** [Replay-triggered brain-wide activation in humans](https://www.nature.com/articles/s41467-024-51582-5) — Huang, Q., Xiao, Z., Yu, Q., et al. (2024)，*Nature Communications*。
    *   把离散经验巩固成连贯叙事，塑造了认知地图。
    *   瞬时的记忆再激活与序列回放伴随着全脑范围的参与。
*   **[论文]** The persistence and transience of memory — Richards, B. A., & Frankland, P. W. (2017)，*Neuron* 94(6), 1071–1084。
    *   "遗忘是适应性的"这条的标准引用，也是最常被引错的一条。

## 🛠️ 工具与框架

对记忆提供细粒度控制的产品——这是实现选择性遗忘的前提。其中十九个的真实写入与检索策略，在
[03-agent-native-memory.zh-CN.md](research/03-agent-native-memory.zh-CN.md) 里做了对照。

| 项目 | 类型 | 简要说明 | 相关论文 |
|---|---|---|---|
| [Mem0](https://github.com/mem0ai/mem0) | 记忆层 / SDK | 自我改进的记忆层，托管 + 开源；写入时抽取与整合。 | [Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory](https://arxiv.org/abs/2504.19413) |
| [Zep](https://github.com/getzep/zep) | 记忆平台 | 关于事实与实体的双时间知识图谱；context engineering 平台。 | [Zep: A Temporal Knowledge Graph Architecture for Agent Memory](https://arxiv.org/abs/2501.13956) |
| [MemoryOS](https://github.com/BAI-LAB/MemoryOS) | 记忆 OS / 架构 | 分层记忆（STM/MTM/LPM）、热度分提升、本地优先。 | [MemoryOS of AI Agent](https://arxiv.org/abs/2506.06326) |
| [Letta](https://github.com/letta-ai/letta) | 虚拟上下文 / 有状态 agent | 虚拟上下文管理与多 agent 记忆模式；MemGPT 这一支工作。 | [MemGPT: Towards LLMs as Operating Systems](https://arxiv.org/abs/2310.08560) |
| [LangChain Memory](https://github.com/langchain-ai/langchain) | 库 / 集成 | LangChain 生态的记忆组件。 | [LangChain memory overview](https://docs.langchain.com/oss/python/concepts/memory) |

## 🤝 参与贡献

最有价值的贡献是纠错：一个错的 DOI、一条已经不成立的产品论断、一个被刷新的基准数字。见
[CONTRIBUTING.md](CONTRIBUTING.md)。

## 许可

MIT —— 见 [LICENSE](LICENSE)。
