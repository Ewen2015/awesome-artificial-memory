# Cognitive Architectures: Memory as a Component of a Mind

**Research date:** 2026-11-01

The tradition in this document tried to do something the LLM field is now doing
by accident: specify, in one formal system, how memory participates in thought.
Its results are mostly ignored by current agent-memory work, which is a loss —
because it made two discoveries that the LLM field is now rediscovering.

The first: **memory cannot be specified independently of the controller that
uses it.** A store's design is only meaningful relative to a retrieval policy,
and a retrieval policy is only meaningful relative to the task structure.

The second: **the interesting part of memory is the arithmetic of availability**
— a scalar per item, updated by use, combining recency, frequency, and context
match into a single number that the controller can compare against a threshold.
Everything else is bookkeeping.

---

## 1. Why this tradition exists

Three programmes converged on the same problem between 1970 and 1990.

**Information processing psychology** (Atkinson & Shiffrin, Newell & Simon) had
component models with hand-drawn arrows but no quantitative predictions.

**Artificial intelligence** had programmes that could search but not remember —
every run started from scratch, and adding knowledge made them slower rather
than better (the *utility problem*: each new rule or fact costs match time).

**Mathematical psychology** had precise models of isolated phenomena
(forgetting curves, reaction-time distributions) that did not compose into a
system.

The cognitive architectures were the attempt to make one artefact that could
produce the phenomena. Their architectural commitments — a fixed set of modules,
a central bottleneck, a declarative store accessed by pattern matching, a
procedural store of condition–action rules — turned out to be durable enough
that the vocabulary survived into modern agent systems even where the mechanism
did not.

**The organisational insight worth keeping.** All three architectures converge
on the same decomposition, and it is not the one modern agent frameworks use:

| Layer | Biological framing | Architecture framing | Modern agent equivalent |
|---|---|---|---|
| **Long-term declarative** | semantic + episodic | chunks in a activation-weighted store | vector DB + memory files |
| **Working memory** | limited-capacity buffer | the current goal stack + matched chunks | context window + todo list |
| **Procedural** | skills | production rules | tool definitions, code |
| **Metacognitive** | prefrontal control | conflict resolution, goal management | planner / critic loop |

The modern stack has the first, second, and fourth. It has **no procedural
layer distinct from tools**, which is exactly the gap that skill libraries
(Voyager, Anthropic's Agent Skills) are now filling — and the reason they rot,
since a production system has a conflict-resolution policy and a skill library
does not.

---

## 2. ACT-R: the arithmetic of availability

Anderson, J. R. (2007). *How Can the Human Mind Occur in the Physical Universe?*
Oxford University Press.

Anderson, J. R., Bothell, D., Byrne, M. D., Douglass, S., Lebiere, C., & Qin,
Y. (2004). An integrated theory of the mind. *Psychological Review*, 111(4),
1036–1060. DOI
[10.1037/0033-295X.111.4.1036](https://doi.org/10.1037/0033-295X.111.4.1036).

ACT-R is the most consequential of these for memory design, because it is the
only one that reduced memory to a **single computable quantity**.

**Base-level activation.** Each chunk *i* has an activation

```
B_i = ln( Σ_j t_j^(-d) )
```

summed over its *j* past uses, where *t_j* is the time since the *j*-th use and
*d* ≈ 0.5. This is Anderson & Schooler's need-probability result in closed form:
frequent and recent items have high activation, and the decay is a **power law**,
not an exponential.

**Why this matters more than it looks.** Three properties are worth separating.

*It is a sum over history, not a function of the last write.* The item's whole
access history is in the number. A `last_accessed` column is not this.

*It is free of a global clock.* There is no decay constant applied to all items;
each item decays at its own rate depending on the spacing of its uses.

*It has an interpretation.* Activation is log odds of need, approximately. That
is a **calibrated** score, not an arbitrary ranking heuristic — which means a
threshold on it has a meaning ("only retrieve if P(need) > 0.3"), and most
systems' cosine-similarity thresholds do not.

**Partial matching and noise.** Retrieval is not argmax. Activation is combined
with a match score over the retrieval cue, plus logistic noise; the chunk with
the highest total above threshold is retrieved, and if none clears threshold the
system **fails gracefully** rather than returning the best of a bad set.

Two ideas here that current systems badly need: retrieval as a **threshold
decision with an abstain option**, and **stochastic** retrieval, which makes the
system explore rather than deterministically returning the same top-1 forever.

**Spreading activation.** Related chunks prime each other, so a cue reaches
material that does not match it directly. This is the graph neighbourhood
expansion that GraphRAG and HippoRAG re-derive, with one difference: in ACT-R the
spread is bounded and decays with distance, so it does not blow up. Modern graph
retrieval usually lacks a principled bound.

**Production compilation.** Repeatedly solving a problem by retrieval-and-reasoning
**compiles** into a single production rule — the system stops needing the memory
because the procedure has absorbed it. This is the mechanism behind "abstraction
requires discarding instances" (Nørby 2015) made operational: the instance is
dropped *because* the rule supersedes it.

For agent memory this is the single most under-implemented idea in the whole
literature. A memory system should have a path by which a frequently retrieved
episode becomes a **procedure** and stops being retrieved. Almost none do; they
keep retrieving the same episodes forever and call the accumulation "learning."

---

## 3. SOAR: memory as a by-product of impasse

Laird, J. E. (2012). *The Soar Cognitive Architecture*. MIT Press.

Laird, J. E., Newell, A., & Rosenbloom, P. S. (1987). SOAR: An architecture for
general intelligence. *Artificial Intelligence*, 33(1), 1–64.
DOI [10.1016/0004-3702(87)90050-7](https://doi.org/10.1016/0004-3702(87)90050-7).

SOAR's write policy is the most interesting thing about it, because it is a
**decision about when to learn**, not what to store.

**Impasse-driven learning.** When the current operator selection cannot proceed
— no operator is proposed, or several are tied — SOAR creates a **substate** to
resolve the impasse. The result of that resolution is **chunked**: a new
production rule is compiled that fires directly in the same situation next time.

The write trigger is therefore **failure of the existing policy**. Not "new
information arrived". Not "time elapsed". Not "the user said remember this". The
system writes *only* when its current knowledge was insufficient — which is
exactly the prediction-error criterion, arrived at independently and 40 years
earlier than the dopamine analogy.

**The consequence is a strong claim about memory size.** Because chunks are only
created at impasses, and each chunk removes an impasse, the knowledge base grows
in proportion to the *novelty of situations encountered*, not to experience
duration. Chunking is a **compression of the policy**, not a log of events.

**Episodic memory in SOAR** (Nuxoll & Laird, 2007, *Integrating episodic memory
into a cognitive architecture*, *Advances in Cognitive Systems*) stores
snapshots of working-memory state at decision points, tagged by time and
derived from the *task* rather than the clock. Retrieval is by cue, and — the
design choice worth copying — episodes are **never used to change behaviour
directly**; they are used for explanation, for learning from a single past case,
and for introspection. That separation (episodic store informs the *learner*, not
the *actor*) is not present in any LLM agent memory system I am aware of, and it
is probably why agent episodic memory is so unreliable: it is being used for the
wrong job.

**Semantic memory** in SOAR is a graph of long-term facts with **activation-based
forgetting** and a **base-level learning** rule similar to ACT-R's. The three
stores — procedural (rules), semantic (facts), episodic (snapshots) — share one
retrieval mechanism but have different write triggers. Again: a type system for
memory that current agent frameworks do not have.

---

## 4. Global workspace and the binding problem

Baars, B. J. (1988). *A Cognitive Theory of Consciousness*. Cambridge University
Press.

Dehaene, S., Kerszberg, M., & Changeux, J.-P. (1998). A neuronal model of a
global workspace in effortful cognitive tasks. *PNAS*, 95(24), 14529–14534.
DOI [10.1073/pnas.95.24.14529](https://doi.org/10.1073/pnas.95.24.14529).

Global workspace theory answers a question the store-and-retrieve frame cannot:
**how do items from different specialised systems end up in one representation?**
Its answer is architectural — a limited-capacity workspace that many
unconscious specialised processors compete to write into, and whose contents are
then broadcast back to all of them.

Two consequences for memory design.

**The workspace is a bottleneck, and that is the point.** Capacity limits force
competition, and competition is what makes the broadcast meaningful — only the
most relevant content reaches the whole system. A system with unlimited
workspace has no selection and therefore no salience.

**Broadcast is the mechanism of binding.** Baddeley's episodic buffer is the
same idea at a smaller scale; so is attention in a transformer, functionally if
not mechanistically. The reason "retrieve top-k chunks and concatenate" feels
wrong as a memory architecture is that concatenation is *not* binding: nothing
in the pipeline decides that these items jointly constitute one situation.

**The AI analogue that does bind** is the KV cache plus attention over it during
generation — the model's own working memory. Which suggests a design rule most
agent frameworks ignore: **retrieval should put material where it can be attended
to jointly, not merely append it to a list.** Two retrieved passages that never
attend to each other are two memories, not one.

Dehaene's *Consciousness and the Brain* (2014, Viking) is the accessible version
and makes the ignition/threshold framing explicit: workspace access is
**all-or-none** past a threshold, which is another argument for thresholded
retrieval over top-k.

---

## 5. Memory-augmented neural networks

This line tried to learn the memory rather than engineer it. It is the direct
predecessor of the current generation and its failures are instructive.

**Graves, A., Wayne, G., & Danihelka, I. (2014).** Neural Turing Machines.
arXiv:[1410.5401](https://arxiv.org/abs/1410.5401).

A controller network reads and writes an external memory matrix through
differentiable attention. The key move: the read/write heads are **content-based
and location-based**, and the write head has an **erase-then-add** operation, so
the network learns *what to overwrite* rather than only *what to append*.

Erase-then-add is worth naming. It is the minimal interface for a memory that
can be revised, and most vector stores do not have it: they add. `upsert` on a
key is not the same thing, because the key must be known in advance, whereas
NTM's erase vector is computed from content.

**Graves, A., Wayne, G., Reynolds, M., Harley, T., Danihelka, I., Grabska-
Barwińska, A., … Hassabis, D. (2016).** Hybrid computing using a neural network
with dynamic external memory. *Nature*, 538(7626), 471–476.
DOI [10.1038/nature20101](https://doi.org/10.1038/nature20101).

The Differentiable Neural Computer added a **usage-based allocation** scheme
(remembering which slots are free, and in what order they were last used) and
**temporal link matrix** recording the order in which memories were written, so
the controller can traverse them in sequence.

The temporal link matrix is the important one and it is almost absent from
modern systems. It is a *provenance ordering*: memory N knows which memory was
written just before and just after it. That buys sequential recall and, more
importantly, it makes it possible to ask "what happened around this" without a
timestamp column and a range query — the structure is in the memory itself.

The DNC is also the honest cautionary tale. It worked on synthetic tasks
(bAbI, graph traversal, question answering over structured input) and did not
transfer to open-domain settings. The lesson generally drawn — "learned memory
doesn't scale" — is too strong. The narrower lesson is that a
**gradient-trained memory has no stable interface**: because the write policy is
learned jointly with everything else, there is no way to inspect it, no way to
delete from it under a policy, and no way to audit what got written. That is
the requirement 2026 systems have and 2016 systems did not.

**Weston, J., Chopra, S., & Bordes, A. (2015).** Memory networks.
arXiv:[1410.3916](https://arxiv.org/abs/1410.3916).
Sukhbaatar, S., Weston, J., & Fergus, R. (2015). End-to-end memory networks.
arXiv:[1503.08895](https://arxiv.org/abs/1503.08895).

Memory Networks separated the memory from the reasoning: store sentences in a
slot array, then run multiple **hops** of attention over the slots before
answering. The multi-hop loop is the ancestor of iterative retrieval
(IRCoT, Self-RAG, HippoRAG's graph walk) and of the "think, retrieve, think"
pattern now standard in agent frameworks.

The end-to-end variant removed the supervision and made the whole thing
differentiable, which is when the results got weaker and the field concluded
that explicit memory was the way. That conclusion was probably premature — what
failed was not explicit memory but *gradient-trained* explicit memory.

---

## 6. Episodic memory in reinforcement learning

This is the branch with the strongest claim to being *the* computational result,
because it produced the "forgetting improves performance" demonstration.

**Blundell, C., Uria, B., Pritzel, A., Li, Y., Ruderman, A., Leibo, J. Z., …
Hassabis, D. (2016).** Model-free episodic control. arXiv:[1606.04460](https://arxiv.org/abs/1606.04460).

Store (state, action, return) and, at decision time, look up the nearest
stored states and take the action with the best recorded return. This is
**non-parametric RL**: no generalisation, no function approximation, just
retrieval. It solved Atari games with sparse reward that DQN could not, purely
because it could recall a single lucky trajectory.

The significance for memory design: **one-shot episodic memory substitutes for a
lot of gradient descent**, and it does so exactly where the reward is sparse —
i.e. where the prediction error signal is rare and large. This is the strongest
practical argument that an agent needs an episodic store *and* that the store's
job is to catch the rare high-value event, not to record everything.

**Pritzel, A., Uria, B., Srinivasan, S., Puigdomènech, A., Vinyals, O.,
Hassabis, D., … Blundell, C. (2017).** Neural episodic control.
arXiv:[1703.01988](https://arxiv.org/abs/1703.01988).

Replaced exact lookup with a differentiable, learned key, enabling generalisation
across similar states while keeping the episodic update rule (fast, non-gradient
writes; slower gradient-based consolidation into the parametric policy). This is
the cleanest instantiation of complementary learning systems in an RL agent:
a fast store written once, and a slow store trained from it.

**Yalnizyan-Carson, A., & Richards, B. A. (2022).** *Forgetting enhances
episodic control with structured memories*. *Frontiers in Computational
Neuroscience*, 16, 757244.
DOI [10.3389/fncom.2022.757244](https://doi.org/10.3389/fncom.2022.757244).

Already cited in [01-human-memory.md](01-human-memory.md), and repeated here
because this is its natural home. Same setup as episodic control, but with a
bounded cache and an eviction rule; the finding is that evicting a large
fraction of structured memories costs nothing, and that *some* eviction beats
unbounded memory by suppressing stale and rarely-visited states.

The result is a quantitative answer to a question every production memory system
answers by guesswork: **how much should you forget?** In this setting, far more
than capacity requires.

**Lengyel, M., & Dayan, P. (2007).** Hippocampal contributions to control:
The third way. *Advances in Neural Information Processing Systems* 20.

The framing paper for why a model-free agent needs a distinct third system
between model-free and model-based: a fast, episodic, non-parametric store that
supports **model-based evaluation without a model**. Worth citing whenever
someone asks why they need a memory layer when they already have a large
context window — the answer is that the context window is the model, and a
model is expensive per query.

---

## 7. Working memory as a computational resource

Cowan, N. (2001). The magical number 4 in short-term memory: A reconsideration
of mental storage capacity. *Behavioral and Brain Sciences*, 24(1), 87–114.
DOI [10.1017/S0140525X01003922](https://doi.org/10.1017/S0140525X01003922).

The capacity limit is ~4 chunks, not 7±2, when chunking and rehearsal are
controlled. Two consequences: the limit is real and small; and humans exceed it
by **changing the unit**, not by enlarging the buffer.

The second is the one AI gets wrong. A 200k-token context is not 200k units of
working memory — attention degrades with position (Liu et al. 2023, *Lost in the
Middle*) and with distractor density (RULER, Hsieh et al. 2024). The effective
capacity is much smaller and, unlike a human's, it is not improved by chunking
because the model does not choose its own units.

**Oberauer, K., & Hein, L. (2012).** Attention to information in working memory.
*Current Directions in Psychological Science*, 21(3), 164–169.
DOI [10.1177/0963721412444727](https://doi.org/10.1177/0963721412444727).

Working memory has *no fixed slot count for the focus of attention*; the
binding limit comes from interference and from the requirement to maintain
**bindings** between features. Capacity limits are about **relation maintenance**,
not item count.

Direct implication: the cost of a long context is not the number of tokens but
the number of *relations among them* the model must keep consistent. That is a
much better predictor of when an agent degrades than context length, and it is
why "just add more context" fails specifically on tasks requiring consistency
across many entities.

---

## 8. What this tradition got right, and what it missed

**Right, and largely ignored:**

*Memory's purpose is to be consulted by a controller under a cost.* All three
architectures define memory in terms of the match-retrieve-decide cycle and its
latency budget. Modern systems define memory in terms of storage and leave the
cost model implicit, which is why they have no principled notion of "enough
retrieval."

*Availability is a single scalar with a history.* ACT-R's base-level activation
is the most successful reduction in the field, and it is directly implementable
in a vector store as a per-item score updated on access.

*Write triggers are the design.* SOAR chunks at impasses. ACT-R reinforces on
use. Neither writes on "new input arrived," and both produce knowledge bases
proportional to novelty rather than duration.

*Abstraction must replace instances.* Production compilation is how a system
stops needing its memories. Without it, memory systems grow monotonically and
degrade — the failure mode `Library Drift` (arXiv:2605.19576) measures in
skill libraries.

**Missed, and now being rediscovered the hard way:**

*Scale and open vocabulary.* Every architecture in this document assumed a
hand-built symbol system with a closed predicate set. None could ingest raw
text. This is why they lost, and it is not a small thing — the memory
representation problem for natural language was solved (badly) by embeddings,
not by chunks.

*Multi-agent and provenance.* None of them model memory as something shared
between principals with different permissions. There is no notion of "this
memory came from an untrusted source."

*Deletion.* None of them has a deletion mechanism because none of them had an
adversary or a user with a right to erasure.

---

## 9. The bridge: what the LLM generation took, and what it skipped

The lineage from these architectures to 2026 agent memory is not a straight
line, but three of its components were inherited implicitly and one was dropped
entirely.

**Inherited:** the store/controller split (context window is working memory);
availability-as-a-scalar (Generative Agents' recency × importance × relevance is
base-level activation with different terms); thresholded retrieval (abstention in
LongMemEval is exactly ACT-R's retrieval failure).

**Dropped:** the **procedural store with a conflict-resolution policy**. This is
what skill libraries are now rebuilding, and the reason they are fragile. A
production system resolves conflicts by specificity and recency of use; a skill
library resolves them by embedding similarity to the task description, which is
the wrong criterion and produces the silent degradation that
`Library Drift` documents.

**Never attempted:** **compilation from episodic to procedural under a policy.**
No mainstream agent framework has a mechanism by which a repeatedly retrieved
episode becomes a rule and stops being retrieved. This is the single clearest
piece of unfinished business in the field, and this document is the place to say
so plainly:

> The gap between human and agent memory is not capacity, retrieval quality, or
> context length. It is that humans **compile**: a solved problem becomes a
> procedure and the episodes that produced it stop being needed. Agent memory
> systems accumulate instead of compiling.

---

*See also: [01-human-memory.md](01-human-memory.md) for the biology these
architectures were modelling, [04-research-architectures.md](04-research-architectures.md)
for what the LLM era built instead, and [03-agent-native-memory.md](03-agent-native-memory.md)
for what shipped in products.*
