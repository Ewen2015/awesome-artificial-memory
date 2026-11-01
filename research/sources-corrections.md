# Citation Corrections

**Research date:** 2026-11-01

A short page of references that are widely copied in the wrong form, mostly
because AI papers cite them secondhand from other AI papers. Each entry gives the
correct form, what the common error is, and why it matters — usually because two
very different papers get collapsed into one.

If you are citing memory science in an AI paper, these are the ones to check.

---

## 1. "Active forgetting" is not Anderson & Hulbert's title alone

**Correct:** Anderson, M. C., & Hulbert, J. C. (2021). *Active forgetting: Adaptation of memory by prefrontal control*. Annual Review of Psychology, 72, 1–36. [DOI](https://doi.org/10.1146/annurev-psych-072720-094140)

**Common error:** cited as "Anderson & Hulbert (2021), *Active forgetting*", or with Anderson as sole author, or in *Psychological Review*.

**Why it matters:** the subtitle is the argument. Prefrontal control is the
mechanism — forgetting is an act of inhibition, not decay — and AI papers that
cite the title alone routinely describe it as "passive decay", which is the
opposite claim.

---

## 2. Richards & Frankland 2017 is in *Neuron*, not *TiCS*

**Correct:** Richards, B. A., & Frankland, P. W. (2017). The persistence and transience of memory. *Neuron*, 94(6), 1071–1084. [DOI](https://doi.org/10.1016/j.neuron.2017.04.037)

**Common error:** *Trends in Cognitive Sciences*; or 2016; or with Frankland first.

**Why it matters:** it is usually cited alongside Ryan & Frankland (2022), and the
pair is the standard "forgetting is adaptive" citation in AI memory papers. If the
journal is wrong, the volume and page numbers are wrong too, and nobody downstream
can find it. Frankland's lab also published a companion piece in the same issue
(Davis & Zhong, 2017, *Neuron* 95(3), 490–503) that is frequently confused with it.

---

## 3. Ryan & Frankland is 2022, volume 23, issue 3

**Correct:** Ryan, T. J., & Frankland, P. W. (2022). Forgetting as a form of adaptive engram cell plasticity. *Nature Reviews Neuroscience*, 23(3), 173–186. [DOI](https://doi.org/10.1038/s41583-021-00548-3)

**Common error:** 2021 (which is when it appeared online); or *Nature Neuroscience*.

**Why it matters:** the online-first year and the issue year differ, which is why
both appear in the literature. The 2022 issue year is what the volume and pages
belong to.

---

## 4. Liu et al. 2012, not Ramirez et al. 2012

**Correct:** Liu, X., Ramirez, S., Pang, P. T., Puryear, C. B., Govindarajan, A., Deisseroth, K., & Tonegawa, S. (2012). Optogenetic stimulation of a hippocampal engram activates fear memory recall. *Nature*, 484(7394), 381–385. [DOI](https://doi.org/10.1038/nature11028)

**Common error:** attributed to "Ramirez et al. (2012)".

**Why it matters:** the Tonegawa lab published two different 2012 *Nature* engram
papers. *Liu et al.* is the activation paper; *Ramirez et al.* (2013, *Science*)
is the false-memory implantation paper. "Ramirez 2012" is a citation that exists
in no journal.

---

## 5. Tulving 1995 is a book chapter, not a paper

**Correct:** Tulving, E. (1995). Organization of memory: Quo vadis? In M. S. Gazzaniga (Ed.), *The Cognitive Neurosciences* (pp. 839–847). MIT Press.

**Common error:** cited as *Journal of Cognitive Neuroscience*, or as a 1995 article with a DOI, or with the title *"What is episodic memory?"* — which is a different Tulving paper (1993, *Current Directions in Psychological Science*, 2(3), 67–70).

**Why it matters:** the SPI model (serial, parallel, independent) is usually
attributed to this chapter. There is no journal DOI to give; a fabricated one in
a reference list is a strong signal the citation was never checked.

---

## 6. Budson & Kensinger, in that order

**Correct:** Budson, A. E., & Kensinger, E. A. (2023). *Why We Forget and How to Remember Better: The Science Behind Memory*. Oxford University Press.

**Common error:** "Kensinger & Budson" — an understandable reversal, because
Kensinger is the better-known memory researcher of the two.

**Why it matters:** mostly a tidiness issue, but it appears in a lot of reading
lists, including several in AI papers' related-work sections.

---

## 7. Ebbinghaus 1885 and the 1913 translation are different citations

**Correct:** Ebbinghaus, H. (1885). *Über das Gedächtnis* (Duncker & Humblot); English: *Memory: A Contribution to Experimental Psychology* (Ruger & Bussenius, trans., 1913).

**Common error:** "(Ebbinghaus, 1885)" attached to the English title.

**Why it matters:** the forgetting curve is usually quoted from the translation,
and the DOI that circulates (10.1037/10011-000) is registered to the 1913
translation. Citing the German original with an English title and an APA DOI is
three citations spliced into one.

---

## 8. The two 1974 DOIs that get swapped

**Correct:** Baddeley & Hitch (1974) is a chapter in *The Psychology of Learning
and Motivation*, Vol. 8 — DOI 10.1016/S0079-7421(08)60452-1. Loftus & Palmer (1974)
is a *Journal of Verbal Learning and Verbal Behavior* article, 13(5), 585–589 —
DOI 10.1016/S0022-5371(74)80018-6.

**Common error:** both appear under the *JVLVB* DOI prefix, sometimes with the
same DOI, because both are 1974 and both were recycled through the same
bibliography generators.

**Why it matters:** Baddeley & Hitch's working-memory model and Loftus & Palmer's
reconstructive-memory study are cited in the same paragraph of a lot of agent
memory papers. They are not the same kind of source and, being a book chapter and
a journal article, they do not even share a DOI registrar path.

---

## 9. HippoRAG 2 is a different paper from HippoRAG

**Correct:** HippoRAG (Gutiérrez et al., 2024, arXiv:2405.14831) and HippoRAG 2
(Gutiérrez et al., 2025, "From RAG to memory", arXiv:2502.14802, ICML 2025).

**Common error:** citing "HippoRAG" for a claim about memory consolidation that
only HippoRAG 2 makes, or vice versa.

**Why it matters:** the first is PageRank over a knowledge graph with a
hippocampal analogy; the second adds a non-parametric continual-learning framing
and a different recognition-memory component. The reported numbers differ and are
not interchangeable.

---

## 10. MemGPT's venue

**Correct:** Packer, C., Wooders, S., Lin, K., Fang, V., Patil, S. G., Stoica, I., & Gonzalez, J. E. (2023). MemGPT: Towards LLMs as operating systems. arXiv:2310.08560.

**Common error:** cited as an ICLR 2024 paper.

**Why it matters:** it circulates as both. If you need a stable citation, the arXiv
preprint is the safe form for every paper in this library that has no confirmed
venue — which is most of them.

---

## A general rule for this repository

Every arXiv ID in [sources.md](sources.md) was checked against arXiv on
2026-11-01. Every journal entry gives volume, issue and pages. Where a venue is
uncertain, only the arXiv form is given rather than a guessed conference.

If you find one that is wrong, open an issue with the DOI — corrections to this
page are the most useful kind of contribution the repository can receive.

---

*Back to [sources.md](sources.md) · [README.md](README.md)*
