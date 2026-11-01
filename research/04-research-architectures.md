# Research Architectures: The Pipeline Era

**Research date:** 2026-11-01

Between 2023 and 2026 the academic literature on LLM memory produced roughly two
dozen named systems, a dozen benchmarks, and one genuinely new idea. This
document separates them.

The genuinely new idea is **write-time consolidation**: the recognition that the
hard problem is not retrieving over a store but deciding what enters it, in what
form, at what granularity. Everything else in this period is a variation on
engineering that predates LLMs — virtual memory, knowledge graphs, PageRank,
caching policy.

---

## 1. The decomposition everyone converged on

By 2025 the literature had settled on a three-stage framing, and it is worth
stating because it is what makes the systems comparable.

**Write.** An event arrives (a turn, a document, an observation). Something
decides whether to store it, what to extract from it, and in what form.

**Manage.** Stored items are organised, merged, linked, decayed, invalidated.
This is where the field's actual novelty lives.

**Read.** A query arrives. Something selects, ranks, and assembles the items into
a prompt.

LongMemEval (Wu et al., arXiv:2410.10813) made the decomposition operational by
scoring indexing, retrieval and reading separately, and by naming the abilities
that actually matter — including **knowledge updates** and **abstention**, both
of which a store-and-retrieve system fails by construction.

The decomposition's value is diagnostic. Read a memory paper and ask which stage
it improves. Most improve one and regress another; almost none report both.

---

## 2. 2023: the architectural generation

### MemGPT — virtual context

Packer, C., Wooders, S., Lin, K., Fang, V., Patil, S. G., Stoica, I., &
Gonzalez, J. E. (2023). *MemGPT: Towards LLMs as Operating Systems*.
arXiv:[2310.08560](https://arxiv.org/abs/2310.08560).

The framing move: treat the context window as **main memory** and the model as a
process that can page. Two external tiers — *recall storage* (recent history,
searchable) and *archival storage* (arbitrary, searchable) — plus *core memory*
(always in context) and a **queue manager** that holds a recursive summary at the
head of the message buffer.

The important contribution is not the tiers, which are ordinary. It is that the
**model itself issues the paging decisions through function calls**. Memory
management becomes an agent behaviour rather than a scheduler's job. That is the
idea 2024–2026 products inherited, mostly without citing it.

What it does not do: nothing decides what is worth writing; the model decides to
write when it decides to. There is no consolidation, no invalidation, and no
notion of forgetting beyond "it wasn't paged in."

### MemoryBank — Ebbinghaus taken literally

Zhong, W., Guo, L., Gao, Q., Ye, H., & Wang, Y. (2023). *MemoryBank: Enhancing
Large Language Models with Long-Term Memory*.
arXiv:[2305.10250](https://arxiv.org/abs/2305.10250).

Each memory carries a **retention score** that decays exponentially with time
since last access and is **reset on recall**. Users can also ask the system for a
"memory summary" of a period, modelled loosely on the forgetting curve's shape.

It is the most direct transplantation of Ebbinghaus in the literature, and it is
instructive precisely because it transplants the wrong part. Ebbinghaus measured
retention under **no rehearsal and no retrieval**; the interesting case is the
one where use changes the curve. The reset-on-access rule is the right intuition
but it is a boolean, not the power-law accumulation
[01-human-memory.md](01-human-memory.md) §2 describes.

The design is also notable for what it assumes: that a scalar per memory is
enough. No provenance, no conflict handling, no scope.

### Generative Agents — the salience formula

Park, J. S., O'Brien, J. C., Cai, C. J., Morris, M. R., Liang, P., &
Bernstein, M. S. (2023). *Generative Agents: Interactive Simulacra of Human
Behavior*. arXiv:[2304.03442](https://arxiv.org/abs/2304.03442).

Twenty-five agents in a sandbox town, each with a **memory stream** of
natural-language observations. Retrieval scores each memory as

```
score = α_recency · recency + α_importance · importance + α_relevance · relevance
```

where recency is exponential decay (~0.995 per hour), importance is an LLM-rated
1–10, and relevance is embedding similarity to the query. A **reflection** process
periodically synthesises higher-level observations from recent memories, which
are then stored back into the same stream.

This formula became the default in the field and is worth examining as a design
artefact.

*It is ACT-R's activation with different terms.* Three signals, additive in
log-odds, combining time, value, and cue match. The convergence is not
coincidence — it is the minimal set of variables needed to predict what will be
needed.

*Importance is an LLM judgement, not a learned quantity.* Human importance is
dopaminergic and tied to prediction error
([01-human-memory.md](01-human-memory.md) §4). An LLM rating "how important is
this" is a different computation, and it is the one component of the formula that
is not measurable against anything.

*Reflection is the first write-time consolidation in this literature.* It creates
new items derived from old ones, at a higher level of abstraction. Every
"dreaming" product feature descends from it.

*The ablation is the finding.* Removing any one of the three terms degrades
behaviour; removing reflection degrades it most. Salience is not one knob.

### Reflexion — retrieval as learning

Shinn, N., Cassano, F., Berman, E., Gopinath, A., Narasimhan, K., & Yao, S.
(2023). *Reflexion: Language Agents with Verbal Reinforcement Learning*.
arXiv:[2303.11366](https://arxiv.org/abs/2303.11366).

After a failed trial, the agent writes a **verbal self-reflection** into an
episodic buffer and retries. No gradient update, no weight change; the memory *is*
the learning mechanism.

This is the cleanest demonstration that Nader's reconsolidation finding
([01-human-memory.md](01-human-memory.md) §3) has an engineering analogue:
retrieval of a failure, articulated, changes future behaviour. It is also the
first mainstream system where **the write is gated on failure** — a partial return
of SOAR's impasse rule.

What it lacks: the reflections are never consolidated, never deduplicated, never
invalidated. Long runs accumulate contradictory advice.

### Voyager — procedural memory as code

Wang, G., Xie, Y., Jiang, Y., Mandlekar, A., Xiao, C., Zhu, Y., Fan, L., &
Anandkumar, A. (2023). *Voyager: An Open-Ended Embodied Agent with Large Language
Models*. arXiv:[2305.16291](https://arxiv.org/abs/2305.16291).

An ever-growing **library of executable skills** in Minecraft, each a JavaScript
function with a natural-language description, retrieved by description embedding,
with a self-verification loop that only commits a skill after it works.

This is procedural memory in the strict sense — the artefact is *code*, and its
correctness is *executed*, not asserted. It is also the first system to make
memory's growth a liability: skills accumulate, descriptions collide, and nothing
prunes. `Library Drift` (arXiv:2605.19576, §6) later measured exactly this.

---

## 3. 2024: retrieval and measurement

### HippoRAG — PageRank as pattern completion

Gutiérrez, B. J., Shu, Y., Gu, Y., Yasunaga, M., & Su, Y. (2024). *HippoRAG:
Neurobiologically Inspired Long-Term Memory for Large Language Models*.
arXiv:[2405.14831](https://arxiv.org/abs/2405.14831).

Build a schema-free knowledge graph from an OpenIE pass, then answer a query by
seeding **Personalized PageRank** on the query's entities and retrieving the
passages attached to high-rank nodes.

The biological claim is precise and worth stating correctly: the graph plus PPR is
offered as an analogue of **hippocampal indexing** — a sparse structure whose
activation reinstates distributed content from a partial cue — and of **pattern
completion**, where a partial input retrieves a whole stored pattern.

The engineering result is that **one graph step reaches multi-hop evidence** that
iterative retrieval needs several LLM calls to find, at 10–30× lower cost. That
is a real result: graph structure substitutes for inference.

The weakness, which HippoRAG 2 fixed: on simple single-fact recall it regresses
against plain RAG, because graph construction loses information and PPR dilutes
the strong direct match.

### HippoRAG 2 — non-parametric continual learning

Gutiérrez, B. J., Shu, Y., Qi, W., Zhou, S., & Su, Y. (2025). *From RAG to
Memory: Non-Parametric Continual Learning for Large Language Models*.
arXiv:[2502.14802](https://arxiv.org/abs/2502.14802). ICML 2025.

Passage nodes and phrase nodes in one graph, with recognition memory (filter
irrelevant passages) and associative memory (traverse), so that simple recall and
multi-hop reasoning improve together instead of trading off.

The framing is the more important contribution. Calling external memory
**non-parametric continual learning** places it correctly: it is the mechanism by
which a frozen model acquires new knowledge without gradient updates, and it
should be evaluated the way continual learning is evaluated — on retention,
interference, and forgetting, not only on recall.

### LoCoMo and LongMemEval — the measurement turn

Maharana, A., Lee, D.-H., Tulyakov, S., Bansal, M., Barbieri, F., & Fang, Y.
(2024). *Evaluating Very Long-Term Conversational Memory of LLM Agents*.
arXiv:[2402.17753](https://arxiv.org/abs/2402.17753).

Dialogues averaging ~300 turns across up to 35 sessions, with question types
covering single-hop, multi-hop, temporal and open-domain, plus a multimodal
variant. The first benchmark that made short-context tricks visibly insufficient.

Wu, D., Wang, H., Yu, W., Zhang, Y., Chang, K.-W., & Yu, D. (2024).
*LongMemEval: Benchmarking Chat Assistants on Long-Term Interactive Memory*.
arXiv:[2410.10813](https://arxiv.org/abs/2410.10813).

500 curated questions over scalable chat histories, testing five abilities:
information extraction, multi-session reasoning, **temporal reasoning**,
**knowledge updates**, and **abstention**. It scores indexing, retrieval and
reading separately.

Two of those five are the important ones. *Knowledge updates* forces the system to
hold a revised fact and stop using the old one — the belief-revision problem.
*Abstention* forces it to say "I don't know," which is ACT-R's retrieval-threshold
failure
([02-cognitive-architectures.md](02-cognitive-architectures.md) §2) and which no
store-and-retrieve system does by default.

### Lost in the Middle and RULER — why the context window isn't memory

Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., &
Liang, P. (2024). *Lost in the Middle: How Language Models Use Long Contexts*.
*TACL*, 12, 157–173. arXiv:[2307.03172](https://arxiv.org/abs/2307.03172).

Performance follows a U-shaped curve in the position of the relevant passage:
best at the beginning and end, worst in the middle. The degradation is not a
capacity limit; it is a utilisation limit.

Hsieh, C.-P., Sun, S., Kriman, S., Acharya, S., Rekesh, D., Jia, F., & Ginsburg,
B. (2024). *RULER: What's the Real Context Size of Your Long-Context Language
Models?* arXiv:[2404.06654](https://arxiv.org/abs/2404.06654).

Effective context length — the length at which performance stays above a
threshold — is far below the advertised window, and only a handful of models
retain usable performance at their claimed maximum.

Together these two settle the argument that made the field build memory systems
instead of buying bigger windows. The relevant statement is not "context is too
small" but "**position and distractor density determine utilisation, and both get
worse with length**."

---

## 4. 2025: typed memory and write-time consolidation

This is the year the field's centre of gravity moved from retrieval to the write
path.

### A-MEM — Zettelkasten with memory evolution

Xu, W., Liang, Z., Mei, K., Gao, H., Tan, J., & Zhang, Y. (2025). *A-MEM: Agentic
Memory for LLM Agents*. arXiv:[2502.12110](https://arxiv.org/abs/2502.12110).
NeurIPS 2025.

Each memory is a **note** with structured attributes (keywords, tags, contextual
description, links), created through an atomic note-construction step. The
distinctive mechanism is **memory evolution**: when a new note is added, the
system checks existing notes for relatedness and **may rewrite their attributes
and links** — context, tags, and connections — rather than only adding a new node.

This is the most important conceptual move of 2025, and it is worth naming
precisely: **memory evolution makes writes non-local.** Inserting a note can
change what other notes mean. That is exactly Bartlett's reconstructive memory
([01-human-memory.md](01-human-memory.md) §1) and exactly what a key-value store
cannot do.

The risk it introduces is equally important: a write can now invalidate prior
work, so the write path needs a conflict model. A-MEM's handling is heuristic and
its evaluation does not measure regression on old items — which is why
StructMemEval (arXiv:2602.11243, §6) had to be built.

### Mem0 — extraction and consolidation as a pipeline

Chhikara, P., Khant, D., Aryan, S., Singh, T., & Yadav, D. (2025). *Mem0: Building
Production-Ready AI Agents with Scalable Long-Term Memory*.
arXiv:[2504.19413](https://arxiv.org/abs/2504.19413). ECAI 2025.

A two-phase pipeline. **Extraction**: an LLM reads a turn-pair and emits candidate
facts. **Update**: each candidate is compared against similar existing memories
and one of ADD / UPDATE / DELETE / NOOP is chosen. A graph variant stores
entities and relations for multi-hop.

The reported results — memory built from a 26k-token conversation answered at
~90% lower token cost than full-context, and materially faster than a
full-context baseline — are less important than the operational claim: **memory
as an extraction pipeline is cheap enough to run on every turn.**

That is the claim most production systems adopted. It is also the source of the
field's biggest unexamined assumption: that the right unit of memory is a
sentence-shaped fact.

### Zep / Graphiti — bi-temporal invalidation

Rasmussen, P., Paliychuk, P., Beauvais, M., Ryan, J., & Chalef, D. (2025). *Zep:
A Temporal Knowledge Graph Architecture for Agent Memory*.
arXiv:[2501.13956](https://arxiv.org/abs/2501.13956).

A **temporally aware knowledge graph** in which every edge carries both **valid
time** (when the fact was true in the world) and **ingestion time** (when the
system learned it). When a new fact contradicts an old one, the old edge is
**invalidated** — `t_invalid` is set — rather than deleted. Episodes are retained
as raw provenance; extracted entities and relations are derived.

Bi-temporality is the single best engineering idea in this literature, and it is
the direct implementation of two findings from
[01-human-memory.md](01-human-memory.md): forgetting is loss of access rather than
loss of payload (Ryan & Frankland), and the question "what did we believe at time
T" is different from "what is true at time T."

The reported result on DMR (94.8% vs 93.4% for MemGPT) and LongMemEval
(71.2% vs 60.2%) is real but should be read with the caveat that Zep authors
benchmarked their own system.

### MemoryOS — heat-based paging

Kang, J., Ji, M., Zhao, Z., & Bai, T. (2025). *MemoryOS of AI Agent*.
arXiv:[2506.06326](https://arxiv.org/abs/2506.06326). EMNLP 2025.

Short-term / mid-term / long-term tiers with **heat-based promotion** — access
frequency and recency raise an item's heat, and heat drives migration between
tiers — plus **segmented paging** that moves whole dialogue segments rather than
individual turns.

Two ideas worth keeping. *Heat as a promotion signal* is the closest thing in the
literature to a working set policy, and it is the correct place to apply Bjork's
retrieval-strength rule — use should raise accessibility.
*Segment-level paging* acknowledges that the unit of memory is not always the
turn; a conversation has internal structure and moving half of it breaks it.

### MemOS — memory as an operating-system resource

Li, Z., Song, S., Xi, C., Wang, H., et al. (2025). *MemOS: A Memory OS for AI
System*. arXiv:[2507.03724](https://arxiv.org/abs/2507.03724).

Introduces the **MemCube**, a container that unifies three substrates:
**plaintext** memory (retrievable text), **activation** memory (KV cache state),
and **parameter** memory (weights or adapters). Memory becomes a schedulable,
transferable resource with lifecycle — generation, merging, migration, eviction.

Two things are genuinely new here.

*Activation memory as a first-class citizen.* A KV cache is a memory: it is
content-addressed, it can be persisted and reloaded, and holding it is what makes
prefix caching cheap. Treating it as memory rather than as a runtime optimisation
is the correct abstraction and almost nobody else does it.

*Memory governance as a scheduling problem.* If memories have different
substrates and costs, admitting one is a resource decision, not a storage decision.
That reframing is what makes "which memory should this agent have" a tractable
question.

### MemInsight, MemGAS, and the granularity problem

Salama, R., Cai, J., Yuan, M., Currey, A., Sunkara, M., Zhang, Y., & Benajiba, Y.
(2025). *MemInsight: Autonomous Memory Augmentation for LLM Agents*.
arXiv:[2503.21760](https://arxiv.org/abs/2503.21760). EMNLP 2025.

Augments memories at write time with semantic structure — entity and attribute
annotations — so that retrieval can match on structure rather than surface
similarity. The gain is largest where the query's wording differs from the
memory's.

Xu, W., et al. (2025). *MemGAS: Multi-Granularity Memory with Entropy-Based
Routing*. arXiv:[2505.19549](https://arxiv.org/abs/2505.19549).

Stores each memory at several granularities (turn, session, topic, summary) and
routes a query to the appropriate granularity using an **entropy-based** measure
of how specific the query is.

Both are attacking the same under-discussed problem: **what is the unit of
memory?** A turn? A fact? A session? The literature mostly assumes a fixed unit
and then tunes retrieval. These two make the unit a decision — which is right, and
which connects directly to Cowan's chunking
([02-cognitive-architectures.md](02-cognitive-architectures.md) §7): humans exceed
capacity by changing the unit, not by enlarging the buffer.

### Sleep-time compute — thinking before the question

Lin, K., Snell, J., Wang, Y., Packer, C., Wooders, S., Stoica, I., & Gonzalez,
J. E. (2025). *Sleep-time Compute: Beyond Inference Scaling at Test-time*.
arXiv:[2504.13171](https://arxiv.org/abs/2504.13171).

Pre-compute, offline and before any query arrives, derived facts and reasoning
about a context. At query time the system reads the pre-computed state instead of
the raw context.

Reported: equal accuracy at roughly **5× less test-time compute**, with larger
gains on queries that are predictable in advance.

This is Rasch & Born's incompatible-modes argument
([01-human-memory.md](01-human-memory.md) §3) turned into a serving strategy:
encoding-time and consolidation-time work are different work, and moving the
second off the critical path is free performance. That it was published as an
inference-optimisation paper rather than a memory paper is a symptom of how
fragmented this field is.

### ACE — context as an evolving playbook

Zhang, Q., Hu, C., Upasani, S., Ma, B., Hong, F., Kamanuru, V., et al. (2025).
*Agentic Context Engineering: Evolving Contexts for Self-Improving Language
Models*. arXiv:[2510.04618](https://arxiv.org/abs/2510.04618).

Treats the context as a **playbook** that evolves through a generate–reflect–curate
loop, with incremental delta updates rather than monolithic rewrites, and a
deduplication step.

Two named pathologies, and these are the paper's real contribution.

**Brevity bias.** When an LLM is asked to rewrite a long context, it
systematically compresses away domain-specific detail in favour of generic
advice. Every summarisation-based consolidation inherits this — which means the
"dreaming" passes in [03-agent-native-memory.md](03-agent-native-memory.md) §3.4
are quietly deleting exactly the specific knowledge that made the memory useful.

**Context collapse.** Repeated full rewrites cause progressive loss of detail
until the context converges to a short generic summary. This is the failure mode
of every system that consolidates by summarising its own summary.

The fix — **delta updates and a curation step** rather than rewrite — is
under-implemented and should be standard.

---

## 5. The mechanism families, stated once

Grouping the literature by mechanism rather than by name is the only way to see
what has actually been tried.

| Family | Representative systems | Core move | Structural weakness |
|---|---|---|---|
| **Paging** | MemGPT | Model issues function calls to move content between context and external store | No write policy; no consolidation |
| **Decay scoring** | MemoryBank, Generative Agents (recency term) | Scalar per item, decays with time, reset on access | Time is the wrong variable; no provenance |
| **Salience retrieval** | Generative Agents | Recency × importance × relevance | Importance is an unfalsifiable LLM judgement |
| **Link/edit graph** | A-MEM | Notes with links; new notes rewrite existing notes | Non-local writes need a conflict model it doesn't have |
| **Write-time extraction** | Mem0, MemInsight | LLM extracts facts at write; ADD/UPDATE/DELETE against neighbours | Assumes memory is sentence-shaped facts |
| **Bi-temporal graph** | Zep / Graphiti | Valid time + ingestion time; contradict → invalidate | Extraction cost; graph quality gates everything |
| **Heat paging** | MemoryOS | Access raises heat, heat promotes between tiers | Heat is a proxy, not a need estimate |
| **Substrate unification** | MemOS | Plaintext + activation + parameter memory as one schedulable resource | Governance overhead; unclear what the scheduler optimises |
| **Graph random walk** | HippoRAG 1/2 | Schema-free graph + Personalized PageRank from query entities | Graph construction loss; regresses on simple recall (fixed in v2) |
| **Granularity routing** | MemGAS, MemInsight | Store at multiple levels; route by query specificity | Router errors are unrecoverable |
| **Offline pre-compute** | sleep-time compute | Consolidate before the query exists | Only helps for predictable queries |
| **Delta curation** | ACE | Incremental edits, not rewrites; explicit dedup | Requires a curation policy that is itself LLM-driven |

**The one thing absent from the table** is a system where a memory stops being
retrieved because it has been *compiled into a procedure*. Voyager comes closest
— skills are executable — but nothing there demotes the episodes that produced
them, and nothing triggers compilation on repetition.

---

## 6. What is actually established, versus claimed

The literature is full of numbers. Very few of them survive scrutiny, and the
ones that do point the same direction.

**Established.**

*Graph structure substitutes for inference.* HippoRAG's 10–30× cost reduction for
multi-hop is a real result and it replicates: one well-placed traversal beats
several LLM calls. The corollary is that graphs do not help simple recall, which
HippoRAG 2 concedes and fixes.

*Write-time consolidation beats retrieve-time assembly.* Every system that adds a
write-time step (Mem0, A-MEM, MemInsight) reports gains over a store-and-retrieve
baseline, and the gains are largest on queries whose wording differs from the
stored text.

*Long context is not a substitute for memory.* Lost in the Middle and RULER both
show utilisation degrading with length and position; the focused-prompt result
(§9) shows a 300-token prompt beating a 113K-token history.

*Forgetting is a real design axis.* MemoryAgentBench (arXiv:2507.05257) tests
**selective forgetting** as one of four competencies and finds that **no method
masters all four** — the systems good at retention are bad at forgetting, and vice
versa. That is a genuine trade-off, measured.

**Claimed but weak.**

*Benchmark gains within a single paper's own suite.* Most of these papers
benchmark on LoCoMo or LongMemEval and report against a baseline they configured.
The MINTEval result (arXiv:2605.18565) — seven memory systems averaging **27.9%**
on interconnected, frequently-updated facts — is the corrective: on a benchmark
nobody tuned against, the systems are far weaker than their own papers suggest.

*"Production-ready".* Mem0's title claims it; the honest reading is that the
architecture is production-shaped, which is different from being validated in
production.

*Latency and cost numbers.* These are almost always measured on the authors'
hardware against a baseline that may be handicapped. Treat them as existence
proofs, not as comparisons.

**Not established at all.**

Whether any of this composes. Nobody has published a system that is good at
extraction, organisation, retrieval, forgetting, and belief revision at once —
and MemoryAgentBench's four-way trade-off suggests nobody can be, without
explicitly scheduling between them.

---

## 7. The 2026 benchmarks: from recall to judgement

The measurement question changed. Earlier benchmarks asked "did it remember";
the 2026 generation asks "**was keeping it correct**."

| Benchmark | arXiv | What it isolates |
|---|---|---|
| **MemoryAgentBench** | [2507.05257](https://arxiv.org/abs/2507.05257) | Four competencies incl. **selective forgetting**; no method masters all |
| **StructMemEval** | [2602.11243](https://arxiv.org/abs/2602.11243) | Memory **organisation** — ledgers, trees, state tracking — not recall |
| **STALE** | [2605.06527](https://arxiv.org/abs/2605.06527) | **Implicit conflict** and belief revision with no explicit negation |
| **Memora / FAMA** | [2604.20006](https://arxiv.org/abs/2604.20006) | Penalises reliance on **obsolete** memory |
| **AMA-Bench** | [2602.22769](https://arxiv.org/abs/2602.22769) | Agent–environment **trajectories**, not chat |
| **MemGym** | [2605.20833](https://arxiv.org/abs/2605.20833) | Memory formation **during** execution |
| **MINTEval** | [2605.18565](https://arxiv.org/abs/2605.18565) | Interconnected, frequently-updated facts → **27.9%** across seven systems |
| **Library Drift** | [2605.19576](https://arxiv.org/abs/2605.19576) | Unbounded skill accumulation; +0.0 pp LLM-authored vs +16.2 pp curated |

Three of these are worth a paragraph each because they name problems the field
had not previously been able to state.

**STALE is the belief-revision benchmark.** It tests cases where a new statement
contradicts an old one *without saying so* — "I moved to Berlin" after "I live in
Paris", with no negation. Detecting the conflict requires understanding, not
string matching, and it is the failure mode every ADD/UPDATE/DELETE classifier
gets wrong. This is the operational form of Zep's invalidation problem.

**StructMemEval is the organisation benchmark.** It asks whether the memory holds
a *structure* — a ledger with entries, a tree of states, a running total — rather
than whether it can recount facts. This matters because a huge class of agent
tasks (bookkeeping, state tracking, debugging) fail not on recall but on structure
maintenance, and no memory system is evaluated on it.

**Library Drift is the accumulation benchmark.** Unbounded skill accumulation
degrades retrieval while benchmark averages stay flat — the failure is invisible
to the metric everyone reports. And the sharp number: LLM-authored skills give
**+0.0 pp** over no skills, where human-curated ones give **+16.2 pp**. The
obvious reading is that self-authored procedural memory does not work yet; the
deeper reading is that *nobody has a compilation policy*, which is why
self-authored skills are indistinguishable from noise.

---

## 8. Two papers that reframe the problem

Neither is a system paper, and both are more useful than most system papers.

**`Retain or Consolidate?`** (arXiv:2607.17545) formalises the trade-off at the
heart of every write path: keep the raw episode, or replace it with a
consolidated derivative? Retaining preserves detail and provenance but grows
without bound; consolidating compresses but commits to a lossy choice that cannot
be undone. The paper's contribution is showing this is a **decision-theoretic**
problem — the correct choice depends on the expected future query distribution —
rather than a heuristic to be tuned.

**`The Epistemics of Agent Memory`** (arXiv:2609.33013) reports the result that
should worry everyone building quality scores into a memory system: **quality
scores do not predict transfer** (ρ = −0.24). A memory judged high-quality by an
LLM judge is not more likely to improve downstream task performance. The
correlation is *negative*.

If that replicates, it invalidates a whole class of mechanisms — Copilot's voting,
importance ratings in Generative Agents, every "rate this memory" step in a
consolidation pass. The paper's interpretation is that quality and usefulness are
different axes: a memory can be accurate, well-written, and useless, and judges
score the first two because they can see them.

---

## 9. Where the research actually is

A short, honest inventory.

**Solved well enough to build on.** Graph retrieval for multi-hop (HippoRAG 2).
Bi-temporal invalidation (Zep). Write-time extraction as a cost-effective default
(Mem0). Offline pre-compute (sleep-time compute). Delta curation over rewrite
(ACE).

**Understood but not solved.** Belief revision without explicit negation
(STALE). Consolidation granularity. Memory organisation as opposed to recall
(StructMemEval). The four-way competency trade-off (MemoryAgentBench).

**Not solved, and arguably not yet properly posed.** Deletion you can prove.
Cross-agent memory. Compilation from episodic to procedural. Knowing when memory
is the wrong tool — the focused-prompt result
(Chroma, *Context Rot*: a ~300-token focused prompt beats a 113K-token full
history on LongMemEval) is not a memory result, it is a warning that memory can
be net-negative.

**The gap between this literature and the products.** The products in
[03-agent-native-memory.md](03-agent-native-memory.md) are, with the exceptions of
MemGPT's descendant (Letta) and Zep, largely *not* built on this research. They
converged independently on a simpler architecture — index plus topic files — that
no paper in this document proposes. It works better. That is worth sitting with:
the field's most successful memory design came from product engineering, and the
literature has not yet explained why it wins.

The likely explanation is the one this document opened with. The index architecture
solves the *interface* problem — what does the model see at session start — and
the research literature was solving the *representation* problem. Interfaces
matter more, because they are what the model actually experiences.

---

*See also: [02-cognitive-architectures.md](02-cognitive-architectures.md) for the
tradition these systems partly re-derive, [03-agent-native-memory.md](03-agent-native-memory.md)
for the products, and [00-timeline.md](00-timeline.md) for the chronology.*
