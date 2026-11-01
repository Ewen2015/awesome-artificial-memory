# 来源：总参考文献

**研究日期：** 2026-11-01

这个仓库里所有引用，集中在一页，并标注它服务于哪一份文档。有 DOI 的给 DOI，否则给 arXiv 或出版社链接。

**关于条目为什么是英文的。** 参考文献条目本身不翻译——期刊名、卷期页码、作者姓名在中文文献里同样按原样引用，翻译会让它无法核对。这一页的**框架、分组和说明**是中文的；条目是国际通用的引用形式。想看同样的清单加英文注解，见 [sources.md](sources.md)。

**关于可靠性。** 书目信息核对到出版社、DOI 注册或 arXiv 页面。产品文档于上述日期从各家自己的页面读取，引用写在 [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) 里而不在这里重复。流传很广的错误引用在下面第 9 节列出并更正。

---

## 1. 人类记忆与遗忘

用于 [01-human-memory.zh-CN.md](01-human-memory.zh-CN.md)。

### 奠基文献

**给读者的提示：** 这一节里最重要的是三篇，其余是背景。Ebbinghaus（1885）确立了"遗忘可以被测量"这件事；Müller & Pilzecker（1900）发现了倒摄抑制，也就是后来所有"新记忆干扰旧记忆"研究的起点；Bartlett（1932）把记忆从"回放"改写成"重建"，这是今天讨论 agent 记忆"污染"时最直接的心理学祖先。

- Ebbinghaus, H. (1885). *Über das Gedächtnis: Untersuchungen zur experimentellen Psychologie*. Leipzig: Duncker & Humblot. 英译：*Memory: A Contribution to Experimental Psychology* (H. A. Ruger & C. E. Bussenius, trans., 1913), Teachers College, Columbia University. [DOI 10.1037/10011-000](https://doi.org/10.1037/10011-000)（该 DOI 注册给 1913 年英译本，不是德文原版）
- Müller, G. E., & Pilzecker, A. (1900). *Experimentelle Beiträge zur Lehre vom Gedächtniss*. Leipzig: J. A. Barth.
- Bartlett, F. C. (1932). *Remembering: A Study in Experimental and Social Psychology*. Cambridge University Press.
- Hebb, D. O. (1949). *The Organization of Behavior: A Neuropsychological Theory*. Wiley.
- Atkinson, R. C., & Shiffrin, R. M. (1968). Human memory: A proposed system and its control processes. In K. W. Spence & J. T. Spence (Eds.), *The Psychology of Learning and Motivation*, Vol. 2, 89–195. Academic Press. [DOI 10.1016/S0079-7421(08)60455-7](https://doi.org/10.1016/S0079-7421(08)60455-7)
- Tulving, E. (1972). Episodic and semantic memory. In E. Tulving & W. Donaldson (Eds.), *Organization of Memory*, 381–403. Academic Press.
- Tulving, E. (1985). Memory and consciousness. *Canadian Psychology*, 26(1), 1–12. [DOI 10.1037/h0080017](https://doi.org/10.1037/h0080017)
- Tulving, E. (1995). Organization of memory: Quo vadis? In M. S. Gazzaniga (Ed.), *The Cognitive Neurosciences*, 839–847. MIT Press.
- Craik, F. I. M., & Lockhart, R. S. (1972). Levels of processing: A framework for memory research. *Journal of Verbal Learning and Verbal Behavior*, 11(6), 671–684. [DOI 10.1016/S0022-5371(72)80001-X](https://doi.org/10.1016/S0022-5371(72)80001-X)
- Baddeley, A. D., & Hitch, G. (1974). Working memory. In G. H. Bower (Ed.), *The Psychology of Learning and Motivation*, Vol. 8, 47–89. Academic Press. [DOI 10.1016/S0079-7421(08)60452-1](https://doi.org/10.1016/S0079-7421(08)60452-1)
- Baddeley, A. (2000). The episodic buffer: a new component of working memory? *Trends in Cognitive Sciences*, 4(11), 417–423. [DOI 10.1016/S1364-6613(00)01538-2](https://doi.org/10.1016/S1364-6613(00)01538-2)
- Cowan, N. (2001). The magical number 4 in short-term memory: A reconsideration of mental storage capacity. *Behavioral and Brain Sciences*, 24(1), 87–114. [DOI 10.1017/S0140525X01003922](https://doi.org/10.1017/S0140525X01003922)
- Loftus, E. F., & Palmer, J. C. (1974). Reconstruction of automobile destruction: An example of the interaction between language and memory. *Journal of Verbal Learning and Verbal Behavior*, 13(5), 585–589. [DOI 10.1016/S0022-5371(74)80018-6](https://doi.org/10.1016/S0022-5371(74)80018-6)
- Squire, L. R. (2004). Memory systems of the brain: A brief history and current perspective. *Neurobiology of Learning and Memory*, 82(3), 171–177. [DOI 10.1016/j.nlm.2004.06.005](https://doi.org/10.1016/j.nlm.2004.06.005)
- Schacter, D. L., & Addis, D. R. (2007). The cognitive neuroscience of constructive memory: remembering the past and imagining the future. *Philosophical Transactions of the Royal Society B*, 362(1481), 773–786. [DOI 10.1098/rstb.2007.2087](https://doi.org/10.1098/rstb.2007.2087)

### 记忆的最优性与可用性

**给读者的提示：** Anderson & Schooler（1991）是整份研究库的支点。它证明了遗忘曲线可以从环境统计里推导出来——需求概率高的东西记得久。如果把这句话想清楚，agent 记忆里几乎所有"该不该写、该不该留"的争论都变成同一个问题：你对未来需求的估计对不对。Bjork & Bjork（1992）的存储强度/检索强度分离是第二重要的概念，因为它是唯一一个能把"记住了"和"想得起来"分开的框架。

- Anderson, J. R., & Schooler, L. J. (1991). Reflections of the environment in memory. *Psychological Science*, 2(6), 396–408. [DOI 10.1111/j.1467-9280.1991.tb00174.x](https://doi.org/10.1111/j.1467-9280.1991.tb00174.x)
- Bjork, R. A., & Bjork, E. L. (1992). A new theory of disuse and an old theory of stimulus fluctuation. In A. F. Healy, S. M. Kosslyn & R. M. Shiffrin (Eds.), *From Learning Processes to Cognitive Processes: Essays in Honor of William K. Estes*, Vol. 2, 35–67. Erlbaum.
- Anderson, J. R., Bothell, D., Byrne, M. D., Douglass, S., Lebiere, C., & Qin, Y. (2004). An integrated theory of the mind. *Psychological Review*, 111(4), 1036–1060. [DOI 10.1037/0033-295X.111.4.1036](https://doi.org/10.1037/0033-295X.111.4.1036)
- Anderson, J. R. (2007). *How Can the Human Mind Occur in the Physical Universe?* Oxford University Press.

### 遗忘作为一种机制

**给读者的提示：** 这一组是 AI 论文引用最多、也读得最浅的一组。Richards & Frankland（2017）和 Ryan & Frankland（2022）的核心不是"遗忘有时有用"，而是遗忘是**由生物机制主动执行**的，因此可以被设计。Fusi 等（2005）的级联模型值得单独读，因为它给出了一个可计算的答案：要同时保留旧记忆和学习新东西，突触必须有一系列时间常数，也就是必须有多档遗忘速度。这是"多时间尺度记忆"这个产品设计的理论出处。

- Fusi, S., Drew, P. J., & Abbott, L. F. (2005). Cascade models of synaptically stored memories. *Neuron*, 45(4), 599–611. [DOI 10.1016/j.neuron.2005.02.001](https://doi.org/10.1016/j.neuron.2005.02.001)
- Nørby, S. (2015). Why forget? On the adaptive value of memory loss. *Perspectives on Psychological Science*, 10(5), 551–578. [DOI 10.1177/1745691615596787](https://doi.org/10.1177/1745691615596787)
- Richards, B. A., & Frankland, P. W. (2017). The persistence and transience of memory. *Neuron*, 94(6), 1071–1084. [DOI 10.1016/j.neuron.2017.04.037](https://doi.org/10.1016/j.neuron.2017.04.037)
- Davis, R. L., & Zhong, Y. (2017). The biology of forgetting — A perspective. *Neuron*, 95(3), 490–503. [DOI 10.1016/j.neuron.2017.05.039](https://doi.org/10.1016/j.neuron.2017.05.039)
- Anderson, M. C., & Hulbert, J. C. (2021). Active forgetting: Adaptation of memory by prefrontal control. *Annual Review of Psychology*, 72, 1–36. [DOI 10.1146/annurev-psych-072720-094140](https://doi.org/10.1146/annurev-psych-072720-094140)
- Ryan, T. J., & Frankland, P. W. (2022). Forgetting as a form of adaptive engram cell plasticity. *Nature Reviews Neuroscience*, 23(3), 173–186. [DOI 10.1038/s41583-021-00548-3](https://doi.org/10.1038/s41583-021-00548-3)
- de Snoo, M. L., & Frankland, P. W. (2025). Neurobiological mechanisms of forgetting across timescales. *Current Opinion in Neurobiology*, 90, 102972. [DOI 10.1016/j.conb.2025.102972](https://doi.org/10.1016/j.conb.2025.102972)

### 遗忘的计算模型

- Yalnizyan-Carson, A., & Richards, B. A. (2022). Forgetting enhances episodic control with structured memories. *Frontiers in Computational Neuroscience*, 16, 757244. [DOI 10.3389/fncom.2022.757244](https://doi.org/10.3389/fncom.2022.757244)
- Tran, L. M., Santoro, A., Liu, L., Josselyn, S. A., Richards, B. A., & Frankland, P. W. (2022). Adult neurogenesis acts as a neural regularizer. *PNAS*, 119(45), e2206704119. [DOI 10.1073/pnas.2206704119](https://doi.org/10.1073/pnas.2206704119)

### 痕迹与神经基质

**给读者的提示：** 海马索引理论（Teyler & DiScenna, 1986）是整个仓库里被借用得最实用的一条。它说：海马不存内容，存地址。这条结构在工程上对应"记忆条目只存引用、内容存在别处"，于是删除、隔离、权限都变简单——[06-security.zh-CN.md](06-security.zh-CN.md) 第 6 节把这个结构当成隐私方案来用。Complementary Learning Systems（McClelland 等，1995）是另一条：快学和慢学必须两套系统，因为一套做不到又快又稳。所有"短期上下文 + 长期存储"的架构都是它的工程版本。

- Teyler, T. J., & DiScenna, P. (1986). The hippocampal memory indexing theory. *Behavioral Neuroscience*, 100(2), 147–154. [DOI 10.1037/0735-7044.100.2.147](https://doi.org/10.1037/0735-7044.100.2.147)
- Teyler, T. J., & Rudy, J. W. (2007). The hippocampal indexing theory and episodic memory: Updating the index. *Hippocampus*, 17(12), 1158–1169. [DOI 10.1002/hipo.20350](https://doi.org/10.1002/hipo.20350)
- McClelland, J. L., McNaughton, B. L., & O'Reilly, R. C. (1995). Why there are complementary learning systems in the hippocampus and neocortex. *Psychological Review*, 102(3), 419–457. [DOI 10.1037/0033-295X.102.3.419](https://doi.org/10.1037/0033-295X.102.3.419)
- Nader, K., Schafe, G. E., & LeDoux, J. E. (2000). Fear memories require protein synthesis in the amygdala for reconsolidation after retrieval. *Nature*, 406(6797), 722–726. [DOI 10.1038/35021052](https://doi.org/10.1038/35021052)
- Liu, X., Ramirez, S., Pang, P. T., Puryear, C. B., Govindarajan, A., Deisseroth, K., & Tonegawa, S. (2012). Optogenetic stimulation of a hippocampal engram activates fear memory recall. *Nature*, 484(7394), 381–385. [DOI 10.1038/nature11028](https://doi.org/10.1038/nature11028)
- Kumaran, D., Hassabis, D., & McClelland, J. L. (2016). What learning systems do intelligent agents need? Complementary learning systems theory updated. *Trends in Cognitive Sciences*, 20(7), 512–534. [DOI 10.1016/j.tics.2016.05.004](https://doi.org/10.1016/j.tics.2016.05.004)
- Kitamura, T., Ogawa, S. K., Roy, D. S., Okuyama, T., Morrissey, M. D., Smith, L. M., … Tonegawa, S. (2017). Engrams and circuits crucial for systems consolidation of a memory. *Science*, 356(6333), 73–78. [DOI 10.1126/science.aam6808](https://doi.org/10.1126/science.aam6808)
- Roy, D. S., Muralidhar, S., Smith, L. M., & Tonegawa, S. (2017). Silent memory engrams as the basis for retrograde amnesia. *PNAS*, 114(46), E9972–E9979. [DOI 10.1073/pnas.1714248114](https://doi.org/10.1073/pnas.1714248114)
- Josselyn, S. A., & Tonegawa, S. (2020). Memory engrams: Recalling the past and imagining the future. *Science*, 367(6473), eaaw4325. [DOI 10.1126/science.aaw4325](https://doi.org/10.1126/science.aaw4325)

### 睡眠、巩固与调制

**给读者的提示：** 这一组是"后台整合"（sleep-time compute、dreaming、记忆重写）的理论出处。要点是：巩固不是一个存储操作，是一个**计算**操作——它比较、抽象、去重、并决定哪些细节可以丢掉。所以"睡眠"不只是把数据搬个地方，而是重写。线上的产品大多只做了搬运的那一半。

- Walker, M. P., & Stickgold, R. (2006). Sleep, memory, and plasticity. *Annual Review of Psychology*, 57, 139–166. [DOI 10.1146/annurev.psych.56.091103.070307](https://doi.org/10.1146/annurev.psych.56.091103.070307)
- Diekelmann, S., & Born, J. (2010). The memory function of sleep. *Nature Reviews Neuroscience*, 11(2), 114–126. [DOI 10.1038/nrn2762](https://doi.org/10.1038/nrn2762)
- Walker, M. P., & Stickgold, R. (2010). Overnight alchemy: sleep-dependent memory evolution. *Nature Reviews Neuroscience*, 11(3), 218. [链接](https://www.nature.com/articles/nrn2762-c1)
- Lisman, J., Grace, A. A., & Düzel, E. (2011). A neoHebbian framework for episodic memory; role of dopamine-dependent late LTP. *Trends in Neurosciences*, 34(10), 536–547. [DOI 10.1016/j.tins.2011.07.006](https://doi.org/10.1016/j.tins.2011.07.006)
- Shohamy, D., & Adcock, R. A. (2010). Dopamine and adaptive memory. *Trends in Cognitive Sciences*, 14(10), 464–472. [DOI 10.1016/j.tics.2010.08.002](https://doi.org/10.1016/j.tics.2010.08.002)
- de Quervain, D. J.-F., Aerni, A., Schelling, G., & Roozendaal, B. (2009). Glucocorticoids and the regulation of memory in health and disease. *Frontiers in Neuroendocrinology*, 30(3), 358–370. [DOI 10.1016/j.yfrne.2009.03.002](https://doi.org/10.1016/j.yfrne.2009.03.002)
- Lupien, S. J., McEwen, B. S., Gunnar, M. R., & Heim, C. (2009). Effects of stress throughout the lifespan on the brain, behaviour and cognition. *Nature Reviews Neuroscience*, 10(6), 434–445. [DOI 10.1038/nrn2639](https://doi.org/10.1038/nrn2639)
- Rasch, B., & Born, J. (2013). About sleep's role in memory. *Physiological Reviews*, 93(2), 681–766. [DOI 10.1152/physrev.00032.2012](https://doi.org/10.1152/physrev.00032.2012)
- Tononi, G., & Cirelli, C. (2014). Sleep and the price of plasticity: From synaptic and cellular homeostasis to memory consolidation and integration. *Neuron*, 81(1), 12–34. [DOI 10.1016/j.neuron.2013.12.025](https://doi.org/10.1016/j.neuron.2013.12.025)

---

## 2. 认知架构

用于 [02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md)。

**给读者的提示：** 这一组文献的价值不在结论，在**形式**。ACT-R 把"该取哪条记忆"写成一个可计算的公式；SOAR 把"学新东西"写成由失败触发的建块；全局工作台把"什么进入意识"写成竞争。三个都是"记忆如何参与推理"的可执行模型，而今天的 agent 记忆系统一个都没有——它们只有存储和检索。这是 [02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md) 那条"编译器"论点的文献基础。

- Laird, J. E., Newell, A., & Rosenbloom, P. S. (1987). SOAR: An architecture for general intelligence. *Artificial Intelligence*, 33(1), 1–64. [DOI 10.1016/0004-3702(87)90050-7](https://doi.org/10.1016/0004-3702(87)90050-7)
- Baars, B. J. (1988). *A Cognitive Theory of Consciousness*. Cambridge University Press.
- Dehaene, S., Kerszberg, M., & Changeux, J.-P. (1998). A neuronal model of a global workspace in effortful cognitive tasks. *PNAS*, 95(24), 14529–14534. [DOI 10.1073/pnas.95.24.14529](https://doi.org/10.1073/pnas.95.24.14529)
- Dehaene, S. (2014). *Consciousness and the Brain: Deciphering How the Brain Codes Our Thoughts*. Viking.
- Laird, J. E. (2012). *The Soar Cognitive Architecture*. MIT Press.
- Nuxoll, A. M., & Laird, J. E. (2007). Extending Soar with dissociated symbolic memories. *Advances in Cognitive Systems*.
- Oberauer, K., & Hein, L. (2012). Attention to information in working memory. *Current Directions in Psychological Science*, 21(3), 164–169. [DOI 10.1177/0963721412444727](https://doi.org/10.1177/0963721412444727)

### 带外部记忆的网络

- Graves, A., Wayne, G., & Danihelka, I. (2014). Neural Turing Machines. [arXiv:1410.5401](https://arxiv.org/abs/1410.5401)
- Weston, J., Chopra, S., & Bordes, A. (2015). Memory networks. [arXiv:1410.3916](https://arxiv.org/abs/1410.3916)
- Sukhbaatar, S., Weston, J., & Fergus, R. (2015). End-to-end memory networks. [arXiv:1503.08895](https://arxiv.org/abs/1503.08895)
- Graves, A., Wayne, G., Reynolds, M., Harley, T., Danihelka, I., Grabska-Barwińska, A., … Hassabis, D. (2016). Hybrid computing using a neural network with dynamic external memory. *Nature*, 538(7626), 471–476. [DOI 10.1038/nature20101](https://doi.org/10.1038/nature20101)

### 情景控制

- Lengyel, M., & Dayan, P. (2007). Hippocampal contributions to control: The third way. *Advances in Neural Information Processing Systems* 20.
- Blundell, C., Uria, B., Pritzel, A., Li, Y., Ruderman, A., Leibo, J. Z., … Hassabis, D. (2016). Model-free episodic control. [arXiv:1606.04460](https://arxiv.org/abs/1606.04460)
- Pritzel, A., Uria, B., Srinivasan, S., Puigdomènech, A., Vinyals, O., Hassabis, D., … Blundell, C. (2017). Neural episodic control. [arXiv:1703.01988](https://arxiv.org/abs/1703.01988)

---

## 3. 研究架构

用于 [04-research-architectures.zh-CN.md](04-research-architectures.zh-CN.md)。

**给读者的提示：** 按年份读而不是按主题读，会看到一条清楚的线：2023 年在解决"怎么塞进上下文"，2024 年在解决"怎么取对"并且第一次有了能证伪的基准，2025 年在解决"写的时候就要处理好看"，2026 年在问"这些记忆到底有没有用"。基准那一组的价值在于它们大多是负面结果——这比正面结果更能说明领域到了哪一步。

### 2023

- Packer, C., Wooders, S., Lin, K., Fang, V., Patil, S. G., Stoica, I., & Gonzalez, J. E. (2023). MemGPT: Towards LLMs as operating systems. [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- Zhong, W., Guo, L., Gao, Q., Ye, H., & Wang, Y. (2023). MemoryBank: Enhancing large language models with long-term memory. [arXiv:2305.10250](https://arxiv.org/abs/2305.10250)
- Park, J. S., O'Brien, J. C., Cai, C. J., Morris, M. R., Liang, P., & Bernstein, M. S. (2023). Generative Agents: Interactive simulacra of human behavior. [arXiv:2304.03442](https://arxiv.org/abs/2304.03442)
- Shinn, N., Cassano, F., Berman, E., Gopinath, A., Narasimhan, K., & Yao, S. (2023). Reflexion: Language agents with verbal reinforcement learning. [arXiv:2303.11366](https://arxiv.org/abs/2303.11366)
- Wang, G., Xie, Y., Jiang, Y., Mandlekar, A., Xiao, C., Zhu, Y., Fan, L., & Anandkumar, A. (2023). Voyager: An open-ended embodied agent with large language models. [arXiv:2305.16291](https://arxiv.org/abs/2305.16291)

### 2024

- Gutiérrez, B. J., Shu, Y., Gu, Y., Yasunaga, M., & Su, Y. (2024). HippoRAG: Neurobiologically inspired long-term memory for large language models. [arXiv:2405.14831](https://arxiv.org/abs/2405.14831)
- Maharana, A., Lee, D.-H., Tulyakov, S., Bansal, M., Barbieri, F., & Fang, Y. (2024). Evaluating very long-term conversational memory of LLM agents. [arXiv:2402.17753](https://arxiv.org/abs/2402.17753)
- Wu, D., Wang, H., Yu, W., Zhang, Y., Chang, K.-W., & Yu, D. (2024). LongMemEval: Benchmarking chat assistants on long-term interactive memory. [arXiv:2410.10813](https://arxiv.org/abs/2410.10813)
- Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., & Liang, P. (2024). Lost in the middle: How language models use long contexts. *TACL*, 12, 157–173. [arXiv:2307.03172](https://arxiv.org/abs/2307.03172)
- Hsieh, C.-P., Sun, S., Kriman, S., Acharya, S., Rekesh, D., Jia, F., & Ginsburg, B. (2024). RULER: What's the real context size of your long-context language models? [arXiv:2404.06654](https://arxiv.org/abs/2404.06654)

### 2025

- Xu, W., Liang, Z., Mei, K., Gao, H., Tan, J., & Zhang, Y. (2025). A-MEM: Agentic memory for LLM agents. [arXiv:2502.12110](https://arxiv.org/abs/2502.12110). NeurIPS 2025.
- Chhikara, P., Khant, D., Aryan, S., Singh, T., & Yadav, D. (2025). Mem0: Building production-ready AI agents with scalable long-term memory. [arXiv:2504.19413](https://arxiv.org/abs/2504.19413). ECAI 2025.
- Rasmussen, P., Paliychuk, P., Beauvais, M., Ryan, J., & Chalef, D. (2025). Zep: A temporal knowledge graph architecture for agent memory. [arXiv:2501.13956](https://arxiv.org/abs/2501.13956)
- Gutiérrez, B. J., Shu, Y., Qi, W., Zhou, S., & Su, Y. (2025). From RAG to memory: Non-parametric continual learning for large language models. [arXiv:2502.14802](https://arxiv.org/abs/2502.14802). ICML 2025.
- Kang, J., Ji, M., Zhao, Z., & Bai, T. (2025). MemoryOS of AI agent. [arXiv:2506.06326](https://arxiv.org/abs/2506.06326). EMNLP 2025.
- Li, Z., Song, S., Xi, C., Wang, H., et al. (2025). MemOS: A memory OS for AI system. [arXiv:2507.03724](https://arxiv.org/abs/2507.03724)
- Salama, R., Cai, J., Yuan, M., Currey, A., Sunkara, M., Zhang, Y., & Benajiba, Y. (2025). MemInsight: Autonomous memory augmentation for LLM agents. [arXiv:2503.21760](https://arxiv.org/abs/2503.21760). EMNLP 2025.
- Xu, W., et al. (2025). MemGAS: Multi-granularity memory with entropy-based routing. [arXiv:2505.19549](https://arxiv.org/abs/2505.19549)
- Lin, K., Snell, J., Wang, Y., Packer, C., Wooders, S., Stoica, I., & Gonzalez, J. E. (2025). Sleep-time compute: Beyond inference scaling at test-time. [arXiv:2504.13171](https://arxiv.org/abs/2504.13171)
- Zhang, Q., Hu, C., Upasani, S., Ma, B., Hong, F., Kamanuru, V., et al. (2025). Agentic context engineering: Evolving contexts for self-improving language models. [arXiv:2510.04618](https://arxiv.org/abs/2510.04618)

### 2026 基准与分析

- MemoryAgentBench. [arXiv:2507.05257](https://arxiv.org/abs/2507.05257)
- StructMemEval. [arXiv:2602.11243](https://arxiv.org/abs/2602.11243)
- STALE. [arXiv:2605.06527](https://arxiv.org/abs/2605.06527)
- Memora / FAMA. [arXiv:2604.20006](https://arxiv.org/abs/2604.20006)
- AMA-Bench. [arXiv:2602.22769](https://arxiv.org/abs/2602.22769)
- MemGym. [arXiv:2605.20833](https://arxiv.org/abs/2605.20833)
- MINTEval. [arXiv:2605.18565](https://arxiv.org/abs/2605.18565)
- Library Drift. [arXiv:2605.19576](https://arxiv.org/abs/2605.19576)
- Retain or Consolidate? [arXiv:2607.17545](https://arxiv.org/abs/2607.17545)
- The Epistemics of Agent Memory. [arXiv:2609.33013](https://arxiv.org/abs/2609.33013)

---

## 4. 安全

用于 [06-security.zh-CN.md](06-security.zh-CN.md)。

**给读者的提示：** MINJA 是这一类攻击的定名之作，MPBench 是唯一一个系统性地测了"哪些防御管用"的工作，Ghost Vectors 是唯一一个把"删除没删干净"量化的工作。三篇合起来构成安全这一章的全部实证基础——这也是为什么那一章里负面结果比正面结果多。

- Dong, S., Xu, S., He, P., Li, Y., Tang, J., Liu, T., Liu, H., & Li, J. (2025). Memory injection attacks on LLM agents via query-only interaction (MINJA). [arXiv:2503.03704](https://arxiv.org/abs/2503.03704)
- MPBench. [arXiv:2606.04329](https://arxiv.org/abs/2606.04329)
- Ghost Vectors. [arXiv:2606.18497](https://arxiv.org/abs/2606.18497)

---

## 5. 基础设施

用于 [05-infrastructure.zh-CN.md](05-infrastructure.zh-CN.md)。

**给读者的提示：** 这一节只有五条，因为基础设施的知识大多在文档而不是论文里。Malkov & Yashunin 的 HNSW 原文值得读一遍参数定义，因为几乎所有产品的默认值都直接来自它；Cormack 等的 RRF 是混合检索的唯一标准做法，理解它的 k=60 为什么这么设，就能理解它为什么会丢掉分数幅度。

- Malkov, Y. A., & Yashunin, D. A. (2020). Efficient and robust approximate nearest neighbor search using Hierarchical Navigable Small World graphs. *IEEE TPAMI*, 42(4), 824–836. [DOI 10.1109/TPAMI.2018.2889473](https://doi.org/10.1109/TPAMI.2018.2889473)
- Subramanya, S. J., Devvrit, F., Simhadri, H. V., Krishnaswamy, R., & Kadekodi, R. (2019). DiskANN: Fast accurate billion-point nearest neighbor search on a single node. *NeurIPS 32*.
- Cormack, G. V., Clarke, C. L. A., & Büttcher, S. (2009). Reciprocal rank fusion outperforms Condorcet and individual rank learning methods. *SIGIR 2009*, 758–759. [DOI 10.1145/1571941.1572114](https://doi.org/10.1145/1571941.1572114)
- pgvector. [github.com/pgvector/pgvector](https://github.com/pgvector/pgvector)
- SQLite FTS5. [sqlite.org/fts5.html](https://sqlite.org/fts5.html)
- Qdrant filtering. [qdrant.tech/documentation/concepts/filtering](https://qdrant.tech/documentation/concepts/filtering/)

---

## 6. 标准与协议

用于 [05-infrastructure.zh-CN.md](05-infrastructure.zh-CN.md) 第 9 节。

**给读者的提示：** 这四个的现状本身就是结论。MCP 在 2026-07-28 的修订里明确走向无状态，等于关上了"标准记忆接口"的门；A2A 明确声明不共享记忆；AGENTS.md 只标准化了指令文件，没有标准化记忆；Agent Memory Repo 还只是一个 spec。也就是说：**2026 年没有记忆的互操作标准**，而这是仓库里所有"import/export 靠各家自己实现"现象的原因。

- Model Context Protocol specification. [modelcontextprotocol.io](https://modelcontextprotocol.io)
- Agent2Agent (A2A) protocol. [a2a-protocol.org](https://a2a-protocol.org)
- AGENTS.md. [agents.md](https://agents.md)
- Agent Memory Repo spec. [github.com/AgentMemoryRepo/agentmemoryrepo](https://github.com/AgentMemoryRepo/agentmemoryrepo)

---

## 7. 书籍

用于 [01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 第 5 节的阅读清单。

**给读者的提示：** 如果只读一本，读 Budson & Kensinger——它是唯一一本把"遗忘为什么有用"讲清楚、而且作者自己就是临床神经心理学家和记忆研究者的书。如果关心"记忆可以被训练到什么程度"，Foer 是最好读的；如果关心记忆作为文化技术的历史，Yates 的 *The Art of Memory* 无可替代，它也是"外部记忆系统"这个概念最早的谱系。

- Baddeley, A., Eysenck, M. W., & Anderson, M. C. (2020/2025). *Memory* (3rd/4th ed.). Routledge.
- Budson, A. E., & Kensinger, E. A. (2023). *Why We Forget and How to Remember Better: The Science Behind Memory*. Oxford University Press.
- Dehaene, S. (2020). *How We Learn: Why Brains Learn Better Than Any Machine… for Now*. Viking.
- Draaisma, D. (2015). *Forgetting: Myths, Perils and Compensations*. Yale University Press.
- Foer, J. (2011). *Moonwalking with Einstein: The Art and Science of Remembering Everything*. Penguin Press.
- Foster, J. K. (2009). *Memory: A Very Short Introduction*. Oxford University Press.
- Genova, L. (2021). *Remember: The Science of Memory and the Art of Forgetting*. Harmony.
- Small, S. A. (2021). *Forgetting: The Benefits of Not Remembering*. Crown.
- Yates, F. A. (1966). *The Art of Memory*. University of Chicago Press.

---

## 8. 产品文档

19 个产品，约 45 个文档页面，读取于 2026-11-01。完整 URL 列在 [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) 末尾，每一条都附着在它支撑的那个论断旁边。这里不重复，因为它们变动频繁，而分开之后就失去了"哪句话来自哪一页"的对应关系。

**关于链接腐烂。** 这次调研期间有四个文档面移动或消失：Windsurf 的记忆文档（并入 Devin）、Roo Code 的记忆文档（整体撤下）、OpenAI 帮助中心（迁到 `learn.chatgpt.com`）、Gemini CLI 的文档主机。引用旧 URL 的地方都会断。凡是完全取不到官方文档的产品，一律从表里移除，而不是用第三方文章重建——见 [03-agent-native-memory.zh-CN.md](03-agent-native-memory.zh-CN.md) 第 8 节。

---

## 9. 常见错误引用（中文摘要）

完整的十项对照与说明在 [sources-corrections.md](sources-corrections.md)。这里给出最容易踩的四个：

**"Anderson & Hulbert (2021), Active forgetting"** —— 副标题 *Adaptation of memory by prefrontal control* 是论点本身。只引主标题的论文，往往把它描述成"被动衰减"，那是反面。

**"Richards & Frankland (2017), TiCS"** —— 是 *Neuron* 94(6), 1071–1084。同期的 Davis & Zhong (2017, *Neuron* 95(3)) 常被与它混淆。

**"Ramirez et al. (2012), Nature"** —— 不存在。2012 年的 *Nature* 论文是 Liu 等（光遗传激活痕迹）；Ramirez 等是 2013 年 *Science* 的假记忆植入。

**"Kensinger & Budson (2023)"** —— 顺序反了，是 Budson & Kensinger。

这一页的一个原则：**所有 arXiv ID 在 2026-11-01 核对过；所有期刊条目给到卷、期、页；凡是不确定会议收录的，只给 arXiv 形式，不猜。**

---

*回到 [sources.md](sources.md) · [README.zh-CN.md](README.zh-CN.md)*
