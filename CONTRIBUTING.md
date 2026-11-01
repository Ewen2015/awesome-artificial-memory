# Contributing to awesome-artificial-memory

This repository is a research library, not a link collection. Contributions are
welcome, and they are held to the standard of one.

## What a contribution has to do

**Every factual claim carries a citation.** Journal entries give author, year,
title, volume, issue and pages, with a DOI. AI papers give the arXiv ID. Product
claims cite the vendor's own documentation, with the URL attached to the specific
claim it supports — not dumped at the bottom. An uncited claim must be written as
an argument, not as a finding.

**Every document exists twice.** `NAME.md` in English and `NAME.zh-CN.md` in
Chinese. If you change a claim, change it in both. The versions are not
sentence-by-sentence translations — the Chinese is written to read naturally in
Chinese — but the claims, numbers and citations must match exactly.

**Every document answers the same frame.** What is stored, what triggers a write,
how it is indexed and retrieved, and what gets forgotten. A new section that does
not engage one of these four usually belongs in an existing one.

**Keep negative results.** If you add a benchmark number, add the ones that came
out badly too. A library that only reports what worked is a sales document.

**Date everything.** Each document carries a research date. If you substantially
revise one, update it.

## Where things go

- A new product, or a correction to one → `03-agent-native-memory.md`. Product
  claims must come from the vendor's own documentation, retrieved on the date you
  cite; third-party write-ups are not a substitute. If the vendor's docs cannot be
  retrieved, the product is dropped rather than reconstructed.
- A new paper → `04-research-architectures.md`, or `01-human-memory.md` if it is
  about biological memory, plus `sources.md`.
- A storage or retrieval technique → `05-infrastructure.md`.
- An attack or a defensive control → `06-security.md`.
- A change to the overall story → `00-timeline.md`, and check that the other
  documents still agree with it.
- A widely miscited reference → `sources-corrections.md`, rather than quietly
  fixing it in place. The wrong form is what most readers will have.

## What gets declined

Rankings of products. Architecture proposals without a reference implementation.
Claims that biology is a blueprint for engineering. Anything whose only source is
a blog post summarising a paper that is itself available.

## How to submit

1. Fork the repository.
2. Create a branch: `git checkout -b add-resource-name`.
3. Make the change, in both language versions.
4. Commit: `git commit -m 'feat: Add resource on selective forgetting'`.
5. Push and open a Pull Request against `main`.

In the pull request description, say what the source is and which of the four
questions your change bears on. A correction with a DOI is the fastest thing to
merge.

## Licence

By contributing you agree your work is released under the repository's MIT
licence.
