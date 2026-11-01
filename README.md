<h1 align="center">Awesome Artificial Memory</h1>

<p align="center">A research library on memory — biological, cognitive, and engineered.</p>

<p align="center">
  <a href="research/README.md">Research</a> ·
  <a href="research/00-timeline.md">Timeline</a> ·
  <a href="README.zh-CN.md">简体中文</a>
</p>

---

## The core idea

Traditional AI optimises for retention. Human intelligence is bound up with the
ability to **selectively forget** — and the last four years of agent engineering
have been a slow rediscovery of that fact. Every serious memory system now has a
write policy, a retrieval policy, and a deletion policy. The good ones know that
the third is the hard one.

The purpose of memory is not to store the past. It is to predict what will be
needed next — and forgetting is what keeps that prediction calibrated.

## What's here

This repository has two layers.

**The curated list** (below) is the original collection: tools, frameworks, and
key papers, kept short and hand-checked.

**The research library** in [`research/`](research/README.md) is the substance —
eight documents, ~7,000 words each, in English and Chinese, tracing memory from
Ebbinghaus to the 2026 agent memory stack.

| Document | What it answers |
|---|---|
| [00 · Timeline](research/00-timeline.md) | How did we get from "memory is storage" to "memory is a decision about what to keep"? 1885 → 2026. |
| [01 · Human memory](research/01-human-memory.md) | How biological memory works — and why forgetting is a mechanism, not a failure. |
| [02 · Cognitive architectures](research/02-cognitive-architectures.md) | What ACT-R, SOAR and global workspace modelled that today's agents skip. |
| [03 · Agent-native memory](research/03-agent-native-memory.md) | What nineteen shipped products actually do, from their own documentation. |
| [04 · Research architectures](research/04-research-architectures.md) | What the literature established, what it only claims, and where the benchmarks came out negative. |
| [05 · Infrastructure](research/05-infrastructure.md) | The storage layer: indexes, filtering, time, tenancy, protocols. |
| [06 · Security](research/06-security.md) | Poisoning, deletion, and isolation — what persistence makes possible. |
| [Sources](research/sources.md) | Every citation with DOIs, plus [ten corrections](research/sources-corrections.md) to widely miscited references. |

Start with the timeline if you want the bird's-eye view. Start with
[03](research/03-agent-native-memory.md) if you are building something this
quarter.

## 🧠 Neuroscience of memory and forgetting

The biological and psychological mechanisms of consolidation and active
forgetting. Full treatment, with 60+ citations, in
[01-human-memory.md](research/01-human-memory.md).

*   **[Book]** [*Why We Forget and How to Remember Better: The Science Behind Memory*](https://www.bc.edu/bc-web/bcnews/science-tech-and-health/psychology/the-science-behind-memory.html) — Budson, A. E., & Kensinger, E. A. (2023), Oxford University Press.
    *   Memory is an active, cyclical process — a structure continually rebuilt, not a static record of the past.
    *   The most useful single book on why forgetting is adaptive rather than defective.
*   **[Paper]** [Replay-triggered brain-wide activation in humans](https://www.nature.com/articles/s41467-024-51582-5) — Huang, Q., Xiao, Z., Yu, Q., et al. (2024), *Nature Communications*.
    *   Consolidation of discrete experiences into a coherent narrative shapes the cognitive map.
    *   Distributed, brain-wide engagement accompanies transient reactivation and sequential replay.
*   **[Paper]** The persistence and transience of memory — Richards, B. A., & Frankland, P. W. (2017), *Neuron* 94(6), 1071–1084.
    *   The standard citation for "forgetting is adaptive", and the one most often miscited.

## 🛠️ Tools and frameworks

Products that give fine-grained control over memory — a prerequisite for
implementing selective forgetting. Nineteen of these, with their actual write
and retrieval policies, are compared in
[03-agent-native-memory.md](research/03-agent-native-memory.md).

| Project | Type | Short notes | Related papers |
|---|---|---|---|
| [Mem0](https://github.com/mem0ai/mem0) | Memory layer / SDK | Self-improving memory layer, hosted + open source; write-time extraction and consolidation. | [Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory](https://arxiv.org/abs/2504.19413) |
| [Zep](https://github.com/getzep/zep) | Memory platform | Bi-temporal knowledge graph over facts and entities; context engineering platform. | [Zep: A Temporal Knowledge Graph Architecture for Agent Memory](https://arxiv.org/abs/2501.13956) |
| [MemoryOS](https://github.com/BAI-LAB/MemoryOS) | Memory OS / architecture | Layered memory (STM/MTM/LPM), heat-score promotion, local-first. | [MemoryOS of AI Agent](https://arxiv.org/abs/2506.06326) |
| [Letta](https://github.com/letta-ai/letta) | Virtual context / stateful agent | Virtual context management and multi-agent memory patterns; the MemGPT line of work. | [MemGPT: Towards LLMs as Operating Systems](https://arxiv.org/abs/2310.08560) |
| [LangChain Memory](https://github.com/langchain-ai/langchain) | Library / integration | Memory components for the LangChain ecosystem. | [LangChain memory overview](https://docs.langchain.com/oss/python/concepts/memory) |

## 🤝 Contributing

Corrections are the most valuable contribution: a wrong DOI, a product claim that
stopped being true, a benchmark number that was superseded. See
[CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT — see [LICENSE](LICENSE).
