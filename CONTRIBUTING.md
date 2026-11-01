# Contributing to awesome-artificial-memory

Contributions are welcome. This repository has two layers, and they have
different standards.

## 1. The curated list (`README.md`)

The original collection. Keep it short and hand-checked.

1. **Scope.** The resource must bear directly on artificial memory, the
   neuroscience of forgetting, or the engineering of forgetting in AI systems —
   selective pruning, memory compression, consolidation, continual learning.
2. **Format.**
    ```markdown
    *   **[Type] Title** [Source URL]
        *   *Focus:* One sentence on what the resource argues or contributes.
    ```
    **Type** is one of `[Paper]`, `[Article]`, `[Book]`, `[Tool]`, `[Project]`,
    `[Video]`.
3. **No duplicates.** Check the list and `research/sources.md` first.
4. **Quality.** Authoritative or highly relevant. Prefer the primary source over
   a write-up of it.

## 2. The research library (`research/`)

The eight documents are long-form and held to a higher bar.

**Every document exists twice.** `NAME.md` in English and `NAME.zh-CN.md` in
Chinese. If you change a claim, change it in both. The versions are not
sentence-by-sentence translations — the Chinese is written to read naturally in
Chinese — but the claims, numbers and citations must match exactly.

**Every factual claim carries a citation.** Journal entries give author, year,
title, volume, issue and pages, with a DOI. AI papers give the arXiv ID. Product
claims cite the vendor's own documentation, with the URL attached to the specific
claim it supports — not dumped at the bottom. An uncited claim must be written as
an argument, not as a finding.

**Every document answers the same frame.** What is stored, what triggers a write,
how it is indexed and retrieved, and what gets forgotten. A new section that does
not engage one of these four usually belongs in an existing one.

**Keep negative results.** If you add a benchmark number, add the ones that came
out badly too. A library that only reports what worked is a sales document.

**Date everything.** Each document carries a research date. If you substantially
revise one, update it.

**Corrections go to `research/sources-corrections.md`.** If you find a reference
that is widely cited in the wrong form, add it there rather than quietly fixing
it — the wrong form is what most readers will have.

### Where things go

- A new product comparison → `research/03-agent-native-memory.md`, plus the table
  in `README.md` if it is one of the handful most people actually use.
- A new paper → `research/04-research-architectures.md` or
  `research/01-human-memory.md`, plus `research/sources.md`.
- A storage or retrieval technique → `research/05-infrastructure.md`.
- An attack or a defensive control → `research/06-security.md`.
- Anything that changes the overall story → `research/00-timeline.md`, and check
  that the other documents still agree with it.

## How to submit

1. Fork the repository.
2. Create a branch: `git checkout -b add-resource-name`.
3. Make the change, in both language versions if it touches `research/`.
4. Commit: `git commit -m 'feat: Add resource on selective forgetting'`.
5. Push and open a Pull Request against `main`.

In the pull request description, say what the source is and — for research
contributions — which of the four questions your change bears on.

## Licence

By contributing you agree your work is released under the repository's MIT
licence.
