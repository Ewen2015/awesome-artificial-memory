# Research: A Complete Map of Memory

**Research date:** 2026-11-01 · **Languages:** English, 简体中文

This directory is a research library on memory, written for people who build
memory systems. It covers human memory, cognitive architectures, shipped agent
memory, research architectures, the storage layer underneath all of them, and the
security consequences of persistence.

It is not a link list. It is one argument, told across eight documents, with
citations.

---

## The argument, in one paragraph

Every memory system — biological or engineered — answers four questions: **what is
stored**, **what triggers a write**, **how it is indexed and retrieved**, and
**what gets forgotten**. Most systems answer one of these deliberately and
silently default on the other three. The good ones are the ones that know which
question they are answering. And the best ones — human memory is the only
existence proof — do something none of the engineered systems do: they **compile**
experience into procedure. Agent memory today accumulates. That gap is the
subject of this library.

---

## Read it in the order that matches your question

**If you want the bird's-eye view.** Start with
[00-timeline.md](00-timeline.md). It runs 1885 → 2026 in nine parts and is written
to be read in twenty minutes. Then stop. You will know where the field is.

**If you are building a memory system this quarter.** Read
[03-agent-native-memory.md](03-agent-native-memory.md) (what nineteen shipped
products actually do, from their own documentation), then
[05-infrastructure.md](05-infrastructure.md) (the storage decisions that will bite
you), then [06-security.md](06-security.md) (the ones that will bite your users).

**If you want to know what the research says and what it doesn't.**
[04-research-architectures.md](04-research-architectures.md). It separates
established results from claimed ones, and includes the 2026 benchmarks whose
headline findings are negative.

**If you came from cognitive science and want to know what AI people get wrong.**
[01-human-memory.md](01-human-memory.md) §7 is a list of twelve specific errors.
[02-cognitive-architectures.md](02-cognitive-architectures.md) explains what the
1970s–2000s architectures did that today's systems skip.

---

## The documents

| # | Document | What it answers | Length |
|---|---|---|---|
| 00 | [00-timeline.md](00-timeline.md) | How did we get from "memory is storage" to "memory is a decision about what to keep"? | 306 |
| 01 | [01-human-memory.md](01-human-memory.md) | How does biological memory actually work, especially forgetting? | 702 |
| 02 | [02-cognitive-architectures.md](02-cognitive-architectures.md) | What did ACT-R, SOAR and global workspace model that LLM agents don't? | 438 |
| 03 | [03-agent-native-memory.md](03-agent-native-memory.md) | What do nineteen shipped products actually do? | 691 |
| 04 | [04-research-architectures.md](04-research-architectures.md) | What has the literature established, and what is only claimed? | 613 |
| 05 | [05-infrastructure.md](05-infrastructure.md) | What breaks in the storage layer? | 448 |
| 06 | [06-security.md](06-security.md) | What does persistence make possible for an attacker? | 420 |
| — | [sources.md](sources.md) | Every citation, with DOIs. | 231 |
| — | [sources-corrections.md](sources-corrections.md) | Ten references that are widely miscited, with the correct form. | 159 |

Each document is ~7,000 words and stands alone. They cross-reference each other,
so reading in order builds; reading one in isolation still works.

---

## Conventions

**Bilingual pairing.** Every document exists twice: `NAME.md` in English and
`NAME.zh-CN.md` in Chinese. They are not translations of each other in the
sentence-by-sentence sense — the Chinese versions are written to read naturally in
Chinese, which means the examples and the rhythm differ in places. The claims,
numbers and citations are identical. `sources.md` and `sources.zh-CN.md` share the
same bibliographic entries by design; only the framing around them is translated.

**Citations.** Journal entries give author, year, title, volume, issue and pages,
with a DOI. AI papers give the arXiv ID. Product claims cite the vendor's own
documentation page. Where a claim has no citation, it is an argument, and it is
written as one.

**What "verified" means here.** Bibliographic data and arXiv IDs were checked
against the publisher or arXiv on 2026-11-01. Product documentation was read from
the vendor's own pages on that date. Reported numbers are the authors' own
reported numbers, not independent replications, and are labelled as such where it
matters. Known citation errors are collected in
[sources-corrections.md](sources-corrections.md) rather than silently fixed.

**Negative results are included on purpose.** A research library that only
reports what worked is a sales document. You will find MINTEval at 27.9%, MPBench
at 50.46% attack success, 25.5% of names recovered from "deleted" vectors, and
Library Drift at +0.0 percentage points. Those numbers are why the other numbers
are worth believing.

**Dates.** Everything here is dated. Memory research moves faster than the
citations in it, so each document carries a research date and the reader should
assume the product landscape has shifted since.

---

## What this library deliberately does not do

It does not rank products. The comparison in
[03-agent-native-memory.md](03-agent-native-memory.md) is descriptive, and the
article's conclusion is structural, not a verdict on any vendor.

It does not propose an architecture. There are eight numbered implications across
the documents and no reference implementation, because the honest state of the
field is that nobody has a validated design.

It does not claim biology is a blueprint. The "mechanism → AI analogue" table in
[01-human-memory.md](01-human-memory.md) §6 says where each analogy holds and
where it breaks, and the breaking points are the more useful half.

---

## Contributing

Corrections are the most valuable contribution. A wrong DOI, a product claim that
stopped being true, a benchmark number that has been superseded — open an issue
with the source. See [../CONTRIBUTING.md](../CONTRIBUTING.md) for the format.

---

*Start here: [00-timeline.md](00-timeline.md) · 中文：[README.zh-CN.md](README.zh-CN.md) · Back to [../README.md](../README.md)*
