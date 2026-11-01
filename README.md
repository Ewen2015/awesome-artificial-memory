<h1 align="center">Awesome Artificial Memory</h1>

<p align="center">A research library on memory — biological, cognitive, and engineered.</p>

<p align="center">
  <a href="00-timeline.md">Timeline</a> ·
  <a href="03-agent-native-memory.md">Agent memory</a> ·
  <a href="sources.md">Sources</a> ·
  <a href="README.zh-CN.md">简体中文</a>
</p>

---

## What this is

One argument about memory, told in eight documents, with citations. It starts with
Ebbinghaus in 1885 and ends with the agent memory stack of late 2026, and it covers
human memory, cognitive architectures, nineteen shipped products, the research
literature, the storage layer underneath all of them, and what persistence makes
possible for an attacker.

It is not a link list. Every document answers the same four questions — **what is
stored**, **what triggers a write**, **how it is indexed and retrieved**, **what
gets forgotten** — plus a fifth axis that cuts across all of them: who owns it, and
who is allowed to see it.

## The core idea

Traditional AI optimises for retention. Human intelligence is bound up with the
ability to **selectively forget** — and the last four years of agent engineering
have been a slow rediscovery of that fact. Every serious memory system now has a
write policy, a retrieval policy, and a deletion policy. The good ones know that
the third is the hard one.

The purpose of memory is not to store the past. It is to predict what will be
needed next — and forgetting is what keeps that prediction calibrated.

And the thing the best system does that no engineered one does: human memory
**compiles**. It turns repeated episodes into procedure, and then stops needing the
episodes. Agent memory today accumulates. That gap is the subject of this library.

## Read it in the order that matches your question

**If you want the bird's-eye view.** Start with [00-timeline.md](00-timeline.md).
1885 → 2026 in nine parts, twenty minutes. Then stop — you will know where the
field is.

**If you are building a memory system this quarter.** [03-agent-native-memory.md](03-agent-native-memory.md)
for what nineteen shipped products actually do, from their own documentation;
[05-infrastructure.md](05-infrastructure.md) for the storage decisions that will
bite you; [06-security.md](06-security.md) for the ones that will bite your users.

**If you want to know what the research says and what it doesn't.**
[04-research-architectures.md](04-research-architectures.md) separates established
results from claimed ones, and includes the 2026 benchmarks whose headline findings
are negative.

**If you came from cognitive science and want to know what AI people get wrong.**
[01-human-memory.md](01-human-memory.md) §7 is a list of twelve specific errors.
[02-cognitive-architectures.md](02-cognitive-architectures.md) explains what the
1970s–2000s architectures did that today's systems skip.

## The documents

| # | Document | What it answers | Words |
|---|---|---|---|
| 00 | [Timeline](00-timeline.md) | How did we get from "memory is storage" to "memory is a decision about what to keep"? | ~4,500 |
| 01 | [Human memory](01-human-memory.md) | How biological memory works — and why forgetting is a mechanism, not a failure. | ~10,000 |
| 02 | [Cognitive architectures](02-cognitive-architectures.md) | What ACT-R, SOAR and global workspace modelled that today's agents skip. | ~6,500 |
| 03 | [Agent-native memory](03-agent-native-memory.md) | What nineteen shipped products actually do, from their own documentation. | ~10,000 |
| 04 | [Research architectures](04-research-architectures.md) | What the literature established, what it only claims, and where the benchmarks came out negative. | ~9,000 |
| 05 | [Infrastructure](05-infrastructure.md) | The storage layer: indexes, filtering, time, tenancy, protocols. | ~6,500 |
| 06 | [Security](06-security.md) | Poisoning, deletion, and isolation — what persistence makes possible. | ~6,000 |
| — | [Sources](sources.md) | Every citation, with DOIs. | ~3,000 |
| — | [Citation corrections](sources-corrections.md) | Ten references that are widely miscited, with the correct form. | ~2,000 |

Each document stands alone and cross-references the others, so reading in order
builds while reading one in isolation still works. Every document exists in
English and in Chinese (`NAME.zh-CN.md`).

## Products covered

The comparison in [03-agent-native-memory.md](03-agent-native-memory.md) covers
nineteen memory systems, read from each vendor's own documentation on 2026-11-01:
Claude Code, ChatGPT, Copilot, Cursor, Devin, Windsurf, OpenHands, Letta,
LangChain/LangGraph, LlamaIndex, Mem0, Zep, MemoryOS, MemOS, A-MEM, Roo Code, Qwen
Code, Kimi, and Doubao. The chapter's conclusion is structural, not a ranking —
they converged independently on an index plus topic files, and none of them
compiles.

Four products were dropped during the survey because their documentation had
moved, disappeared, or could not be retrieved: see §8 of that document for the
reasoning rather than a third-party reconstruction.

## Conventions

**Bilingual pairing.** Every document exists twice. The Chinese versions are
written to read naturally in Chinese rather than translated sentence by sentence,
so examples and rhythm differ in places; claims, numbers and citations are
identical.

**Citations.** Journal entries give author, year, title, volume, issue and pages,
with a DOI. AI papers give the arXiv ID. Product claims cite the vendor's own
documentation page. An uncited claim is written as an argument, not as a finding.

**Negative results are included on purpose.** MINTEval at 27.9%, MPBench at
50.46% attack success, 25.5% of person names recovered from "deleted" vectors,
Library Drift at +0.0 percentage points. Those numbers are why the other numbers
are worth believing.

**Everything is dated.** Each document carries a research date. Memory research
moves faster than the citations in it, and the product landscape in particular
should be assumed to have shifted since 2026-11-01.

## What this deliberately does not do

It does not rank products, propose an architecture, or claim biology is a
blueprint. There are eight numbered implications across the documents and no
reference implementation, because the honest state of the field is that nobody has
a validated design. The "mechanism → AI analogue" table in
[01-human-memory.md](01-human-memory.md) §6 says where each analogy holds and where
it breaks, and the breaking points are the more useful half.

## Contributing

Corrections are the most valuable contribution: a wrong DOI, a product claim that
stopped being true, a benchmark number that was superseded. See
[CONTRIBUTING.md](CONTRIBUTING.md).

## Licence

MIT — see [LICENSE](LICENSE).
