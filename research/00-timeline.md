# A Timeline of Memory Research, 1885–2026

**Research date:** 2026-11-01

This document is the bird's-eye view. It answers one question: *how did we get
from "memory is a store" to "memory is a decision about what to keep"?*

Read it first. Everything else in `research/` is an expansion of one period in
this timeline.

---

## The four questions

Every memory system — biological, computational, or commercial — has to answer
the same four questions. The history of the field is largely the history of
which question people were working on.

| # | Question | Short name | Where it lives today |
|---|---|---|---|
| 1 | What is stored, and in what substrate? | **representation** | `01-human-memory.md`, `04-research-architectures.md` |
| 2 | What triggers a write? | **encoding policy** | `04-research-architectures.md`, `03-agent-native-memory.md` |
| 3 | How is it reached later? | **index and retrieval** | `05-infrastructure.md` |
| 4 | What makes it go away? | **forgetting policy** | `01-human-memory.md`, `06-security.md` |

A fifth axis cuts across all four: **who owns it and who can see it** —
scope, provenance, and permission. That axis barely existed before 2023 and now
dominates the engineering conversation (`06-security.md`).

The recurring failure mode in both neuroscience and AI is answering one question
and silently defaulting on the other three. A vector database answers (1) and
(3); it has no opinion on (2) or (4). A `CLAUDE.md` file answers (2) by hand and
defaults on (4) entirely. Naming which questions a system has answered is the
most useful analytical move in this repo.

---

## Part I — Measurement, 1885–1950

Before 1885, memory was a philosophical topic. Ebbinghaus made it an
experimental one, and in doing so gave the field its first quantitative law.

| Year | Work | What changed |
|---|---|---|
| 1885 | Ebbinghaus, *Über das Gedächtnis* | Forgetting is measurable. Retention drops steeply then flattens: roughly 58% at 20 minutes, 21% at 31 days. The **savings method** (relearning time saved, not recall accuracy) is the invention — it measures a residue you cannot consciously report. |
| 1900 | Müller & Pilzecker | **Retroactive inhibition**: material learned right after a list damages retention of that list, and sooner is worse. Implies the trace keeps consolidating after encoding — the first statement that *what happens after the write determines what survives*. |
| 1932 | Bartlett, *Remembering* | Memory is **reconstruction**, guided by schemas. Recall fills gaps with prior knowledge rather than replaying a record. |
| 1949 | Hebb, *The Organization of Behavior* | The cellular basis: co-activation strengthens a connection. The ancestor of every embedding-similarity heuristic. |

The through-line: by 1950 the field had established that (a) forgetting follows
a lawful curve, (b) the post-encoding window is fragile, and (c) retrieval is
generative, not playback. All three are still routinely violated by AI memory
systems.

---

## Part II — Architecture, 1950–1975

The cognitive revolution reframed memory as a *system with components*, which is
the frame AI inherited wholesale.

| Year | Work | What changed |
|---|---|---|
| 1968 | Atkinson & Shiffrin | The modal model: sensory register → short-term store → long-term store. Crucially separates **structural features** (fixed architecture) from **control processes** (rehearsal, coding, retrieval strategy). |
| 1972 | Tulving | **Episodic vs semantic** memory. Context-bound events versus context-free knowledge. A functional distinction, not an anatomical one. |
| 1972 | Craik & Lockhart | **Levels of processing**. Durability depends on *depth of encoding*, not repetition. The trace is a by-product of the operations performed. |
| 1974 | Baddeley & Hitch | **Working memory** is not one buffer: phonological loop, visuospatial sketchpad, central executive — specialised stores plus a controller. |
| 1974 | Loftus & Palmer | Post-event wording alters what witnesses report *and later believe*. The query contaminates the store. |

The single most transferable idea here is Atkinson & Shiffrin's split between
architecture and control policy. Almost every AI memory system of 2023–2024
built an architecture and left the policy implicit — which is why they behave
erratically and cannot be tuned.

---

## Part III — Systems, computation, and the first optimality argument, 1975–2000

| Year | Work | What changed |
|---|---|---|
| 1985 / 1995 | Tulving | Remember/know and the **SPI** model: serial encoding, parallel storage, **independent retrieval**. Systems couple on write and decouple on read. |
| 1986 | Teyler & DiScenna | **Hippocampal indexing theory**. The hippocampus stores an *index* to cortical content. The fast structure holds addresses, not payloads. |
| 1991 | Anderson & Schooler | **Need probability**. Recall tracks environmental statistics — frequency, recency, spacing. Memory availability is a statistical estimate of future demand, not an arbitrary decay. |
| 1992 | Bjork & Bjork | **Storage strength vs retrieval strength**. "We still have it" and "we can reach it now" are different variables. Retrieval is itself a learning event. |
| 1995 | McClelland, McNaughton & O'Reilly | **Complementary learning systems**: a fast sparse store (hippocampus) and a slow interleaved store (neocortex). Fast learning in a slow system causes catastrophic interference. |
| 2000 | Baddeley | The **episodic buffer**: a limited-capacity store that *binds* information across systems into unitary episodes. |

Anderson & Schooler is the hinge of this entire repo. It converts "forgetting is
a feature" from a slogan into a testable claim: if your retrieval score is not
an estimate of *P(item needed now | history)*, you are doing keyword search with
extra steps.

---

## Part IV — Active forgetting and the engram, 2000–2016

The field turned from "how is memory stored" to "how is memory removed", and
found that removal is an active process.

| Year | Work | What changed |
|---|---|---|
| 2000 | Nader, Schafe & LeDoux | **Reconsolidation**. Retrieving a memory returns it to a labile state requiring protein synthesis. **Retrieval is a write.** |
| 2005 | Fusi, Drew & Abbott | **Cascade models**. Per-synapse plasticity states produce power-law forgetting: high capacity *and* long retention. Forgetting rate depends on the weight's own history, not wall-clock time. |
| 2012 | Liu et al. | Optogenetic reactivation of an engram ensemble produces recall. The ensemble is a physical handle, not a correlate. |
| 2015 | Nørby | Why forget: emotion regulation, **abstraction**, context attunement. You cannot form a schema while retaining every instance. |
| 2016 | Kumaran, Hassabis & McClelland | CLS updated for AI: replay is **goal-dependent**, not uniform. Replay is a prioritisation policy. |

---

## Part V — Forgetting as regularisation, 2017–2022

This is the period that gave AI research its licence to treat forgetting as a
design goal rather than a defect.

| Year | Work | What changed |
|---|---|---|
| 2017 | Richards & Frankland, *Neuron* | "The goal of memory is not the transmission of information through time… the goal is to **optimize decision-making**. Transience is as important as persistence." Maps biological transience onto machine-learning **regularisation**. |
| 2017 | Davis & Zhong | Three families of forgetting: neurogenesis, interference, **intrinsic**. In *Drosophila*, erasure may be the **default state**; remembering is the active expenditure. |
| 2017 | Roy et al. | **Silent engrams**. After amnesia the memory is still there, inaccessible to natural cues but recoverable optogenetically. **Amnesia ≠ erasure.** |
| 2017 | Kitamura et al. | Cortical engram matures as the hippocampal engram silences — fast store migrating to slow store. |
| 2020 | Josselyn & Tonegawa | Engram review: competitive allocation, engram complexes, artificial and false memories. Allocation is a resource-constrained write policy. |
| 2022 | Ryan & Frankland | Forgetting is **adaptive engram cell plasticity**: the dominant form is loss of *access*, not loss of payload. |
| 2022 | Yalnizyan-Carson & Richards | The computational proof: an RL agent forgetting a large fraction of structured memories performs **better** than an unbounded one. |
| 2022 | Tran et al. | Random reinitialisation of ~3.2% of hidden units acts as a regulariser, trading train accuracy for test accuracy. |

The thesis sentence for the whole repo appears in 2017: **memory exists to
improve decisions, and a perfectly faithful memory is a pathological one.**

---

## Part VI — The LLM agent memory boom, 2023

LLM agents rediscovered long-term memory in 2023, largely without reading
Part V. The first generation was architectural: build a hierarchy, page things
in and out.

| Work | Mechanism | Lineage |
|---|---|---|
| **MemGPT** (arXiv:2310.08560) | OS-style virtual context: main context + recall storage + archival storage, paged by the LLM through function calls; recursive summary at the head of the queue | Atkinson & Shiffrin, one level down |
| **MemoryBank** (arXiv:2305.10250) | Per-memory retention score with exponential decay, reset on access | Ebbinghaus, taken literally |
| **Generative Agents** (arXiv:2304.03442) | Memory stream with **recency × importance × relevance** retrieval, plus periodic reflection | The canonical salience formula that later work inherits |
| **Reflexion** (arXiv:2303.11366) | Verbal self-feedback stored in an episodic buffer | Retrieval as a learning event |
| **Voyager** (arXiv:2305.16291) | Ever-growing library of executable skills | Procedural memory as code |

Note what is missing in 2023: no benchmark, no threat model, no deletion story,
and almost no use of the neuroscience that had spent forty years on exactly
these questions.

---

## Part VII — Retrieval gets serious, 2024

2024 was about *reaching* memory better, and about finally measuring it.

| Work | Mechanism | Why it mattered |
|---|---|---|
| **HippoRAG** (arXiv:2405.14831) | Schema-free knowledge graph + **Personalized PageRank**, seeded on query entities | A single graph step reaches multi-hop evidence; 10–30× cheaper than iterative retrieval |
| **LoCoMo** (arXiv:2402.17753) | Dialogues averaging ~300 turns over up to 35 sessions | First benchmark that forced long-horizon evaluation |
| **LongMemEval** (arXiv:2410.10813) | 500 questions over scalable chat histories; five abilities including **knowledge updates** and **abstention** | Decomposed memory into indexing → retrieval → reading. Became the de facto standard. |
| **Lost in the Middle** (arXiv:2307.03172, TACL 2024) | U-shaped positional curve | "Just use the long context" stops being a serious answer |
| **RULER** (arXiv:2404.06654) | Effective context length is far below advertised | Same conclusion, measured |

---

## Part VIII — Typed memory and the memory operating system, 2025

2025 is the year memory stopped being "a vector store with a prompt" and became
a product surface with a data model. Two developments ran in parallel.

**Research side — the pipeline era.**

| Work | Mechanism |
|---|---|
| **A-MEM** (arXiv:2502.12110, NeurIPS 2025) | Zettelkasten notes with links, plus **memory evolution** — a new note can rewrite existing notes' attributes |
| **Mem0** (arXiv:2504.19413, ECAI 2025) | Write-time extraction / consolidation / retrieval, with a graph variant |
| **Zep / Graphiti** (arXiv:2501.13956) | **Bi-temporal** knowledge graph: valid time and ingestion time; contradiction **invalidates** an edge instead of deleting it |
| **MemoryOS** (arXiv:2506.06326, EMNLP 2025) | STM/MTM/LTM with heat-based promotion and segmented paging |
| **MemOS** (arXiv:2507.03724) | Memory as a schedulable resource; **MemCube** unifies plaintext, activation (KV), and parameter memory |
| **HippoRAG 2** (arXiv:2502.14802, ICML 2025) | Fixes HippoRAG's regression on simple factual recall; frames external memory as **non-parametric continual learning** |
| **MemInsight** (arXiv:2503.21760, EMNLP 2025) | Augments memories with semantic structure at write time |
| **MemGAS** (arXiv:2505.19549) | Multi-granularity memory units with an entropy-based router |
| **Sleep-time compute** (arXiv:2504.13171) | Think offline about a context *before* queries arrive; equal accuracy at ~5× less test-time compute |
| **ACE** (arXiv:2510.04618) | Context as an evolving playbook; names **brevity bias** and **context collapse** |

**Product side — convergence on a shared shape.**

Without coordinating, Claude Code, Qwen Code, Devin, OpenHands, Letta, and the
Kimi API all arrived at the same architecture: **a small always-loaded index
(`MEMORY.md`) plus topic files read on demand, under an explicit character or
line budget.** Claude Code caps the index at 200 lines or 25 KB; Qwen Code at
200 lines / 25,000 code units; OpenHands at ~6,000 characters; Kimi injects at
most 16 KiB each of `README.md` and `index.md`.

That convergence is the most important empirical finding in `03-agent-native-memory.md`, and
it is not an accident: it is the same solution the hippocampus and the
neocortex arrived at — a small fast index in front of a large slow store.

---

## Part IX — Consolidation, governance, and the second half, 2026

The dominant question changed again. It is no longer "how do we store more" but
"**who decides what gets kept, and can we prove the decision was right**".

**Consolidation became a product feature.** Devin ships *Dreaming*, a daily
background pass that merges overlapping notes and removes stale ones. Qwen Code
ships `/dream`. Gemini CLI ships an opt-in Auto Memory that proposes memory
*diffs* for human approval. Letta ships dreaming subagents. GitHub Copilot ships
Dreaming over sessions and PR feedback. Kimi ships 梦境. This is McClelland's
slow store and Diekelmann & Born's sleep, shipped as a cron job.

**Measurement moved from recall to judgement.** New benchmarks stopped asking
"did it remember" and started asking "was keeping it correct":

| Benchmark | What it tests |
|---|---|
| **MemoryAgentBench** (arXiv:2507.05257) | Four competencies including **selective forgetting**; no method masters all four |
| **StructMemEval** (arXiv:2602.11243) | Memory **organization** (ledgers, trees, state tracking), not recall |
| **STALE** (arXiv:2605.06527) | **Implicit conflict** and belief revision with no explicit negation |
| **Memora / FAMA** (arXiv:2604.20006) | Penalises reliance on obsolete memory |
| **AMA-Bench** (arXiv:2602.22769) | Agent–environment trajectories rather than chat |
| **MemGym** (arXiv:2605.20833) | Memory formation *during* execution |

The headline number: on MINTEval (arXiv:2605.18565), seven memory systems
average **27.9% accuracy** on interconnected, frequently-updated facts. The gap
between demo and deployment is now measured, and it is large.

**Security became its own field.** Memory poisoning is a named attack class
distinct from prompt injection: MINJA reports 98.2% injection success with
query-only access (arXiv:2503.03704), MPBench reports 50.46% average attack
success across systems and finds that **prompt-injection defences do not
transfer** (arXiv:2606.04329). Deletion is unsolved: "Ghost Vectors" recovers
25.5% of person names from soft-deleted embeddings (arXiv:2606.18497).

**Standards started, and mostly stalled.** AGENTS.md reached >60k open-source
projects and was donated to the Linux Foundation's Agentic AI Foundation. MCP's
2026-07-28 revision went **stateless** — no session, so a memory server can no
longer assume continuity. A2A explicitly disclaims memory sharing. The IETF
`aimem-bundle` draft and the W3C memory-interoperability group exist; neither
has traction.

---

## The five shifts, stated plainly

If you read nothing else, read this.

**1. From storage to write policy.** The hard problem was never "where do I put
it" — it is "should this have been written at all". 2023 systems wrote
everything; 2026 systems gate writes on surprise, recurrence, or human approval.
The biological analogue is dopaminergic write-enable: commit only when the model
was wrong.

**2. From similarity to need-probability.** Cosine distance answers "what does
this resemble". Memory answers "what will be needed now". Anderson & Schooler
gave the correct objective in 1991; most production systems still optimise the
wrong one.

**3. From deletion to invalidation.** Ryan & Frankland: the dominant biological
failure is unreachable-but-present, not deleted. Graphiti's bi-temporal edges
(`t_valid` / `t_invalid`) are the first mainstream engineering implementation of
that insight — keep both facts, mark the older one invalid, preserve history.
Soft-delete vectors, by contrast, are *worse* than hard delete for privacy,
because the payload is still reconstructible.

**4. From inline to offline consolidation.** Waking is for encoding, sleep for
consolidation; the two modes are incompatible (Rasch & Born 2013). 2026
products independently reinvented this as "dreaming" — a scheduled batch pass
that runs when no session is live.

**5. From silo to provenance-tracked federation.** Agents no longer share one
store; they share *evidence*. Devin's `[source: …; added: …]` per line, Copilot's
citations validated against the current branch, and OpenHands wrapping memory in
`<UNTRUSTED_CONTENT>` are all the same move: make the origin of a memory a
first-class field, because a memory whose origin you cannot check is an attack
surface.

---

## Where the frontier actually is, late 2026

Six open problems that are genuinely unsolved, with the work attacking each:

1. **Consolidation decisions.** When to keep raw, when to compress, when to
   abstract into a rule or skill. `Retain or Consolidate?` (arXiv:2607.17545)
   formalises the trade; `The Epistemics of Agent Memory` (arXiv:2609.33013)
   shows quality scores do not predict transfer (ρ = −0.24).
2. **Belief revision without explicit negation.** STALE (arXiv:2605.06527).
3. **Deletion you can prove.** Ghost Vectors (arXiv:2606.18497), W3C
   cryptographic-erasure proposals.
4. **Procedural memory lifecycle.** Skill libraries silently rot: `Library
   Drift` (arXiv:2605.19576) shows unbounded accumulation degrades retrieval
   while benchmark averages stay flat; LLM-authored skills give +0.0 pp where
   human-curated ones give +16.2 pp.
5. **Memory that survives architecture changes.** Every index is
   rebuildable in principle and rarely rebuilt in practice; portability
   standards exist only on paper.
6. **Knowing when memory is the wrong tool.** A 300-token focused prompt beats
   a 113K-token full history on LongMemEval (Chroma, *Context Rot*). Sometimes
   the correct memory system is a smaller one.

---

*Next: [01-human-memory.md](01-human-memory.md) for the neuroscience that most
of the above is a re-derivation of, or
[03-agent-native-memory.md](03-agent-native-memory.md) for what shipped.*
