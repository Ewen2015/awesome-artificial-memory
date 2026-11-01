# 人的记忆与遗忘：原始文献基座

**研究日期：** 2026-11-01

今天绝大多数 AI 记忆系统，都是对人类记忆的一次朴素重新推导。这份文档把一手文献集中起来，目的是让这种推导变成**有意识的**，而不是无意识的——同时也让那些与五十年实证证据相冲突的设计，能被识别为冲突。

**怎么用这份文档。** 第 1–4 节是证据。第 5 节是书单。第 6 节把每个生物机制映射到最接近的 AI 对应物，更重要的是说清楚**类比在哪里断掉**。第 7 节列 AI 从业者最常搞错的十二件事。

如果只读一节，读第 7 节。

---

## 1. 经典基础

### Ebbinghaus（1885 / 英译 1913）

Ebbinghaus, H. (1885). *Über das Gedächtnis: Untersuchungen zur experimentellen
Psychologie*. Leipzig: Duncker & Humblot.
英译：*Memory: A Contribution to Experimental Psychology*（Ruger & Bussenius 译，
1913）。DOI [10.1037/10011-000](https://doi.org/10.1037/10011-000)。

他用无意义音节做材料，用**节省法**测量保持——测的不是"能回忆多少"，而是**重新学习省下多少时间**。保持率的形状是先陡降后趋平：约 20 分钟 58%，1 小时 44%，1 天 33%，31 天 21%（不同复现版本的数值略有出入，引用时用"约"）。

两件事重要。第一，这条曲线让遗忘变得**可量化**，并且证明损失是前重后轻、渐近的，不是线性的。第二，节省法测到的是被试**说不出**的残留——AI 系统没有这种区分，因为它要么检索到要么没有。

每个记忆系统里的每条衰减或近因函数，都是这条曲线的后裔。而大家忘掉的细节是：**尾巴很长**。这才是"保留一份压缩残留"而不是直接删除的经验依据。

### Müller & Pilzecker（1900）

*Experimentelle Beiträge zur Lehre vom Gedächtniss*. Leipzig: J. A. Barth.

他们证明了**倒摄抑制**：紧接在清单之后学习的内容会损害对清单的保持，插入材料来得越早，损害越大。由此推断出**持续兴奋**（perseveration）——痕迹在编码后仍在一段时间内反复回响并巩固，这段时间里它是脆弱的。

这是巩固理论的直系祖先，也是每条写入流水线都会重新发现的原理的第一次表述：**编码之后发生了什么，决定什么能活下来。** 写入缓冲不是优化，它就是机制本身。

### Bartlett（1932）

*Remembering: A Study in Experimental and Social Psychology*. Cambridge
University Press.

记忆是**重构**，由图式引导。回忆会用先验知识填补空缺，而不是回放记录。经典结果：英国被试在复述一个北美原住民民间故事时，会逐渐把它"正常化"成自己文化的预期，删掉并改造不合的部分。

这是"完美记录是错误目标"最有力的论据。LLM agent 的检索路径**永远**是生成式的——所以它已经有重构记忆了。问题只剩一个：它有没有把"这是重构"标出来。

### Atkinson & Shiffrin（1968）

*Human memory: A proposed system and its control processes*. In Spence &
Spence (Eds.), *The Psychology of Learning and Motivation*, Vol. 2, 89–195.
DOI [10.1016/S0022-5371(68)80002-7](https://doi.org/10.1016/S0022-5371(68)80002-7)。

模态模型：**感觉登记**、**短时存储**（有限，工作台）、**长时存储**（实际上无限）。持久的贡献是把**结构特征**（固定架构）和**控制过程**（复述、编码、检索策略）分开。

直接照搬过来：大而慢的存储前面放一个小而快的有损缓冲，这是*架构*选择；真正有意思的行为全在**控制策略**里。而这恰恰是 2023 年那批 agent 记忆失败的地方——它们建了存储，把策略留成了隐含的。

### Tulving（1972, 1985, 1995）

Tulving, E. (1972). Episodic and semantic memory. In Tulving & Donaldson (Eds.),
*Organization of Memory*, 381–403.

**情景记忆**：带有时间—空间情境的个人经历。**语义记忆**：脱离情境的结构化知识。这个区分是**功能性的，不是解剖性的**——它问的是检索时是重新实例化了一个事件，还是提取出一条概括。

Tulving, E. (1985). Memory and consciousness. *Canadian Psychology*, 26(1),
1–12. DOI [10.1037/h0080017](https://doi.org/10.1037/h0080017)。把每个系统和一种意识形式配对：程序性 = **无觉知**（anoetic），语义 = **知晓**（noetic），情景 = **自传体觉知**（autonoetic，即心理时间旅行）。remember/know 范式出自这里。

Tulving, E. (1995). Organization of memory: Quo vadis? In Gazzaniga (Ed.),
*The Cognitive Neurosciences*, 839–847. MIT Press. 给出 **SPI**：编码是**串行**的（情景编码要穿过语义），存储是**并行**的，检索是**独立**的。

SPI 是一条能直接用的设计规则：**写入时耦合，读取时解耦。** 它同时也是一个提醒——"每种记忆配一个存储"不是生物的做法。

Tulving 这个切分对 AI 设计最有用，原因很直接：大多数 agent 的"记忆"既不是情景的也不是语义的，而是一份只能追加的日志——没有情景索引（时间、地点、agent 标签），也没有语义蒸馏。

### Baddeley & Hitch（1974），Baddeley（2000）

Baddeley & Hitch (1974), *Working memory*, in *Psychology of Learning and
Motivation* Vol. 8, 47–89.
DOI [10.1016/S0022-5371(74)80011-3](https://doi.org/10.1016/S0022-5371(74)80011-3)。

工作记忆不是一个缓冲：**语音回路**、**视空间画板**、**中央执行**。干扰是分域的。

Baddeley (2000). The episodic buffer: a new component of working memory?
*Trends in Cognitive Sciences*, 4(11), 417–423.
DOI [10.1016/S1364-6613(00)01538-2](https://doi.org/10.1016/S1364-6613(00)01538-2)。

**情景缓冲器**把各子系统的信息以及长时记忆的信息**绑定**成单一情景。Baddeley 自己总结这次修订换来了什么，那句话最要紧：真正的进展是"把注意力放在**整合**信息的过程上，而不是放在各子系统的隔离上"。

这是生物记忆与当前 AI 记忆之间最大的真实缺口。RAG 流水线检索片段；它们不把检索到的片段、当前上下文、任务状态**绑定**成一个连贯表征。

### Squire（2004）

*Memory systems of the brain: A brief history and current perspective*.
*Neurobiology of Learning and Memory*, 82(3), 171–177.
DOI [10.1016/j.nlm.2004.06.005](https://doi.org/10.1016/j.nlm.2004.06.005)。

**陈述性**（情景 + 语义；内侧颞叶）对**非陈述性**（程序性技能、启动、条件化；纹状体、新皮层、杏仁核、小脑）。Squire 的表述明确反对二分法：重点是*多个分离的系统*，每个有自己的运行特性。

对 AI 的推论：记忆**不是一种基底**。权重、检索索引、技能/策略是不同的系统，更新规则不同，遗忘曲线也不同。把它们压缩成"记忆模块"，是范畴错误。

### Craik & Lockhart（1972）

*Levels of processing: A framework for memory research*. *Journal of Verbal
Learning and Verbal Behavior*, 11(6), 671–684.
DOI [10.1016/S0022-5371(72)80001-X](https://doi.org/10.1016/S0022-5371(72)80001-X)。

持久性取决于编码时的**加工深度**。浅层知觉加工留下脆弱的痕迹；语义的、精细化的加工留下持久的痕迹。痕迹是**加工过程的副产品**。

这是反对"全都写进来，以后再检索"最有力的论据：*怎么编码*决定*能否存活*，与曝光次数无关。换成 AI 的说法——写入函数做什么（摘要？建链？抽实体？打来源戳？）比存储容量更重要。而且与人不同，AI 可以廉价地反复重编码，这是它独有的杠杆。

### Anderson & Schooler（1991）——枢纽论文

*Reflections of the environment in memory*. *Psychological Science*, 2(6),
396–408. DOI
[10.1111/j.1467-9280.1991.tb00174.x](https://doi.org/10.1111/j.1467-9280.1991.tb00174.x)。

他们分析了真实环境——《纽约时报》、父母对孩子的说话、一位研究者的邮件——发现**某条信息以后会被再次需要的概率**，与**频率、近因、间隔**的关系，和人类回忆率与这三个量的关系遵循同一条规律。

所以记忆的可及性不是随意的衰减，而是**对未来需求的统计估计**，并且被环境的统计规律调校过。在 ACT-R 里，这就是追踪 log 需求几率的基础激活值。

这是这个 repo 最核心的一条引用。它把"遗忘是特性"从口号变成了可检验的最优性主张：**如果你的检索分数不是在估计 P(现在需要这条 | 历史)，你就没在做记忆这件事。**

### Bjork & Bjork（1992）

*A new theory of disuse and an old theory of stimulus fluctuation*. In Healy,
Kosslyn & Shiffrin (Eds.), *Essays in Honor of William K. Estes*, Vol. 2, 35–67.

两个独立的强度：**存储强度**（学得多牢；从不下降）与**提取强度**（当前可及性；会波动，容量受限）。提取会同时提高两者，且在存储强度高、提取强度低时，对提取强度的提升最大——间隔效应和测验效应的机制就在这里。

这是"我们还留着"和"我们现在能拿到"最干净的分离。agent 记忆正需要这个区分：*存在存储里* 对 *在这个查询下可检索*。生物上提取强度随**使用**移动；大多数向量库里它只随时间移动。

---

## 2. 遗忘作为主动机制

### Anderson & Hulbert（2021）

*Active forgetting: Adaptation of memory by prefrontal control*. *Annual Review
of Psychology*, 72, 1–36. DOI
[10.1146/annurev-psych-072720-094140](https://doi.org/10.1146/annurev-psych-072720-094140)。

回顾了一个世纪关于"被动衰减还是主动抑制"的争论，结论站在**主动**这边：前额叶对海马的记忆活动施加上而下的、常常是抑制性的控制，压制特定痕迹。机制是**目标导向的压制**，不是侵蚀。

这是对"存储加垃圾回收"模型最有力的反驳：遗忘是一个*由控制器执行的*操作。一个只会用 TTL 过期的系统，只实现了两部分机制中的被动那一半。

### Richards & Frankland（2017）——论点论文

*The persistence and transience of memory*. *Neuron*, 94(6), 1071–1084.
DOI [10.1016/j.neuron.2017.04.037](https://doi.org/10.1016/j.neuron.2017.04.037)。

> "记忆的目的不是把信息传过时间本身。记忆的目的是**优化决策**。因此，短暂与持久同等重要。"

他们把生物上的短暂性——突触弱化与消除、海马神经发生造成的覆写——映射到机器学习的**正则化**：遗忘细节防止**过拟合**于特定过往事件，改善泛化与行为灵活性。

这是把那层 AI 类比摆到同行评审场合的论文，也点明了共同的失败模式是过拟合。

### Davis & Zhong（2017）

*The biology of forgetting — A perspective*. *Neuron*, 95(3), 490–503.
DOI [10.1016/j.neuron.2017.05.039](https://doi.org/10.1016/j.neuron.2017.05.039)。

三大类：**神经发生型**、**干扰型**、**内在型**。刻画最清楚的内在通路是果蝇的"遗忘细胞"向印迹细胞释放**多巴胺**，驱动 Rac1/Cofilin 级联重塑肌动蛋白骨架。

最有挑战性的主张：**内在遗忘可能是默认状态**，一直在与巩固竞争、不断擦除记忆。擦除是基线，记住是主动支出——这是"遗忘是特性"能拿到的最锋利表述。

### Ryan & Frankland（2022）

*Forgetting as a form of adaptive engram cell plasticity*. *Nature Reviews
Neuroscience*, 23(3), 173–186.
DOI [10.1038/s41583-021-00548-3](https://doi.org/10.1038/s41583-021-00548-3)。

记忆存在**印迹细胞群**里，回忆就是再激活。遗忘不是删除，而是**回路重塑把印迹细胞从可及状态移到不可及状态**。（Ryan 自己的说法：记忆还在保险箱里，只是你忘了密码。）

**生物遗忘的主要形态是丢失索引/访问路径，不是丢失载荷。** 删行的向量库建模的是罕见情况。工程推论立刻就跟上了:墓碑记录、过期索引、还在但取不到的行，值得和"淘汰"同等分量的设计关注。

### Nørby（2015）

*Why forget? On the adaptive value of memory loss*. *Perspectives on
Psychological Science*, 10(5), 551–578.
DOI [10.1177/1745691615596787](https://doi.org/10.1177/1745691615596787)。

三种适应性功能：**情绪调节**、**知识获取**（抽象化与自动化要求丢弃实例）、**情境适配**（让知识保持当前有效）。

第二条是 AI 最需要的：**抽象要求丢弃实例**。一个从不遗忘的 agent 无法形成概括，只能形成一张查找表。

### Fusi, Drew & Abbott（2005）

*Cascade models of synaptically stored memories*. *Neuron*, 45(4), 599–611.
DOI [10.1016/j.neuron.2005.02.001](https://doi.org/10.1016/j.neuron.2005.02.001)。

每个突触是**一串带梯度可塑性的状态**：一个权重被增强之后，后续的增强事件会把它推进学习率*更低*的状态。于是系统在多个时间尺度上都有动力学，产生**幂律遗忘**，兼顾高容量与长保持。

精确而少被用到的教训：**遗忘速率应该是权重自身历史的函数，不是墙上时钟的函数。** 长期稳定的权重应当比新写入的更难被覆写。这是直接可实现的，continual learning 里说的"元可塑性"重新推导的就是它。

### 计算层面的跟进

Yalnizyan-Carson, A., & Richards, B. A. (2022). *Forgetting enhances episodic
control with structured memories*. *Frontiers in Computational Neuroscience*,
16, 757244. DOI
[10.3389/fncom.2022.757244](https://doi.org/10.3389/fncom.2022.757244)。

一个在迷宫里用情节记忆缓存的 RL 智能体，可以忘掉**很大比例**的旧记忆而**不损失性能**——前提是它的表征携带结构信息——并且*一定程度的*遗忘**优于**无限记忆，因为它降低了过时信息和罕至状态的影响。

这是 Richards & Frankland 论点的计算证明，也是文献里最值得引用的一句话的来源：**大脑遗忘的量超过容量所要求的**，这只在遗忘本身在做事时才讲得通。

Tran, L. M., Santoro, A., Liu, L., Josselyn, S. A., Richards, B. A., &
Frankland, P. W. (2022). *Adult neurogenesis acts as a neural regularizer*.
*PNAS*, 119(45), e2206704119. DOI
[10.1073/pnas.2206704119](https://doi.org/10.1073/pnas.2206704119)。

把神经发生建模成**随机重置一小部分隐藏单元**（最优约 3.2%），在 CIFAR-10 上改善了泛化，效果与 dropout 相当，优于权重衰减和神经噪声——代价是*牺牲训练精度换取测试精度*。

对已学结构的局部破坏是一种正则化，而且带着具体数字。媒体上"突触修剪"的说法，是对这篇和 2017 年那篇综述的转述。

de Snoo, M. L., & Frankland, P. W. (2025). *Neurobiological mechanisms of
forgetting across timescales*. *Current Opinion in Neurobiology*, 90, 102972.
DOI [10.1016/j.conb.2025.102972](https://doi.org/10.1016/j.conb.2025.102972)。

目前最精确的表述：遗忘是**一族降低印迹可及性的机制**，作用在不同时间尺度上——短期（脑状态波动，逐刻改变可及性）与中期/长期（重塑印迹回路，使其逐渐更难被提取）。

---

## 3. 神经基底

### 印迹细胞

**Liu, X., Ramirez, S., Pang, P. T., Puryear, C. B., Govindarajan, A.,
Deisseroth, K., & Tonegawa, S. (2012).** Optogenetic stimulation of a
hippocampal engram activates fear memory recall. *Nature*, 484(7394), 381–385.
DOI [10.1038/nature11028](https://doi.org/10.1038/nature11028)。

把情境恐惧条件化时活跃的细胞用 ChR2 标记；**换一个情境**光遗传学再激活它们，动物照样僵住。这是一个**充分性**证明：细胞群不是记忆的相关物，它是记忆的物理把手。

**Roy, D. S., Muralidhar, S., Smith, L. M., & Tonegawa, S. (2017).** Silent
memory engrams as the basis for retrograde amnesia. *PNAS*, 114(46),
E9972–E9979. DOI
[10.1073/pnas.1714248114](https://doi.org/10.1073/pnas.1714248114)。

茴香霉素诱导健忘之后，光遗传学激活 DG 印迹仍然能在至少 8 天内唤起记忆；记忆还在，只是**沉默**——自然线索够不到，直接激活够得到。这是"**失忆 ≠ 擦除**"最干净的实验证明，也是可及性框架的实证支撑。

**Kitamura, T., Ogawa, S. K., Roy, D. S., Okuyama, T., Morrissey, M. D., Smith,
L. M., … Tonegawa, S. (2017).** Engrams and circuits crucial for systems
consolidation of a memory. *Science*, 356(6333), 73–78.
DOI [10.1126/science.aam6808](https://doi.org/10.1126/science.aam6808)。

海马与皮层的印迹按不同时间表成熟，随皮层印迹成熟，海马印迹细胞逐渐沉默——这是**快存储向慢存储迁移**的物理实现，同一条记忆同时存在于两处，可及性不同。

**Josselyn, S. A., & Tonegawa, S. (2020).** Memory engrams: Recalling the past
and imagining the future. *Science*, 367(6473), eaaw4325.
DOI [10.1126/science.aaw4325](https://doi.org/10.1126/science.aaw4325)。

定论性综述。印迹细胞为分配而竞争——内在兴奋性决定谁胜出——表现出更高的树突棘密度和优先的下游连接，支持人工再激活，允许植入**虚假记忆**，也存在可被重新激活的沉默印迹。一条记忆由跨脑区的**印迹复合体**支撑，而不是单个节点。

值得带走的两点：记忆是从分布式细胞群装配出来的重构；**分配是竞争性的**——一种资源受限的写入策略，不是自由追加。

### 海马索引

**Teyler, T. J., & DiScenna, P. (1986).** The hippocampal memory indexing
theory. *Behavioral Neuroscience*, 100(2), 147–154.
DOI [10.1037/0735-7044.100.2.147](https://doi.org/10.1037/0735-7044.100.2.147)。

海马存的是**索引**——事件发生时哪些皮层区域活跃；重新激活索引就重新激活皮层细胞群。皮层持有内容；海马持有**指针**。

这是文献里最能直接迁移的想法，而且值得说清为什么：它预测记忆系统里稀缺、快速、高分辨率的那部分结构应该装**地址，不装载荷**。向量索引本来就是这样——而大多数 agent 记忆系统不是，它们把内容块存进索引就以为完事了。

**Teyler, T. J., & Rudy, J. W. (2007).** The hippocampal indexing theory and
episodic memory: Updating the index. *Hippocampus*, 17(12), 1158–1169.
DOI [10.1002/hipo.20350](https://doi.org/10.1002/hipo.20350)。

索引必须捕捉一个事件特征所产生的皮层活动模式，并且能投射回去，从而让*部分线索*重建整个模式。引这篇作为现代表述，1986 年那篇是源头。

### 互补学习系统

**McClelland, J. L., McNaughton, B. L., & O'Reilly, R. C. (1995).** Why there
are complementary learning systems in the hippocampus and neocortex.
*Psychological Review*, 102(3), 419–457.
DOI [10.1037/0033-295X.102.3.419](https://doi.org/10.1037/0033-295X.102.3.419)。

两个学习速率相反的系统：海马快速学新项目而不破坏结构；新皮层学得慢、交错地学，因此能从大量经验中发现结构。在慢学习系统里做快学习会导致**灾难性干扰**；海马快存储加上**重放**把新记忆交错地写入新皮层。

这是"情节缓冲 + 慢权重"整套架构的理论宪章——也是"简单微调毁掉旧能力"的正确诊断：你让一个新皮层去干了海马的活。

**Kumaran, D., Hassabis, D., & McClelland, J. L. (2016).** What learning systems
do intelligent agents need? Complementary learning systems theory updated.
*Trends in Cognitive Sciences*, 20(7), 512–534.
DOI [10.1016/j.tics.2016.05.004](https://doi.org/10.1016/j.tics.2016.05.004)。

海马的**重放是目标依赖的**——它按 agent 当前的需要给经验统计加权，而不是均匀重放。对结构一致的信息，新皮层学习可以**很快**。作者是造 DeepMind 的人，写的是智能 agent 需要什么。对 AI repo 来说这是最有用的单篇整合文献，因为它把重放重新定义为一个**优先级策略**——而记忆系统本质上就是一个优先级策略。

### 睡眠与巩固

**Diekelmann, S., & Born, J. (2010).** The memory function of sleep. *Nature
Reviews Neuroscience*, 11(2), 114–126.
DOI [10.1038/nrn2762](https://doi.org/10.1038/nrn2762)。

新编码的痕迹在睡眠中被再激活并被重新分布（"系统巩固"），慢波睡眠推动海马→新皮层迁移，REM 支持稳定化与整合。睡眠是一个**离线计算窗口**，快存储在不受新干扰的情况下重放进慢存储。

**Rasch, B., & Born, J. (2013).** About sleep's role in memory. *Physiological
Reviews*, 93(2), 681–766.
DOI [10.1152/physrev.00032.2012](https://doi.org/10.1152/physrev.00032.2012)。

**清醒为编码优化，睡眠为巩固优化**——而且这两种模式互不相容，因为要求互相冲突。这是一个论据：巩固应该是一个**计划性的批处理**，而不是在检索过程中顺手发生的事。

**Walker, M. P., & Stickgold, R. (2006).** Sleep, memory, and plasticity.
*Annual Review of Psychology*, 57, 139–166.
DOI [10.1146/annurev.psych.56.091103.070307](https://doi.org/10.1146/annurev.psych.56.091103.070307)。
以及 **Walker & Stickgold（2010），"Overnight alchemy: sleep-dependent memory
evolution"，*Nature Reviews Neuroscience*, 11(3), 218**
（[链接](https://www.nature.com/articles/nrn2762-c1)），提出三种整合操作——**单元化、同化、抽象化**——并给出 SWS 后接 REM 的两阶段模型。这个三分法可以直接当作"整合任务到底该对重放数据做什么"的分类法。

**Tononi, G., & Cirelli, C. (2014).** Sleep and the price of plasticity.
*Neuron*, 81(1), 12–34.
DOI [10.1016/j.neuron.2013.12.025](https://doi.org/10.1016/j.neuron.2013.12.025)。

**突触稳态假说**：清醒时的学习在全脑范围内增强突触，抬高能量成本、降低信噪比、**让学习容量饱和**；睡眠把净突触强度重新归一化，并下调部分突触。这是**巩固是一项算力受限操作**最清楚的生物学表述。对应的 AI 类比不是权重衰减——而是一个容量固定的系统必须*花掉*容量，而重新归一化是它换取容量的方式。

### 再巩固

**Nader, K., Schafe, G. E., & LeDoux, J. E. (2000).** Fear memories require
protein synthesis in the amygdala for reconsolidation after retrieval. *Nature*,
406(6797), 722–726. DOI [10.1038/35021052](https://doi.org/10.1038/35021052)。

重新激活一条已巩固的恐惧记忆，会把它带回需要新蛋白质合成的**不稳定状态**。再激活后给茴香霉素造成遗忘；不给再激活、只给茴香霉素不造成遗忘；延迟 6 小时给也不造成遗忘。

**检索本身就是一次写入。** 读—改—写才是生物学上正确的原语；纯读路径是虚构。每一次访问都是更新——或污染——记录的机会。

---

## 4. 神经调质与显著性

**Lisman, J., Grace, A. A., & Düzel, E. (2011).** A neoHebbian framework for
episodic memory; role of dopamine-dependent late LTP. *Trends in Neurosciences*,
34(10), 536–547.
DOI [10.1016/j.tins.2011.07.006](https://doi.org/10.1016/j.tins.2011.07.006)。

Hebb 规则能解释早期 LTP，解释不了**晚期** LTP——后者需要一个额外的非局部信号；在 CA1，新奇性和奖赏通过**多巴胺**提供这个信号。结果是一条**新 Hebb 规则**：前 × 后 × 神经调质。

朴素的点积相似度是一条早期规则——它能标记，但不能**提交**。提交需要第三个全局的、携带价值的信号。

**Shohamy, D., & Adcock, R. A. (2010).** Dopamine and adaptive memory. *Trends
in Cognitive Sciences*, 14(10), 464–472.
DOI [10.1016/j.tics.2010.08.002](https://doi.org/10.1016/j.tics.2010.08.002)。

多巴胺——标记动机性重要事件的系统——让记忆偏向有动机意义的事件，所以**预期和结果**都在塑造什么被存下来。编码是**按价值加权的**。

合起来：**奖赏预测误差就是写入使能信号。** 预测误差说的是"这跟模型的预期不一样"，而这正是模型应该更新的条件。一个每次观察都写下来的 agent，是在没有写入使能线的情况下运行；一个在惊奇时才写的 agent，实现的是多巴胺规则。

**de Quervain, D. J.-F., Aerni, A., Schelling, G., & Roozendaal, B. (2009).**
Glucocorticoids and the regulation of memory in health and disease. *Frontiers
in Neuroendocrinology*, 30(3), 358–370.
DOI [10.1016/j.yfrne.2009.03.002](https://doi.org/10.1016/j.yfrne.2009.03.002)。

关键的不对称：糖皮质激素**增强**情绪唤起经历的巩固，但**损害**提取。这是**分阶段**的调制——同一个信号强化写入，削弱读取。

**Lupien, S. J., McEwen, B. S., Gunnar, M. R., & Heim, C. (2009).** Effects of
stress throughout the lifespan on the brain, behaviour and cognition. *Nature
Reviews Neuroscience*, 10(6), 434–445.
DOI [10.1038/nrn2639](https://doi.org/10.1038/nrn2639)。

应激效应取决于发育时点和具体子系统。**一个全局调制信号对不同子系统可以有相反的效果**——所以单个全局的"重要性"或"学习率"标量并不是一个中性的设计选择。

---

## 5. 书

| 著作 | 出处 | 为什么值得占一格 |
|---|---|---|
| **Why We Forget and How to Remember Better**（2023） | Budson, A. E., & Kensinger, E. A. *Why We Forget and How to Remember Better: The Science Behind Memory*. Oxford University Press. ISBN 9780197607732. [OUP](https://global.oup.com/academic/product/why-we-forget-and-how-to-remember-better-9780197607732) | 通俗但严谨的最佳一本，围绕"遗忘是正常且有用的"来组织。作者是神经科医生加认知神经科学家。**第一作者是 Budson。** |
| **Moonwalking with Einstein**（2011） | Foer, J. *Moonwalking with Einstein*. Penguin Press. [PRH](https://www.penguinrandomhouse.com/books/307431/moonwalking-with-einstein-by-joshua-foer/) | 关于记忆宫殿（方法位置法）最好的叙述——那本质上就是一套索引方案。记忆运动员的记性普通，编码器不普通。 |
| **The Art of Memory**（1966） | Yates, F. A. *The Art of Memory*. University of Chicago Press. [UCP](https://press.uchicago.edu/ucp/books/book/chicago/A/bo91674300.html) | 印刷术让记忆术变得不必要之前，两千五百年刻意记忆架构的历史。 |
| **Remember**（2021） | Genova, L. *Remember: The Science of Memory and the Art of Forgetting*. Harmony. [PRH](https://www.penguinrandomhouse.com/books/622269/remember-by-lisa-genova/) | 抵达"日常遗忘是特性"这个框架最短的路。 |
| **Memory: A Very Short Introduction**（2009） | Foster, J. K. Oxford University Press. ISBN 9780192806758. [OUP](https://global.oup.com/academic/product/memory-a-very-short-introduction-9780192806758) | 干净的教科书级入门。 |
| **Memory**（第 3 版 2020；第 4 版 2025） | Baddeley, A., Eysenck, M. W., & Anderson, M. C. Routledge. [Routledge](https://www.routledge.com/Memory/Baddeley-Eysenck-Anderson/p/book/9781032592084) | 参考书。 |
| **How We Learn**（2020） | Dehaene, S. *How We Learn: Why Brains Learn Better Than Any Machine… for Now*. Viking. [PRH](https://www.penguinrandomhouse.com/books/606196/how-we-learn-by-stanislas-dehaene/) | 神经科学与机器学习的对照，也是对"规模就是一切"有用的配重。 |
| **Forgetting**（2021） | Small, S. A. *Forgetting: The Benefits of Not Remembering*. Crown. [PRH](https://www.penguinrandomhouse.com/books/622823/forgetting-by-scott-a-small/) | 单作者论证遗忘具适应性最好的一本。 |
| **Forgetting: Myths, Perils and Compensations**（2015） | Draaisma, D. Yale University Press. [Yale](https://yalebooks.yale.edu/book/9780300207286/forgetting/) | 历史与文化侧的对照。引它来说"把遗忘当失败是一种现代偏见"。 |

---

## 6. 机制 → 最接近的 AI 对应物

**只当类比来读。** 这里没有任何一句在说 AI 应该照抄生物。生物机制是进化在特定约束下的产物——代谢成本、发育时间、生存——这些约束对硅不成立。这个映射的价值是**诊断性**的：它显示出一个系统回答了哪些设计问题，又默默默认了哪些。

| 生物机制 | 最接近的 AI 对应物 | 类比成立之处 | 断裂之处 |
|---|---|---|---|
| Ebbinghaus 衰减曲线 | 指数衰减 / TTL；近因加权打分 | 都是单调、前重后轻、渐近的 | 生物衰减依赖条目自身历史（级联元可塑性给出幂律）；TTL 用的是写入至今的时间，错的变量 |
| Müller & Pilzecker 倒摄抑制 | 写入缓冲 / 写后冲刷窗口 | 都说一条记忆容易被紧随其后的东西伤害 | 生物干扰是内容介导的（相似材料伤害更大）；写队列对内容无感 |
| Atkinson–Shiffrin 控制过程 | 检索/重排策略、查询改写、路由 | 都把架构和可调策略分开 | 人的控制过程是习得的、元认知的；agent 策略多是手写启发式 |
| Tulving 情景/语义 | 原始事件日志加来源戳 vs 蒸馏出的事实与 schema | 都区分情境绑定与脱离情境 | 生物的语义记忆在编码时就被共同建构，不是从情节日志批量摘要出来的。"日志 → 摘要"的单向流水线是 AI 的发明 |
| Baddeley 情景缓冲器 | 绑定检索片段、任务状态、当前轮次的工作上下文对象 | 都容量有限、多模态、目的是**绑定** | **没有广泛采用的对应物。最大的真实缺口。** |
| 工作记忆容量（约 4 个组块） | 上下文预算；top-k | 都硬性限制同时活跃的条目数 | 组块让人在信息量上近乎无界；上下文窗口按 token 扩展，且随位置非线性退化 |
| 加工深度 | 写入/编码函数：摘要、建链、抽实体、打来源 | 都认为编码深度决定持久性 | AI 能廉价地反复重编码；人基本不能。AI 的杠杆在重加工 |
| 需求概率 | 把检索分数当作 P(现在需要这条 \| 历史) 的估计 | 正确的目标函数，且可从日志直接估计 | 环境统计随部署而变；全局衰减常数是一个平稳性假设 |
| 存储强度 vs 提取强度 | 持久化的记录 vs 当前查询下的分数 | 都区分"还留着"和"拿得到" | 生物上提取强度随**使用**移动，多数存储里只随**时间**移动。读取应该更新分数 |
| 主动遗忘 | 由策略驱动的显式删除/压制 API | 都由控制器驱动，不是被动衰减 | 人的压制是线索特定且可逆的；AI 的删除是全局且不可逆的 |
| 印迹可及性 | 过期索引项、墓碑、取不到的向量 | 都是失去访问而非失去载荷 | 生物可及性随脑状态波动；AI 索引可用性是二值的 |
| 突触级联（Fusi） | 逐参数元可塑性学习率；权重年龄 | 都让覆写成本依赖历史 | 级联模型需要多个离散状态；实现里常用一个标量近似 |
| 神经发生作为正则化 | 随机单元重置；dropout | 都显示破坏结构能改善泛化 | 神经发生在空间和时间上都是有结构的；随机重置不是 |
| 内在遗忘 | 与写入竞争的后台压实任务 | 都把擦除当默认 | 生物遗忘受多巴胺门控、受显著性调制；cron 任务不是 |
| 海马索引 | 存**地址**、内容存在别处的向量索引 | 都让部分线索重建完整模式 | 生物索引稀疏且模式分离；稠密嵌入正相反，且会互相干扰 |
| 互补学习系统 | 快的情节存储 + 慢权重 + 重放 | 双时间尺度切分是文献里验证得最好的结构 | 生物重放在睡眠中离线发生；在线经验重放没有这种分离 |
| 睡眠巩固 | 离线批处理：去重、合并、抽象、重建索引 | 都需要一个编码**关闭**的窗口 | 人的睡眠是带神经调质门控的全脑状态切换；批处理任务与服务共用同一基底，也共用它的失败模式 |
| 突触稳态 | 容量重新归一化；周期性重嵌入 | 都从有限的可塑性预算出发 | SHY 预测净**下调**，AI 系统很少这么做——它们加容量 |
| 再巩固 | 带不稳定窗口的读—改—写 | 都把检索当作修改记录的机会 | 人的再巩固需要蛋白质合成且可被阻断；AI 没有这种代价的对应物 |
| 新 Hebb / 多巴胺门控 LTP | 由预测误差或奖赏门控的写入使能 | 都只在模型错了的时候提交 | 生物的 RPE 是一个广播的标量；AI 的惊奇估计是逐 token 的，且常常校准不准 |
| 应激不对称 | 写入与读取路径各自独立的显著性权重 | 都分阶段 | 人的应激是一个起效与消退都慢的全局内分泌状态；AI 没有等价模式 |
| 重构记忆（Bartlett） | 依检索上下文生成（RAG 隐含地就在做） | 都产出看似合理的重构而非逐字拷贝 | 人的重构是**无标记的**——当事人分辨不出来。AI 系统*能*标来源，而且应该标 |
| Loftus 效应 | 查询措辞改变检索到什么、重写成什么 | 都是读取路径污染存储 | 人的污染持久且难检测；AI 可以记录。选择不记录是一个设计决定 |

**三行值得单独拎出来当设计原则。** (i) *Bjork*：读取应该移动检索分数；纯读路径在生物学上是错的，在运营上也是浪费的。(ii) *Ryan & Frankland*：最该建模的失败形态是**还在但取不到**，不是"被删了"。(iii) *Bartlett*：如果你的检索路径是生成式的——而 LLM agent 里它永远是——那你已经有重构记忆了，只是没做标记。

---

## 7. AI 从业者对人类记忆的十二个误解

**1. "记忆就是检索。"** 记忆是**重构**。Bartlett（1932）确立回忆是图式引导的重构；Schacter & Addis（2007）把它重新表述为*建构性的情节模拟*——同一套机制既支持回忆过去也支持想象未来。一个为逐字回放而优化的系统，即便可行，也是错的设计。

**2. "遗忘是被动衰减或缺陷。"** 遗忘是**主动的、受控的操作**。前额叶对海马痕迹施加自上而下的抑制控制（Anderson & Hulbert 2021）；在果蝇里默认状态可能就是擦除，而巩固是那个主动竞争的方（Davis & Zhong 2017）。

**3. "遗忘意味着信息没了。"** 主要形态是**失去可及性**。沉默印迹能被光遗传学取回（Roy et al. 2017）；遗忘是把印迹从可及移向不可及的回路重塑（Ryan & Frankland 2022）。把遗忘建模成删除，是在建模罕见情况。

**4. "容量限制是应该被工程消除的缺陷。"** 它是**抽象化的机制**。知识获取与自动化*要求*丢弃实例（Nørby 2015）；注意焦点限制约 4 个组块（Cowan 2001），而人类超出它的方式是组块化——改变单位，而不是扩大缓冲。一个上下文无限又不做压缩的系统不是超人的记忆，而是一个形成不了概念的系统。

**5. "巩固是免费的，或者可以顺手做。"** 它是**算力受限**的。可塑性会饱和，必须重新归一化（Tononi & Cirelli 2014）；清醒与睡眠是互不相容的模式（Rasch & Born 2013）。Müller & Pilzecker 在 1900 年说的是同一件事：编码后的窗口之所以脆弱，正因为巩固就发生在那里。

**6. "记忆是一个写进去、读出来的存储。"** 检索**就是写入**（Nader et al. 2000），而且检索比重学更能加强记忆（Bjork & Bjork 1992）。一个读路径没有副作用的系统不是简化的大脑——它缺了一个生物学既用来学习也用来污染的机制。

**7. "相似度是正确的检索信号。"** 正确的信号是**需求概率**——这条信息*现在*被需要的可能性估计，它是频率、近因、间隔的函数，不是与查询的语义距离（Anderson & Schooler 1991）。

**8. "记忆是一个模块。"** 它至少分陈述性/非陈述性（Squire 2004），再细分为情景/语义/程序性，并按 SPI 关系组织（Tulving 1995）。权重、索引、策略是不同的系统。把"记忆层"当单一组件，是在重犯 1960 年代模态模型的错误。

**9. "存得越多严格越好。"** Yalnizyan-Carson & Richards（2022）做过这个实验：一个忘掉很大比例旧记忆的 agent，表现得*优于*无界记忆的版本。Tran et al.（2022）在网络层面得到同样的结果。

**10. "记忆的保真度是目标。"** 目标是**决策质量**（Richards & Frankland 2017）。第 6 节所有内容都来自把这句话当真——包括这一条：一个完全忠实的记忆是*病态的*。

**11. "显著性是一个旋钮。"** 它至少是两个，而且符号相反。糖皮质激素增强巩固同时损害提取（de Quervain et al. 2009）；应激效应随子系统和发育时点而不同（Lupien et al. 2009）。一个同时作用于写入和读取路径的单一重要性分数，不是对生物的简化——它是另一种计算。

**12. "遗忘是基于时间的。"** 它是**基于历史与状态**的。保持率来自每个突触可塑性状态组成的级联，产生的是幂律而非指数遗忘（Fusi et al. 2005）；可及性在短时间尺度上随脑状态波动（de Snoo & Frankland 2025）。一个 `TTL` 字段算的是 `now - created_at`，而这恰恰是这两篇都不认为是关键变量的那个量。

---

## 一句话论点

如果这个 repo 只能留一句话，应该是这句：

> 记忆不是一个存储，而是一台**关于未来需求的预测机**；遗忘是让它的预测保持校准的机制——所以一个从不遗忘的系统，不是记忆更好的系统，而是一个已经停止估计的系统。

---

*参见：[02-cognitive-architectures.md](02-cognitive-architectures.zh-CN.md) 看试图用这些发现去建记忆的计算传统，以及 [00-timeline.md](00-timeline.zh-CN.md) 看时间脉络。*
