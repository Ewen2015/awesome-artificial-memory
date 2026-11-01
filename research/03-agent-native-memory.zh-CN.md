# Agent 原生的记忆：agent 们到底怎么管自己

**研究日期：** 2026-11-01

这份文档是对十九个已落地的 agent 产品如何管理记忆的独立调研，材料全部取自各家自己的文档，不取市场营销材料，也不取第三方文章。下面每一条都读自[来源](#来源)里列出的页面。文档里没有的细节，要么确实不存在，要么标注为未核实。

它同时也是一个论证。实证发现是：**六个互不协调的团队收敛到了同一个架构上**。搞明白为什么，比读任何一篇研究论文都更能说明 agent 记忆是什么。

---

## 1. 先解决词汇问题

厂商用"记忆"这个词指至少四种互不相关的东西，这个领域的大部分混乱——包括学术文献里的——都来自把它们混为一谈。

| 制品 | 谁写 | 持久 | 跨会话 | 权威性 |
|---|---|---|---|---|
| **指令**（`CLAUDE.md`、`AGENTS.md`、`QWEN.md`、`GEMINI.md`、rules 文件） | 人 | 是 | 是 | 高——当作长期命令 |
| **持久记忆**（agent 自己写的笔记、抽取的事实） | agent | 是 | 是 | 低——当作回忆，明确*不是*政策 |
| **情节存储**（transcript、事件日志、会话状态） | 确定性代码 | 是 | 是 | 无——证据，不是指令 |
| **参考资料**（文档、文件、检索到的上下文） | 人或系统 | 是 | 是 | 视情况而定 |

最要紧、而且每个成熟产品都明确说出来的区分，是**指令 vs 记忆**。它不是命名习惯，它是一条信任层级。

ChatGPT 的文档说，必须遵守的团队规定要放在 `AGENTS.md`，把记忆当作"一层有用的回忆，而不是……必须始终生效的规则的唯一来源"。GitHub Copilot 说仓库事实"不应被依赖为始终会被遵守的规则……请改用自定义指令"。OpenHands 把"写给*任何* agent 的指令"和"本 agent 学到的东西"分开。Qwen Code 一句话讲清了政策："auto-memory 是尽力而为，`QWEN.md` 是保证的。"

**这个层级为什么存在。** agent 自己写的记忆，出自一个会被它处理的内容操纵的系统。指令文件出自一个可追责的人。把两者合并，就意味着一条被投毒的记忆会静默地变成政策——而这正是 [06-security.md](06-security.zh-CN.md) 记录的攻击类别。信任层级首先是一个安全控制，只是它恰好也是可用性特性。

**对设计的推论。** 任何记忆系统都应该能回答：*这是我回忆起来的事实，还是我必须遵守的规则？* 如果答案存在同一个地方、同一个权威层级里，那这个系统没有答案。

---

## 2. 收敛：一个索引加若干主题文件

六个产品，六个互不相关的团队，走到了同一个架构上。

**Claude Code。** `~/.claude/projects/<project>/memory/MEMORY.md` 是索引，"每条记忆一个主题文件"。会话开始时只加载 `MEMORY.md`——前 200 行或 25 KB，以先到者为准——主题文件由 agent 用普通文件工具按需读取。

**Qwen Code。** `~/.qwen/projects/<project>/memory/`，`MEMORY.md` 是"紧凑的索引，不是存储"。预算：**200 行和 25,000 个 UTF-16 码元**，由生成的正文和提示加载部分共享。

**Devin。** 一块 memory drive——"一个持久的 Markdown 笔记 Git 仓库"——`MEMORY.md` 里放通用偏好加上其余文件的索引。每个会话拿到自己的一份 checkout，并收到 `MEMORY.md` 作为上下文。

**OpenHands。** 两层（`~/.openhands/memory/`、`<workspace>/.openhands/memory/`），每层一个 `MEMORY.md`，它是"唯一被注入提示的文件"，另有日记 `YYYY-MM-DD.md`，"从不被自动注入"。

**Letta。** 一个 git 仓库，被投影成文件系统。记忆根目录下的文件每轮都在系统提示里；自带 `MEMORY.md` 的子目录"留在上下文之外，直到需要"。

**Kimi API。** 一个挂在 `/mnt/agents/memories/<store-name>/` 的记忆库。会话开始时平台只提供根 `README.md` 和 `index.md` 的有限内容，**各最多 16 KiB**；其余必须用文件工具读。

| 产品 | 索引文件 | 预算 | 溢出策略 |
|---|---|---|---|
| Claude Code | `MEMORY.md` | 200 行或 25 KB | 写入时报错；加载时尾部静默丢弃 |
| Qwen Code | `MEMORY.md` | 200 行 / 25,000 码元 | 优先容纳 ≤150 码元的普通条目；整条跳过，绝不截半行 |
| Devin | `MEMORY.md` | 未公布 | 由 Dreaming 重排 |
| OpenHands | `MEMORY.md` | 合计约 6,000 字符 | 每层从最旧开始整行丢弃，并给出提示 |
| Letta | `MEMORY.md` | 5 MiB / 50 MiB 仓库配额（不是上下文） | 用目录嵌套代替截断 |
| Kimi API | `README.md` + `index.md` | 各 16 KiB | 其余需显式读文件 |

**这就是那个发现。** 不是"大家都在用 Markdown"——那是基底选择，而且部分是当前工具链的偶然。发现是：**大家都收敛到"两级记忆 + 第一级硬预算 + 第二级懒加载"**，而且每个人都独立地发现这个预算是必需的。

**为什么会收敛。** 想想 agent 在会话开始真正需要什么。它有一个固定而昂贵的上下文预算，而且必须在知道会被问什么之前决定自己要知道什么。每个选项的取舍不同：

*全量加载。* 成本随记忆规模线性增长，质量随位置和干扰密度下降，到某个规模系统就失效了。这是所有人一开始的做法，也是所有人放弃的做法。

*按相似度检索。* 需要一个查询，而会话开始时没有查询。更糟的是，它让行为依赖于一个会在脚底下变的嵌入模型，并且让来源追踪和删除都难得多。

*加载小索引，其余按需取。* 成本固定且小。由 agent 自己的推理决定取什么——用的是唯一能看到真实任务的那个部件。索引是一份**目录**，而目录是大存储与小工作集之间最便宜的接口。

第三种赢，原因可以推广：**索引把检索问题变成了导航问题，而导航是模型本来就会的事。** 不需要新组件，关键路径上没有嵌入模型，不需要调参。

**海马早就到了。** 一个小的快结构，装着指向大的慢存储的指针，部分线索即可重建整体（[01-human-memory.md](01-human-memory.zh-CN.md) 第 3 节）。这个收敛不是时尚的巧合，而是同一个问题、同一个解。

**另两种设计有教育意义。** Gemini CLI 用三层 `GEMINI.md`、没有索引，整个层级拼接后随每条提示发送——这只有在预期规模很小时才成立。Cline 用六份固定文档（`projectbrief.md`、`productContext.md`、`activeContext.md`、`systemPatterns.md`、`techContext.md`、`progress.md`），没有索引，也完全没有运行时支持：模型被指示"在**每个**任务开始时读取**所有** memory bank 文件"。两者都能用，两者都被同一个约束限住，而两者都不能像索引那样扩展。

---

## 3. 四个问题，由已上线的代码来回答

### 3.1 存什么——类型系统

这里是产品差异最大的地方，也是 2023 年以来这个领域真正有进展的地方。

**Claude Code** 用四种类型，写在 YAML frontmatter 里：`user`（角色、专长、工作偏好）、`feedback`（纠正与已验证的做法）、`project`（进行中的工作、截止日期、无法从代码或 git 历史里推出的决定）、`reference`（项目之外的东西在哪里找，比如 issue tracker、看板）。Claude 被指示跳过任何能从代码库推出、或 `CLAUDE.md` 已经说过的东西。

**Qwen Code** 实质上是同样的四种——关于你、你的反馈、项目上下文、外部参考——外加一个可选的结构化模式，增加 `category`、`keywords`、`usage scenarios` 三个 frontmatter 字段。

**GitHub Copilot** 用两种，按*归属而非内容*切分：仓库事实（属于某个仓库，在那个仓库的设置里管理）与用户偏好（属于用户，跨所有仓库，在受管套餐里归计费实体所有）。模型根据"这条适用于谁"来决定，模糊时会追问。

**阿里云百炼** 用三种，按*可变性*切分：事实记忆（事件；可自动更新、可过期）、用户画像（来自自定义模板的固定属性）、技能记忆（"从轨迹中抽取的可复用任务方法"）。

**Devin** 没有枚举，但公布了收录政策：收偏好、收"纠正连同之后要遵守的规则"、收决定及其理由、收仓库与环境的坑。不收会话摘要、不收任务状态（PR 号、状态）、不收任何重新发现很便宜的东西、不收密钥。

**豆包** 用四个功能桶——画像、偏好、兴趣、习惯——外加短期/长期之分。

**这些之间的规律。** 有四个区分轴反复出现，每个回答一个不同的问题：

| 轴 | 它回答什么 | 例子 |
|---|---|---|
| **主体** | 这条是关于谁的？ | Claude Code 的 `user` 对 `project`；Copilot 的用户偏好对仓库事实 |
| **内容来源** | 这是被告知的还是被纠正的？ | Claude Code 的 `user` 对 `feedback` |
| **可变性** | 这条会变，还是固定的？ | 百炼的画像是固定，事实会过期 |
| **可推导性** | 我能不能直接查出来？ | Devin 的"重新发现很便宜"排除项 |

第四条是最被低估的。每个成熟产品都有一条明确的**排除**政策——不存代码已经说的、不存 git 历史已经说的、不存文件系统已经说的。这不是整洁癖；这是加工深度原理的正确反向应用：如果重新推导很便宜，存下来只增加过时风险，不带来检索收益。Devin 的文档直说了，Copilot 的过期机制则在结构上强制执行。

### 3.2 什么触发一次写入

这是研究文献最关心、而产品回答最不一致的一个问题。按写得多少排序：

**什么都抽。** 百炼对每次对话和轨迹输入都做抽取。豆包被动拾取"在对话中反复出现、看起来有用"的偏好。

**agent 干活时顺手写。** Claude Code、Qwen Code、Devin、OpenHands、Letta 都允许工作中的 agent 在会话内编辑记忆。Devin 的说法："Devin 会在你工作时自己保存这些。"

**在空闲之后跑后台。** Codex CLI 会等到一个对话"空闲足够久，以避免总结仍在进行中的工作"——明确**不**在对话结束时跑，因为对话结束不等于工作结束。Gemini CLI 的 Auto Memory 要求会话**空闲 ≥ 3 小时且 ≥ 10 条用户消息**才会抽取。

**只有人批准才写。** Gemini CLI 把记忆更新起草成 unified-diff 的 `.patch` 文件，把过程写成 `SKILL.md`；它"不能直接编辑活动记忆文件"，补丁要过目标白名单，只在批准后原子应用。Antigravity 的 `/learn` 是同一个形状："agent 从不立刻编辑配置文件。它先起草一份提案给你审阅。"

**只在失败时写。** 这是 SOAR 的僵局规则（[02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md) 第 3 节），而**没有任何已上线产品实现它**。第 7 节再说。

**这张触发条件表其实是一张"谁负责"的表。** 会话内写入便宜、即时，但发生在会话结果已知之前——Devin 的文档明说"新记忆作用于未来的会话，不是写下它的那个会话"，这是在承认这次写入尚未被验证。后台批次能看到结果并做整合，这就是为什么它们无一例外收敛到 *dreaming* 这个词上。审批门是给"写错了代价很高"的场景用的。

### 3.3 索引与检索

索引已经定了（第 2 节）。检索路径没有，而产品在一条真实的架构断层线上分开了：**系统给 agent 一个搜索工具，还是预先注入？**

**agent 自己导航。** Claude Code、Devin、OpenHands、Letta、Kimi API 只注入索引，给 agent 普通文件工具。Letta 默认**不建**向量索引——agent 用普通文件搜索和读取；语义搜索要另外开启索引。

**服务端检索。** 百炼做语义搜索并注入 top-k，暴露 `top_k` 和 `min_score`（Pro 版默认 0.3）。Copilot 服务端做相关性过滤，而且关键地——**在使用前拿引用去当前分支校验**——一条证据已经不在的记忆不会被送出。

**什么都不做。** Gemini CLI 把整个 `GEMINI.md` 层级随每条提示发送。Cline 依赖模型遵守"读六个文件"的指示。

有三个设计点值得单独拎出来。

*Copilot 的引用校验是这份调研里最有意思的检索机制，而且没有别人做。* 每条记忆都带着指向代码的引用；检索时拿去当前分支校验。这是一个**真值维护**步骤，不是相关性步骤，也是"失效而非删除"原则（[01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 第 2 节，Ryan & Frankland：遗忘是失去访问，不是失去载荷）在已上线系统里唯一的实现。

*OpenHands 把注入的记忆包在 `<UNTRUSTED_CONTENT>` 里。* 整块——两个 `MEMORY.md` 索引——都带着"记忆可能包含提示注入"的明确警告到达。这是正确的默认值，而且几乎是独一份。

*Kimi API 的 16 KiB × 2 根文件，是唯一公开的、明确属于上下文预算而非文件预算的数字。* 存储里单个文件最大 20 MiB；只有 32 KiB 的索引会到达模型。

### 3.4 遗忘

已上线代码里有三种机制，而只有一种符合文献所说的遗忘。

**过期。** GitHub Copilot：**默认 90 天**，被点"有用"才重置，*检索不重置*，被点"没用"则在弃用后删除；组织可把仓库事实设为 7–180 天。百炼：事实规则默认**有效期 180 天**。这些都是真实、强制的，而且——注意——**由经验证的质量重置，而不是由使用重置**，这是对生物学规则的有意偏离。

**容量下的衰减。** 豆包：「存储容量有限，久远或低频的记忆可能会被弱化或遗忘」。而且明确说：「记忆召回是概率性的，不是确定性的」——所以同一个问题可能召回不同的记忆。

**整合批次。** Devin 的 Dreaming 大约"在你使用 Devin 的日子里每天跑一次"，合并重叠笔记、删掉"没有任何会话用过"的临期或过时笔记、补上漏掉的教训、重排索引——同时保留来源链接和显式偏好。Qwen Code 有 `/dream`。Gemini CLI 的 Auto Memory 是"仅提案"的变体。Letta 的 dreaming 按**步数或压缩事件**触发而不是按时间表，而且它的第二轮复核"不会向你请求批准"。Copilot 的 Dreaming 扫合格会话和 PR 评审反馈来"更新上下文，而不是底层模型"。Kimi 的梦境有活动触发（`min_changed_sessions`）和/或每日定时（`HH:mm` + 时区）。

**到处都缺的。** 没有产品实现依赖检索的衰减（Bjork 那条"读会加强"）。没有产品把"仍然存着"和"当前可达"实现成两个独立字段——最接近的是 Copilot 的投票，那是质量信号，不是可及性信号。也没有任何产品实现**可证明的**删除；见 [06-security.md](06-security.zh-CN.md)。

**软删除比看起来更糟。** 豆包的记忆开关关掉时"停止新记忆、停止使用已有记忆，但不删除它们"。Devin：关掉记忆"不删除已有笔记，并停止 dreaming"。两者都是合理的产品决定，两者都意味着用户以为数据没了之后，数据仍在且可恢复。

---

## 4. 八个机制族

按机制而非按厂商分组，十九个产品落在八个族里，而这些族横切公司规模与地域。

| 族 | 成员 | 特征 |
|---|---|---|
| **指令文件层级** | Aider、Amp、Zed、Cline（rules 层）、Antigravity、Devin Local、Cursor（rules）、Codex/ChatGPT（`AGENTS.md`） | 固定作用域上的有限 Markdown 文件，拼接注入；只有人写；可移植性就是全部意义 |
| **索引 + 主题文件** | Claude Code、Qwen Code、Devin、OpenHands、Letta、Kimi API、Codex CLI、Antigravity `/learn`（退化情形）、Gemini CLI（无索引） | 小的常驻索引，细节按需读，有明确预算 |
| **Git 支撑的记忆文件系统** | Letta、Devin、Qwen Code（团队层）、OpenHands（可提交）、Kimi API（版本而非 git） | 提交是持久性边界；合并替代"后写覆盖"；记忆可以当 PR 审 |
| **后台整合** | Devin、Qwen Code、Gemini CLI、Letta、Copilot、Kimi API | 定时或触发；按自主度分为"只提案"与"直接写" |
| **类型化记录存储** | Copilot、百炼、Claude Code、Qwen Code、Kimi API、Claude apps | 记忆是带字段的数据——类型、来源、投票、有效期——不是散文 |
| **不透明的云端行为记忆** | ChatGPT、Claude apps、Gemini app、豆包、Kimi app、Cursor Grok Bot | 没有路径、没有格式、没有 API；产品保留摘要、弱化、概率召回的权利 |
| **提示强制的伪记忆** | Cline（Memory Bank）；历史上的 Roo Code（无法核实） | 模型被指示在任务边界读写文件；正确性完全依赖指令遵循 |
| **作为可查询制品的情节存储** | Copilot（SQLite + `/chronicle`）、OpenHands（事件流 + Condenser）、Letta（`messages search`）、Claude Code（带保留期的 transcript）、Cursor SDK（`journal.jsonl`） | 对话记录被索引和查询，而不是被压缩成记忆——这是抽取路线的一个明确替代 |

两点要注意。

**第 2 族和第 3 族正在合流。** 每个 git 支撑的实现（Letta、Devin、Qwen 团队层）同时也用索引。git 基底不是索引架构的替代品，它是索引架构下面的持久层。

**第 6 族是产品品类，不是技术品类。** ChatGPT、Claude apps、Gemini app、豆包、Kimi app、Cursor 的 Grok Bot 之所以都不透明，原因相同：它们都是消费级服务，记忆是个人化功能，基底是实现细节。这种技术上的不开放不是偶然——可检索的记忆文件是一个接口承诺，而消费级产品规避接口承诺。

---

## 5. 真正重要的设计差异

六个差异，区分了会活得久的产品和活不久的产品。

**1. 会话开始时冻结，还是实时更新。** Claude Code 在开始时加载一次索引。Devin 把规则说明白："新记忆作用于未来的会话，不是写下它的那个会话。"豆包承认延迟——「记忆的更新需要一定的加载时间」——以及纠正之后旧记忆仍可能被召回。Letta 和 Gemini CLI（`/memory reload`）是实时更新的异类。

冻结是站得住的：任务中途变的记忆让行为不可复现。但它有一个产品文档没写的代价——agent 无法在产生纠正的那个会话里学到它，所以用户必须先重复一遍，系统才适应。

**2. 谁能写。** agent 自编辑（Claude Code、Qwen、Devin、OpenHands、Letta）、后台抽取（Codex、Gemini Auto Memory、Copilot Dreaming）、仅提案（Gemini、Antigravity）。只有"仅提案"的设计让人可追责，也只有它与受监管环境兼容。注意 Letta 的第二轮明确"不会向你请求批准"——自主度是一个谱，而厂商在悄悄移动。

**3. 来源追踪。** Devin 在每条项目符号里嵌 `[source: …; added: …]`。Copilot 存引用并校验。Claude Code 打一个 ISO-8601 的 `modified` 字段。OpenHands 把整个注入块包成不可信。Cursor 警告自动化记忆可被不可信输入投毒。豆包和 Kimi 完全没有来源。

来源追踪是预测一个记忆系统是否安全的最佳单一指标。没有来源的记忆无法核验，也无法在证据消失时失效。

**4. 作用域粒度。** 范围很宽，而选择是有后果的：

| 作用域 | 产品 | 后果 |
|---|---|---|
| 机器本地 | Claude Code、Codex CLI | 记忆不跟随用户 |
| 按 worktree | Qwen Code | 隔离免费，共享靠手动 |
| 按仓库、git 共享 | Qwen 团队层、OpenHands（可选） | 可审阅，但提交会进仓库 |
| 按 agent | Letta | 记忆是身份，不是财产 |
| 按账号 | ChatGPT、Claude apps、Gemini app、Kimi、豆包 | 方便；没有项目隔离 |
| 按自动化/机器人 | Cursor | 记忆属于一个岗位 |
| 仓库**和**用户，分开 | Copilot | 唯一有两个并存作用域、且归不同所有者的产品 |

Copilot 的切分最有原则：关于代码的事实属于代码，关于人的偏好属于人，两者有不同的生命周期、不同的权限模型、不同的计费实体。其他所有人选了一个，丢了另一个。

**5. 第三方可读性。** 与基底完全相关，与厂商规模无关。每个用文件基底的编程 agent 都透明；每个消费级助手都不透明。有意思的中间态是 Copilot（服务端记忆、文件端会话）和 Cursor（文件端日志、云端自动化记忆）。

**6. 导入导出。** Claude apps 有实验性的记忆导入导出。Kimi 支持从其他 AI 服务导入记忆。Devin 把格式发布成开放规范（Agent Memory Repo）。没有别人提供可移植的记忆制品。

最后一条比读起来更重要。不能导出的记忆是不能审计、不能迁移、不能带走投奔别家的记忆。可移植性是"用户拥有自己的记忆"的前提——而它现在是一个差异化卖点，不是基线。

---

## 6. 它们都做对了什么

三件事，而且都不是显然的。

**它们都把指令和记忆分开了。** 每个成熟产品都把层级说清楚，而且都把记忆当作弱于政策的东西。这是对 2023 年的真实进步——那时候所谓"记忆"只是一段塞进提示、没有权威层级的文本。

**它们都给索引设了界，而且设得很狠。** 200 行、25 KB、6,000 字符、16 KiB。没有人公布一个宽松的预算，因为每个人都实测发现大索引会毁掉质量。这个领域是靠测量、而不是靠引用 Cowen 的四组块限制，到达了大致同一个数字。

**它们几乎都让人能检视记忆。** 即便不透明的产品也给出一个可编辑可删除的列表。纯文本产品走得更远，而且很明显这是有意的产品决定而非用 Markdown 的副作用：Devin 明确说记忆文件在应用里**只读**，所以修改必须经过一次会话，因为手改的记忆会破坏来源链。

---

## 7. 它们都做错了什么

四个失败，都指向同一个根源：**这个领域实现了存储，跳过了编译器。**

**1. 没有从情节到程序的编译。** 一条频繁被检索的记忆永远是一条记忆。没有产品有一个机制，让反复使用的情节变成规则并从此不再被检索——ACT-R 的产生式编译和 SOAR 的组块化（[02-cognitive-architectures.zh-CN.md](02-cognitive-architectures.zh-CN.md) 第 2–3 节）。存在的是它的*反面*：Antigravity 的 `/learn` 和 Gemini 的 Auto Memory 把一次会话变成规则或技能，但那是显式的一次性用户动作，不是由重复触发的策略。

后果是可测量的。`Library Drift`（arXiv:2605.19576）发现无限累积会让检索退化，而基准均分不动；LLM 自撰技能带来 +0.0 pp，人工整理技能带来 +16.2 pp。没有编译，就是技能库腐烂的原因。

**2. 读取路径不写入。** 没有产品在检索时更新一条记忆的分数、有效性或置信度。Copilot 最接近——它对记忆质量投票，而"没用"会缩短有效寿命——但票是由一个单独的判断投出的，不是由使用投出的。Bjork 的提取强度规则（读会加强，且在存储强度高、提取强度低时加强最多）在任何地方都没有实现。

代价是：一条被检索一千次的记忆和一条被检索一次的无法区分，所以系统不知道哪些记忆是承重的。这是每个系统都已经拥有、又扔掉的数据。

**3. 索引是单点故障。** 这里每个架构都依赖 `MEMORY.md` 是一份好的目录。它不是的时候会怎样？Claude Code 在写入超预算时报错。Qwen 优先短条目。OpenHands 整行丢弃最旧的。这些都是一种**静默退化**——记忆还在，索引不再指向它，而没有任何东西告诉任何人。这恰恰是 Ryan & Frankland 描述的"还在但取不到"这种生物上主要的失败形态，只不过这里是意外到达的，不是设计到达的。

**4. 跨 agent 记忆，被政策禁止了。** A2A 明确声明不共享记忆。MCP 的 2026-07-28 修订变成无状态，取消了记忆服务器所需要的会话假设。Claude Code 的子 agent 不继承主对话的自动记忆。这个领域决定了 agent 之间不共享记忆，而且这个决定是在标准组织里做出的，不是在设计评审里。它对不对确实还没有定论——但它的后果，即 agent 团队无法积累共享知识，限制了多 agent 系统能变成什么。

**根源，说一次。** 这份调研里的每个产品都把记忆当作*保留*。没有一个把它当作*编译*——让学到的东西不再需要被记住的那个过程。[01-human-memory.zh-CN.md](01-human-memory.zh-CN.md) 里的生物系统两者都有。差距就在这里。

---

## 8. 产品笔记

从完整调研中压缩而来；每个产品的细节与来源 URL 见[来源](#来源)。

**Claude Code** —— 仓库作用域目录里的类型化 Markdown 笔记，`MEMORY.md` 索引上限 200 行 / 25 KB，机器本地，子 agent 不继承。索引架构最干净的实例。

**Claude apps** —— 账号级主题记忆加项目级记忆，带项目隔离，以及一个明确的**敏感主题**类别（健康、种族、族裔、宗教、政治、性别认同），默认排除，仅在明确选择加入后保存。有些数据即使用户要求也从不保存。

**Codex CLI** —— 默认关闭。`~/.codex/memories/` 存摘要、持久条目、近期输入和证据；在空闲之后由后台批次生成。`AGENTS.md` 链截断在 32 KiB。

**ChatGPT** —— 账号级记忆，明确置于指令之下；项目级指令与 Sources；每个 dot 的笔记与已保存记忆分开，且其设置变化不影响已写下的笔记。Computer History（macOS，需主动开启）把辅助功能文本和交互事件挖成记忆，不含截图与音频。

**Cursor** —— "记忆"指三件互不相关的事：托管 agent 的逐轮追加 `journal.jsonl`（每条消息 2,000 字符截断）、工作文件系统之外的按自动化 `MEMORIES.md`、以及不透明的按机器人画像。IDE 本身只有规则，没有记忆。

**GitHub Copilot** —— 两个服务端存储、归属不同；引用在分支上校验；投票机制；默认 90 天过期、由"有用"重置；以及一条硬规则：只有根 agent 可以写，子 agent 只能读。这份调研里生命周期模型最完整的一个。

**Windsurf → Devin Local** —— 功能被撤下的最清楚案例。旧 Cascade Memories 只存在于 `/windsurf/plugins/cascade/` 路径下；后继产品的文档有规则、技能、插件、MCP 和 hooks，没有记忆。

**Gemini CLI** —— 三层 `GEMINI.md` 随每条提示发送，加上工具触到某个目录时的即时发现机制。Auto Memory 是这份调研里门控最严的后台批次：空闲 ≥ 3 小时、≥ 10 条用户消息、用锁文件在多个 CLI 实例间串行化抽取、用状态文件记录已处理的会话版本、补丁只在批准后原子应用、密钥脱敏。

**Gemini app** —— 需主动开启、仅美国、需要 AI 套餐和个人账号。两个桶：记忆条目和指令。Google 自己警告，让它忘记某个话题"并不总是完美生效"，并建议去删掉底层对话。

**Antigravity** —— 完全没有记忆。`/learn` 命令开头就是"Agents don't remember conversations"，把会话中的教训转成规则或技能，需人批准，且优先更新已有制品而不是新建重复的。

**Cline** —— Memory Bank 是一种"文档方法论"，不是存储引擎：仓库里六个 Markdown 文件，由粘进 `.clinerules` 的指令驱动。它存在的目的是撑过上下文溢出，正确性完全取决于模型是否遵守"读全六个"的指示。

**Aider** —— 没有记忆。一个用户提供的约定文件，用 `/read` 加入从而只读且可缓存，另有一个从不被自动读回的按仓库 transcript。

**OpenHands** —— 两层、agent 自维护、作为明确不可信的文本注入、约 6,000 字符预算、整行截断并提示。所有开源实现里安全姿态最好的一个。

**Devin** —— git 支撑的 memory drive，逐条来源，每日 Dreaming，按组织内的个人作用域，以及一个开放规范（Agent Memory Repo）——跨厂商标准化格式的唯一尝试。

**Amp** —— 没有记忆；一个五级 `AGENTS.md` 加载器（cwd 到 `$HOME` 的各级父目录、读取某子树时加载该子树、个人、系统、工作区），带 glob 作用域片段，并可回退到 `AGENT.md`/`CLAUDE.md`。

**Zed** —— 没有记忆；一个常驻指令文件（个人 + 八个工具文件名中的首个匹配），按需行为移到 Skills。

**Letta** —— 一个归 agent 所有的 git 仓库，投影成文件系统，路径位置决定某物是常驻上下文还是按需取。默认不建向量索引。子 agent 用 git worktree，使 dreaming 能并发更新记忆而不阻塞主 agent。

**豆包** —— 默认开启、不透明、容量受限、会衰减的抽取，只从对话文本（含语音转文字）挖，不含图片、文件和链接，召回是概率性的，并明确承认更新有延迟。

**Kimi app** —— 账号同步的记忆空间，文件组织的已保存事实加上过往对话，回答时给出引用，支持导入导出。新体验自 2026 年 10 月起逐步推出。

**Kimi API** —— 版本化、可挂载、项目作用域的文件库；每会话 ≤ 8 个库，绑定在会话创建时冻结，索引注入 16 KiB × 2，并发写冲突返回 409，Dreaming 原地重写同一个库且失败时不自动回滚。

**Qwen Code** —— 四种内容类型，200 行 / 25,000 码元索引，受保护的 `pinned/` 区域，git 共享的团队层（默认关闭），`/dream`，以及可选的分层"结构化召回"模式（只注入相关子树）。

**阿里云百炼** —— 一个计费服务，类型系统最明确（事实 / 画像 / 技能），逐条有效期，每类上限 50 条规则，`top_k`/`min_score` 检索，前后快照审计，四条集成通道（REST、MCP、CLI、Skill）。

**Roo Code** —— 已剔除。截至 2026-11-01，任何 URL 都解析不到官方记忆文档；域名重定向到 Roomote，文档 SPA 和 GitHub raw 路径都是 404。不用第三方文章重建。

---

## 9. 这份调研意味着什么

四件事，按它们应当改变你做什么的程度排序。

**1. 索引就是设计。** 如果你在建 agent 记忆，真正要紧的制品是目录，不是存储。它的大小、格式、更新策略，以及溢出时会发生什么——这四个决定系统能不能用。其余都是存储。

**2. 按字符设预算，并且大声失败。** 每个公布了预算的产品都把预算设得很小。每个静默溢出的产品都会丢记忆。Claude Code 的"写入时报错"是正确直觉——装不下的索引是写的人应该知道的设计失败，而不是加载时的意外。

**3. 来源追踪不是可选项，它就是安全机制。** 有来源的记忆可以被核验、被失效、被审计。没有来源的记忆，是一个可被操纵的进程给出的无法核验的主张。Devin 那行格式——`[source: …; added: …]`——是这块领域里最便宜的正确答案，应该照抄。

**4. 把编译器做出来。** 这是没人做、而所有人都需要的一件事。缺的机制不是更好的检索或更大的上下文，而是一个策略：一条反复被用的记忆变成一个规则、一个技能或一条代码路径，然后不再被检索。没有它，记忆单调增长并退化——这份调研里多数产品正处于这个状态，而它们各自的 dreaming 就是在打这个补丁。

---

## 来源

全部于 2026-11-01 获取。

**Anthropic** — [Claude Code 记忆](https://code.claude.com/docs/en/memory) ·
[对话搜索与记忆](https://support.claude.com/en/articles/11817273-use-claude-s-chat-search-and-memory-to-build-on-previous-context) ·
[Projects](https://support.claude.com/en/articles/9517075-what-are-projects)

**OpenAI** — [memories](https://learn.chatgpt.com/docs/customization/memories.md) ·
[个性化](https://learn.chatgpt.com/docs/customization/personalize.md) ·
[projects](https://learn.chatgpt.com/docs/customization/projects.md) ·
[dot 的任务与记忆](https://learn.chatgpt.com/docs/dots/tasks-and-memory.md) ·
[AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md.md)

**Cursor** — [rules](https://cursor.com/docs/rules.md) ·
[automations](https://cursor.com/docs/automations.md) ·
[agent SDK memory](https://cursor.com/docs/agent-sdk/reference/memory.md) ·
[Grok Bot memory](https://cursor.com/help/grok-bot/memory.md)

**GitHub** — [Copilot memory](https://docs.github.com/en/copilot/concepts/prompting-and-context/copilot-memory) ·
[dreaming](https://docs.github.com/en/copilot/concepts/prompting-and-context/copilot-dreaming) ·
[会话数据](https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data)

**Cognition** — [Devin 记忆与 dreaming](https://docs.devin.ai/product-guides/memory) ·
[rules](https://docs.devin.ai/product-guides/rules) ·
[knowledge（已废弃）](https://docs.devin.ai/product-guides/knowledge) ·
[Devin Local 自定义](https://docs.devin.ai/desktop/devin-local/customizations.md) ·
[旧 Cascade memories](https://docs.devin.ai/windsurf/plugins/cascade/memories.md) ·
[Agent Memory Repo 规范](https://github.com/AgentMemoryRepo/agentmemoryrepo)

**Google** — [GEMINI.md](https://geminicli.com/docs/cli/gemini-md/) ·
[记忆文件](https://geminicli.com/docs/cli/memory/) ·
[记忆管理教程](https://geminicli.com/docs/cli/tutorials/memory-management/) ·
[auto memory](https://geminicli.com/docs/cli/auto-memory/) ·
[Gemini app 个人上下文](https://support.google.com/gemini/answer/16598625?hl=en&p=saved_info) ·
[Antigravity rules](https://antigravity.google/docs/rules) ·
[Antigravity /learn](https://antigravity.google/docs/commands/learn) ·
[Antigravity projects](https://antigravity.google/docs/projects)

**其他** — [Cline Memory Bank](https://docs.cline.bot/prompting/memory-bank) ·
[Aider 约定](https://aider.chat/docs/usage/conventions.html) ·
[Aider 选项](https://aider.chat/docs/config/options.html) ·
[OpenHands 持久记忆](https://docs.openhands.dev/sdk/guides/persistent-memory) ·
[OpenHands Condenser](https://docs.openhands.dev/sdk/architecture/condenser) ·
[Amp AGENTS.md](https://ampcode.com/docs/markdown/customize/agents-md) ·
[Zed instructions](https://raw.githubusercontent.com/zed-industries/zed/main/docs/src/ai/instructions.md) ·
[Zed rules](https://raw.githubusercontent.com/zed-industries/zed/main/docs/src/ai/rules.md) ·
[Letta memory](https://docs.letta.com/agent-sdk/memory/index.md) ·
[Letta MemFS](https://docs.letta.com/concepts/memfs/index.md) ·
[Letta 记忆配置](https://docs.letta.com/configuration/memory/index.md)

**中文产品** — [豆包记忆](https://lf9-cdn-tos.draftstatic.com/obj/ies-hotsoon-draft/grace_legal/doubao_member_copywrite_for_ai.html) ·
[Kimi 记忆（App）](https://www.kimi.com/help/kimi/memory) ·
[Kimi 记忆库（API）](https://platform.kimi.com/docs/hosted-agents/memory.md) ·
[Kimi 梦境](https://platform.kimi.com/docs/hosted-agents/dreams.md) ·
[Qwen Code memory](https://qwenlm.github.io/qwen-code-docs/en/users/features/memory/) ·
[百炼记忆库](https://help.aliyun.com/en/model-studio/memory-library)

---

*参见：[04-research-architectures.md](04-research-architectures.zh-CN.md) 看这些产品部分继承的研究系统，[05-infrastructure.md](05-infrastructure.zh-CN.md) 看下面的存储层，以及 [06-security.md](06-security.zh-CN.md) 看第 1 节那条信任层级为什么是承重的。*
