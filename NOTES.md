# Research notes

Working notes for the `research/` library. The documents themselves are in
[`research/`](research/README.md); this file records how they were built, what
was dropped, and what is still open.

**Last full pass:** 2026-11-01

---

## Method

The library was built question-first, not source-first. Before collecting
anything, one frame was fixed: every memory system answers four questions — what
is stored, what triggers a write, how it is indexed and retrieved, what gets
forgotten — plus a fifth cross-cutting axis of ownership, provenance and
permission. Every document is organised around that frame. This is why the
documents argue with each other rather than merely sitting side by side.

Sources were gathered in four separate passes (human memory, agent-native
products, research architectures, infrastructure and standards), each written up
before the next began, so that later passes could be checked against earlier
claims rather than the reverse.

Product claims were read from each vendor's own documentation on the research
date. Where a vendor's documentation could not be retrieved, the product was
dropped rather than reconstructed from third-party coverage — see the appendix at
the end of [research/03-agent-native-memory.md](research/03-agent-native-memory.md).

## What was dropped, and why

Four products were removed during the survey because their docs had moved,
disappeared or become unrecoverable: Windsurf's memory documentation (folded into
Devin), Roo Code's (withdrawn entirely), and two documentation hosts that
relocated mid-research (OpenAI's help centre, Gemini CLI's). Third-party write-ups
were not used as substitutes. The rule: a product is in the comparison because its
own documentation says what it does, not because someone blogged about it.

## Known soft spots

**Infrastructure is written at the level of mechanism, not measurement.** The
storage chapter describes filter semantics, index trade-offs and temporal
modelling, and cites canonical documentation, rather than quoting per-vendor
latency and recall numbers. Those numbers are not stable enough to publish without
benchmarking them ourselves, and this repository does not run benchmarks.

**2026 benchmark numbers are single-source.** Every 2026 figure in
[research/04-research-architectures.md](research/04-research-architectures.md) and
[research/06-security.md](research/06-security.md) is the authors' own reported
number, not an independent replication. They are labelled as such in the text, and
they should be read as evidence that a failure mode exists rather than as
measurements of its size.

**The agent-memory chapter is a snapshot.** Nineteen products, ~45 documentation
pages, one date. Vendor memory features ship monthly. The structural findings
(the convergence on a small index plus topic files; the near-total absence of
compilation from episodic to procedural memory) are more durable than any specific
product claim, and are the parts written to survive.

## Open questions

1. **Does compilation require a different substrate?** Every shipped system
   accumulates episodes and retrieves them. None converts repeated episodes into a
   procedure that no longer needs the episodes. Whether this is an architectural
   limit of retrieval-based memory or simply nobody has built it is the single
   most important open question in this library. [02](research/02-cognitive-architectures.md)
   argues the former; nothing yet disproves it.

2. **What is the right unit of forgetting?** Systems forget at the level of a
   memory: a fact, a document, a message. Biology forgets at the level of a
   *distinction* — the ability to tell two similar things apart degrades
   differently from the ability to recall either. No shipped system has a
   fine-grained forgetting control, and it is not obvious what one would look
   like.

3. **Is provenance sufficient for truth?** Provenance tells you where a memory came
   from; citation checking tells you the evidence still exists. Neither tells you
   whether a memory that passes both is *false*. [06](research/06-security.md) ends
   on this; it may be that this is not a memory problem at all.

4. **What happens at ten years?** Every longitudinal claim in this library is about
   sessions, weeks or at most a year. Nobody has published on what an agent memory
   store looks like after a decade of accumulation — and given Library Drift, the
   interesting question is not whether it degrades but what it degrades *into*.

## Revision log

- **2026-11-01** — research library written: `00`–`06`, `sources`, and
  `sources-corrections`, each in English and Chinese. Root README restructured as
  a two-layer navigation hub.
