# Human Memory and Forgetting: The Source Base

**Research date:** 2026-11-01

Most AI memory systems are naive re-derivations of human memory. This document
collects the primary literature so that the re-derivation can be deliberate
rather than accidental — and so that designs which contradict fifty years of
evidence can be recognised as such.

**How to use this document.** Sections 1–4 are the evidence. Section 5 is the
book list. Section 6 maps each biological mechanism to its closest AI analogue
and — more importantly — says where the analogy breaks. Section 7 lists the
things AI practitioners most often get wrong.

Read Section 7 first if you only read one.

---

## 1. Classic foundations

### Ebbinghaus (1885 / trans. 1913)

Ebbinghaus, H. (1885). *Über das Gedächtnis: Untersuchungen zur experimentellen
Psychologie*. Leipzig: Duncker & Humblot.
English: *Memory: A Contribution to Experimental Psychology* (Ruger & Bussenius,
trans., 1913). DOI [10.1037/10011-000](https://doi.org/10.1037/10011-000).

He memorised nonsense syllables and measured retention by the **savings
method** — how much *relearning time* was saved, not how much could be
recalled. Retention fell steeply then flattened: approximately 58% at 20 min,
44% at 1 h, 33% at 1 day, 21% at 31 days (figures vary slightly across
reproductions of his table; quote them as approximate).

Two things matter. The curve made forgetting **quantifiable** and showed the
loss is front-loaded and asymptotic rather than linear. And the savings method
measures a residue the subject cannot consciously report — a distinction AI
systems have no analogue for, because they retrieve or they don't.

Every decay/recency function in every memory system descends from this curve.
The detail people forget: **the tail is long**. That is the empirical basis for
keeping a compressed residue rather than deleting outright.

### Müller & Pilzecker (1900)

*Experimentelle Beiträge zur Lehre vom Gedächtniss*. Leipzig: J. A. Barth.

They demonstrated **retroactive inhibition**: material learned immediately after
a list degrades retention of that list, and the sooner the interpolated material
arrives, the worse the damage. From this they inferred **perseveration** — the
trace keeps reverberating and consolidating for a period after learning, and is
fragile during that window.

This is the direct ancestor of consolidation theory, and the first statement of
a principle every write pipeline re-derives: **what happens after encoding
determines what survives.** A write buffer is not an optimisation; it is the
mechanism.

### Bartlett (1932)

*Remembering: A Study in Experimental and Social Psychology*. Cambridge
University Press.

Memory is **reconstruction**, guided by schemas. Recall fills gaps with prior
knowledge rather than replaying a record. The classic result: English subjects
recalling a Native American folk tale progressively normalise it toward their
own cultural expectations, dropping and transforming details that do not fit.

This is the strongest argument that a "perfect record" is the wrong target. An
LLM agent's retrieval path is *always* generative — so it already has
reconstructive memory. The question is only whether it marks the reconstruction.

### Atkinson & Shiffrin (1968)

*Human memory: A proposed system and its control processes*. In Spence &
Spence (Eds.), *The Psychology of Learning and Motivation*, Vol. 2, 89–195.
DOI [10.1016/S0022-5371(68)80002-7](https://doi.org/10.1016/S0022-5371(68)80002-7).

The modal model: a **sensory register**, a **short-term store** (limited,
the workspace), a **long-term store** (effectively unlimited). The lasting
contribution is the separation of **structural features** (fixed architecture)
from **control processes** (rehearsal, coding, retrieval strategy).

Transfer directly: a small fast lossy buffer in front of a large slow store is
an *architectural* choice; the interesting behaviour lives in the **control
policy**. This is precisely where 2023-era agent memory failed — it built
storage and left the policy implicit.

### Tulving (1972, 1985, 1995)

Tulving, E. (1972). Episodic and semantic memory. In Tulving & Donaldson (Eds.),
*Organization of Memory*, 381–403.

**Episodic**: personally experienced events with temporal–spatial context.
**Semantic**: context-free organised knowledge. The distinction is *functional,
not anatomical* — it is about whether retrieval re-instantiates an episode or
extracts a generalisation.

Tulving, E. (1985). Memory and consciousness. *Canadian Psychology*, 26(1),
1–12. DOI [10.1037/h0080017](https://doi.org/10.1037/h0080017). Pairs each
system with a form of awareness: procedural = **anoetic**, semantic = **noetic**,
episodic = **autonoetic** (mental time travel). Introduces remember/know.

Tulving, E. (1995). Organization of memory: Quo vadis? In Gazzaniga (Ed.),
*The Cognitive Neurosciences*, 839–847. MIT Press. Supplies **SPI**: encoding is
**S**erial (episodic encoding passes through semantic), storage is **P**arallel,
retrieval is **I**ndependent.

SPI is a directly usable design rule: **couple on write, decouple on read.** It
is also a warning that "one store per memory type" is not what biology does.

The Tulving cut is the single most useful one for AI design, because most agent
"memory" is neither episodic nor semantic — it is an append-only log with no
episodic indexing (no time/place/agent tags) and no semantic distillation.

### Baddeley & Hitch (1974), Baddeley (2000)

Baddeley & Hitch (1974), *Working memory*, in *Psychology of Learning and
Motivation* Vol. 8, 47–89.
DOI [10.1016/S0022-5371(74)80011-3](https://doi.org/10.1016/S0022-5371(74)80011-3).

Working memory is not one buffer: **phonological loop**, **visuospatial
sketchpad**, **central executive**. Interference is domain-specific.

Baddeley (2000). The episodic buffer: a new component of working memory?
*Trends in Cognitive Sciences*, 4(11), 417–423.
DOI [10.1016/S1364-6613(00)01538-2](https://doi.org/10.1016/S1364-6613(00)01538-2).

The **episodic buffer** binds information from the slave systems and from
long-term memory into unitary episodes. Baddeley's own framing of what the
revision achieved is the sentence that matters: the advance is "focussing
attention on the processes of **integrating** information, rather than on the
isolation of the subsystems."

This is the largest genuine gap between biological and current AI memory. RAG
pipelines retrieve chunks; they do not *bind* retrieved chunks plus current
context plus task state into a coherent representation.

### Squire (2004)

*Memory systems of the brain: A brief history and current perspective*.
*Neurobiology of Learning and Memory*, 82(3), 171–177.
DOI [10.1016/j.nlm.2004.06.005](https://doi.org/10.1016/j.nlm.2004.06.005).

**Declarative** (episodic + semantic; medial temporal lobe) versus
**nondeclarative** (procedural skills, priming, conditioning; striatum,
neocortex, amygdala, cerebellum). Squire's framing is explicitly
anti-dichotomy: the point is *multiple separate systems*, each with its own
operating characteristics.

Corollary for AI: memory is **not one substrate**. Weights, retrieval index, and
skill/policy are different systems with different update rules and different
forgetting curves. Collapsing them into "the memory module" is a category error.

### Craik & Lockhart (1972)

*Levels of processing: A framework for memory research*. *Journal of Verbal
Learning and Verbal Behavior*, 11(6), 671–684.
DOI [10.1016/S0022-5371(72)80001-X](https://doi.org/10.1016/S0022-5371(72)80001-X).

Durability is a function of **depth of processing** at encoding. Shallow
perceptual analysis yields fragile traces; semantic, elaborative analysis yields
durable ones. The trace is a **by-product of the operations performed**.

The strongest argument against "write everything, retrieve later": *how* you
encode determines *whether* it survives, independent of exposure count. In AI
terms, the write function (summarise? link? extract entities? attach
provenance?) matters more than storage volume — and unlike humans, AI systems
can cheaply re-encode, which is their unique lever.

### Anderson & Schooler (1991) — the bridge paper

*Reflections of the environment in memory*. *Psychological Science*, 2(6),
396–408. DOI
[10.1111/j.1467-9280.1991.tb00174.x](https://doi.org/10.1111/j.1467-9280.1991.tb00174.x).

They analysed real environments — the *New York Times*, parental speech to
children, a researcher's email — and showed that the **probability an item will
be needed again** follows the same lawful relation to **frequency, recency and
spacing** as human recall does.

Memory availability is therefore not an arbitrary decay. It is a **statistical
estimate of future demand**, tuned to environmental regularities. In ACT-R this
becomes base-level activation tracking log need-odds.

This is *the* citation for this repo. It converts "forgetting is a feature" from
a slogan into a testable optimality claim: **if your retrieval score is not an
estimate of P(item needed at query time | history), you are not doing what
memory does.**

### Bjork & Bjork (1992)

*A new theory of disuse and an old theory of stimulus fluctuation*. In Healy,
Kosslyn & Shiffrin (Eds.), *Essays in Honor of William K. Estes*, Vol. 2, 35–67.

Two independent strengths: **storage strength** (how well learned; never
decreases) and **retrieval strength** (current accessibility; fluctuates,
capacity-limited). Retrieval increases both, and increases retrieval strength
*more* when storage strength is high and retrieval strength is low — the
mechanism behind spacing and testing effects.

The cleanest formal separation of "we still have it" from "we can reach it now".
An agent memory needs exactly this distinction: *retained in the store* versus
*retrievable under this query*. Retrieval strength in biology moves with **use**;
in most vector stores it moves with time only.

---

## 2. Forgetting as an active mechanism

### Anderson & Hulbert (2021)

*Active forgetting: Adaptation of memory by prefrontal control*. *Annual Review
of Psychology*, 72, 1–36. DOI
[10.1146/annurev-psych-072720-094140](https://doi.org/10.1146/annurev-psych-072720-094140).

Reviews a century of the "passive decay or active inhibition?" debate and lands
on **active**: prefrontal cortex exerts top-down, often inhibitory control over
hippocampal mnemonic activity, suppressing specific traces. The mechanism is
**goal-directed suppression**, not erosion.

The strongest counter to the "storage with garbage collection" model: forgetting
is an *executed operation with a controller*. Any system whose only forgetting
is TTL expiry has implemented the passive half of a two-part mechanism.

### Richards & Frankland (2017) — the thesis paper

*The persistence and transience of memory*. *Neuron*, 94(6), 1071–1084.
DOI [10.1016/j.neuron.2017.04.037](https://doi.org/10.1016/j.neuron.2017.04.037).

> "The goal of memory is not the transmission of information through time, per
> se. Rather, the goal of memory is to **optimize decision-making**. As such,
> transience is as important as persistence."

They map biological transience — synaptic weakening and elimination,
hippocampal neurogenesis overwriting — onto machine-learning **regularisation**:
forgetting details prevents **overfitting** to specific past episodes and
improves generalisation and behavioural flexibility.

This is the paper that makes the AI analogy explicit in a peer-reviewed venue
and names overfitting as the shared failure mode.

### Davis & Zhong (2017)

*The biology of forgetting — A perspective*. *Neuron*, 95(3), 490–503.
DOI [10.1016/j.neuron.2017.05.039](https://doi.org/10.1016/j.neuron.2017.05.039).

Three families: **neurogenesis-based**, **interference-based**, **intrinsic**.
The best-characterised intrinsic pathway is *Drosophila* "forgetting cells"
releasing **dopamine** onto engram cells, driving a Rac1/Cofilin cascade that
remodels the actin cytoskeleton.

The provocative claim: **intrinsic forgetting may be the default state**,
constantly erasing memory in competition with consolidation. Erasure as
baseline, remembering as active expenditure — the sharpest statement available
of "forgetting is a feature."

### Ryan & Frankland (2022)

*Forgetting as a form of adaptive engram cell plasticity*. *Nature Reviews
Neuroscience*, 23(3), 173–186.
DOI [10.1038/s41583-021-00548-3](https://doi.org/10.1038/s41583-021-00548-3).

Memories are stored in **engram cell ensembles**; recall is reactivation.
Forgetting is not deletion but **circuit remodelling that shifts engram cells
from an accessible to an inaccessible state**. (Ryan's own phrasing: the memories
are in a safe but you can't remember the code.)

**The dominant form of biological forgetting is loss of the index/access path,
not loss of the payload.** Vector stores that delete rows are modelling the rare
case. An engineering consequence follows immediately: tombstones, stale-index
handling, and unreachable-but-present records deserve as much design attention
as eviction.

### Nørby (2015)

*Why forget? On the adaptive value of memory loss*. *Perspectives on
Psychological Science*, 10(5), 551–578.
DOI [10.1177/1745691615596787](https://doi.org/10.1177/1745691615596787).

Three adaptive functions: **emotion regulation**, **knowledge acquisition**
(abstraction and automatisation require discarding instances), and **context
attunement** (keeping knowledge current).

The second is the one AI most needs: **abstraction requires discarding the
instance**. An agent that never forgets cannot form a generalisation, only a
lookup table.

### Fusi, Drew & Abbott (2005)

*Cascade models of synaptically stored memories*. *Neuron*, 45(4), 599–611.
DOI [10.1016/j.neuron.2005.02.001](https://doi.org/10.1016/j.neuron.2005.02.001).

Each synapse is a **cascade of states with graded plasticity**: after a weight
is potentiated, further potentiating events push it into states with *lower*
learning rate. This yields dynamics across many timescales and **power-law
forgetting**, combining high capacity with long retention.

Precise, underused lesson: **forgetting rate should be a function of the history
of the weight, not of wall-clock time.** A weight stable for a long time should
be harder to overwrite than a fresh one. This is directly implementable, and is
what "metaplasticity" in continual-learning work re-derives.

### The computational follow-ups

Yalnizyan-Carson, A., & Richards, B. A. (2022). *Forgetting enhances episodic
control with structured memories*. *Frontiers in Computational Neuroscience*,
16, 757244. DOI
[10.3389/fncom.2022.757244](https://doi.org/10.3389/fncom.2022.757244).

An RL agent using an episodic memory cache in mazes can forget a **large
fraction** of older memories with **no performance loss** — provided its
representations carry structural information — and *some* forgetting **improves**
performance over unbounded memory by reducing the influence of outdated
information and rarely visited states.

This is the computational demonstration of the Richards & Frankland thesis, and
the most quotable result in the literature: **the brain forgets more than
capacity requires**, which is only explicable if forgetting is doing work.

Tran, L. M., Santoro, A., Liu, L., Josselyn, S. A., Richards, B. A., &
Frankland, P. W. (2022). *Adult neurogenesis acts as a neural regularizer*.
*PNAS*, 119(45), e2206704119. DOI
[10.1073/pnas.2206704119](https://doi.org/10.1073/pnas.2206704119).

Modelling neurogenesis as **random reinitialisation of a small subset of hidden
units** (~3.2% optimal) in a CNN on CIFAR-10 improved generalisation,
performing comparably to dropout and beating weight decay and neural noise —
while *sacrificing training accuracy* for test accuracy.

Partial destruction of learned structure is a regulariser, with a number
attached. The "synaptic pruning" framing in the popular press is a re-report of
this and of the 2017 review.

de Snoo, M. L., & Frankland, P. W. (2025). *Neurobiological mechanisms of
forgetting across timescales*. *Current Opinion in Neurobiology*, 90, 102972.
DOI [10.1016/j.conb.2025.102972](https://doi.org/10.1016/j.conb.2025.102972).

The current, most precise statement: forgetting is a **family of mechanisms that
reduce engram accessibility**, operating on distinct timescales — short-term
(brain-state fluctuations altering accessibility moment to moment) and
intermediate/long-term (plasticity that rewires engrams so they become
progressively harder to retrieve).

---

## 3. Neural substrate

### Engram cells

**Liu, X., Ramirez, S., Pang, P. T., Puryear, C. B., Govindarajan, A.,
Deisseroth, K., & Tonegawa, S. (2012).** Optogenetic stimulation of a
hippocampal engram activates fear memory recall. *Nature*, 484(7394), 381–385.
DOI [10.1038/nature11028](https://doi.org/10.1038/nature11028).

Cells active during contextual fear conditioning were labelled with ChR2;
reactivating them optogenetically **in a different context** produced freezing.
A **sufficiency** demonstration: the ensemble is not a correlate of the memory,
it is the memory's physical handle.

**Roy, D. S., Muralidhar, S., Smith, L. M., & Tonegawa, S. (2017).** Silent
memory engrams as the basis for retrograde amnesia. *PNAS*, 114(46),
E9972–E9979. DOI
[10.1073/pnas.1714248114](https://doi.org/10.1073/pnas.1714248114).

After anisomycin-induced amnesia, optogenetic recall of the DG engram still
worked for at least 8 days; the memory was present but **silent** —
inaccessible to natural cues, accessible to direct activation. The cleanest
experimental demonstration that **amnesia ≠ erasure**, and the empirical support
for the accessibility framing.

**Kitamura, T., Ogawa, S. K., Roy, D. S., Okuyama, T., Morrissey, M. D., Smith,
L. M., … Tonegawa, S. (2017).** Engrams and circuits crucial for systems
consolidation of a memory. *Science*, 356(6333), 73–78.
DOI [10.1126/science.aam6808](https://doi.org/10.1126/science.aam6808).

Hippocampal and prefrontal engrams mature on different schedules, with
hippocampal engram cells becoming silenced as the cortical engram matures — a
physical implementation of **fast-store-to-slow-store migration**, with the same
memory in two places at different accessibility.

**Josselyn, S. A., & Tonegawa, S. (2020).** Memory engrams: Recalling the past
and imagining the future. *Science*, 367(6473), eaaw4325.
DOI [10.1126/science.aaw4325](https://doi.org/10.1126/science.aaw4325).

The definitive review. Engram cells compete for allocation — intrinsic
excitability determines winners — show increased spine density and preferential
downstream connectivity, support artificial reactivation, allow **false memories**
to be implanted, and exist as silent engrams that can be re-activated. A memory
is supported by an **engram complex** across regions, not a single node.

Two claims worth carrying: memory is a reconstruction assembled from a
distributed ensemble; and **allocation is competitive** — a resource-constrained
write policy, not a free append.

### Hippocampal indexing

**Teyler, T. J., & DiScenna, P. (1986).** The hippocampal memory indexing
theory. *Behavioral Neuroscience*, 100(2), 147–154.
DOI [10.1037/0735-7044.100.2.147](https://doi.org/10.1037/0735-7044.100.2.147).

The hippocampus stores an **index** of which neocortical areas were active
during an event; reactivating the index reactivates the cortical ensemble. The
cortex holds the content; the hippocampus holds the **pointer**.

This is the most directly transferable idea in the literature, and it is worth
being explicit about why: it predicts that a memory system's scarce, fast,
high-resolution structure should contain **addresses, not payloads**. That is
what a vector index is — and it is what most agent memory systems are not, since
they store content chunks in the index and call it done.

**Teyler, T. J., & Rudy, J. W. (2007).** The hippocampal indexing theory and
episodic memory: Updating the index. *Hippocampus*, 17(12), 1158–1169.
DOI [10.1002/hipo.20350](https://doi.org/10.1002/hipo.20350).

The index must capture the cortical activity pattern generated by an episode's
features and project back to them, so a *partial cue* reinstates the whole
pattern. Cite this as the modern statement; the 1986 paper is the origin.

### Complementary learning systems

**McClelland, J. L., McNaughton, B. L., & O'Reilly, R. C. (1995).** Why there
are complementary learning systems in the hippocampus and neocortex.
*Psychological Review*, 102(3), 419–457.
DOI [10.1037/0033-295X.102.3.419](https://doi.org/10.1037/0033-295X.102.3.419).

Two systems with opposite learning rates: the hippocampus learns new items fast
without disrupting structure; the neocortex learns slowly and interleaved, so it
can discover structure across many experiences. Fast learning in a slow-learning
system causes **catastrophic interference**; the hippocampal fast store plus
**replay** interleaves new memories into the neocortex.

The theoretical charter for the entire "episodic buffer + slow weights"
architecture — and the correct diagnosis of why naive fine-tuning destroys old
capabilities: you asked a neocortex to do a hippocampus's job.

**Kumaran, D., Hassabis, D., & McClelland, J. L. (2016).** What learning systems
do intelligent agents need? Complementary learning systems theory updated.
*Trends in Cognitive Sciences*, 20(7), 512–534.
DOI [10.1016/j.tics.2016.05.004](https://doi.org/10.1016/j.tics.2016.05.004).

Hippocampal **replay is goal-dependent** — it weights experience statistics by
what the agent currently needs, not uniformly. Neocortical learning can be
**rapid** for structure-consistent information. Written by people who built
DeepMind, explicitly about what an intelligent agent needs. The most useful
single consolidation paper for an AI repo, because it reframes replay as a
**prioritisation policy**, which is what a memory system actually is.

### Sleep and consolidation

**Diekelmann, S., & Born, J. (2010).** The memory function of sleep. *Nature
Reviews Neuroscience*, 11(2), 114–126.
DOI [10.1038/nrn2762](https://doi.org/10.1038/nrn2762).

Recently encoded traces are reactivated and redistributed during sleep ("system
consolidation"), with slow-wave sleep driving hippocampal→neocortical transfer
and REM supporting stabilisation and integration. Sleep is an **offline compute
window** in which the fast store replays into the slow store without new
interference.

**Rasch, B., & Born, J. (2013).** About sleep's role in memory. *Physiological
Reviews*, 93(2), 681–766.
DOI [10.1152/physrev.00032.2012](https://doi.org/10.1152/physrev.00032.2012).

**Waking is optimised for encoding and sleep for consolidation** — and the two
modes are mutually exclusive because they have incompatible requirements. That
is an argument that consolidation should be a **scheduled batch process**, not
something that happens inline during retrieval.

**Walker, M. P., & Stickgold, R. (2006).** Sleep, memory, and plasticity.
*Annual Review of Psychology*, 57, 139–166.
DOI [10.1146/annurev.psych.56.091103.070307](https://doi.org/10.1146/annurev.psych.56.091103.070307).
With **Walker & Stickgold (2010), "Overnight alchemy: sleep-dependent memory
evolution", *Nature Reviews Neuroscience*, 11(3), 218**
([link](https://www.nature.com/articles/nrn2762-c1)), which argues for three
integration operations — **unitisation, assimilation, abstraction** — under a
two-stage SWS-then-REM model. That three-way decomposition is a ready-made
taxonomy for what a consolidation job should actually *do* with replayed data.

**Tononi, G., & Cirelli, C. (2014).** Sleep and the price of plasticity.
*Neuron*, 81(1), 12–34.
DOI [10.1016/j.neuron.2013.12.025](https://doi.org/10.1016/j.neuron.2013.12.025).

The **synaptic homeostasis hypothesis**: waking learning strengthens synapses
brain-wide, raising energy cost, lowering signal-to-noise, and **saturating
learning capacity**; sleep renormalises net synaptic strength and down-selects
synapses. The clearest biological statement of **consolidation as a
compute-bounded operation**. The AI analogy is not weight decay — it is that a
system with fixed capacity must *spend* capacity, and renormalisation is how it
buys more.

### Reconsolidation

**Nader, K., Schafe, G. E., & LeDoux, J. E. (2000).** Fear memories require
protein synthesis in the amygdala for reconsolidation after retrieval. *Nature*,
406(6797), 722–726. DOI [10.1038/35021052](https://doi.org/10.1038/35021052).

Reactivating a consolidated fear memory returns it to a **labile state**
requiring new protein synthesis. Anisomycin after reactivation produced amnesia;
anisomycin without reactivation did not; a 6-hour-delayed infusion did not.

**Retrieval itself is a write.** A read-modify-write cycle is the biologically
correct primitive; a pure read path is a fiction. Every access is an opportunity
to update — or corrupt — the record.

---

## 4. Neuromodulation and salience

**Lisman, J., Grace, A. A., & Düzel, E. (2011).** A neoHebbian framework for
episodic memory; role of dopamine-dependent late LTP. *Trends in Neurosciences*,
34(10), 536–547.
DOI [10.1016/j.tins.2011.07.006](https://doi.org/10.1016/j.tins.2011.07.006).

The Hebb rule explains early-phase LTP but not **late** LTP, which requires an
additional non-local signal; at CA1, novelty and reward supply it via
**dopamine**. The result is a **neoHebbian** rule: pre × post × neuromodulator.

A plain dot-product similarity is an early-phase rule — it can tag but not
**commit**. Commitment needs a third, global, value-carrying signal.

**Shohamy, D., & Adcock, R. A. (2010).** Dopamine and adaptive memory. *Trends
in Cognitive Sciences*, 14(10), 464–472.
DOI [10.1016/j.tics.2010.08.002](https://doi.org/10.1016/j.tics.2010.08.002).

Dopamine — the system that signals motivationally important events — biases
memory toward events of motivational significance, so both **expectations and
outcomes** shape what is stored. Encoding is **value-weighted**.

Together: **reward prediction error is the write-enable signal.** Prediction
error says "this was not what the model expected," which is exactly the condition
under which the model should update. An agent that writes on every observation
is running without a write-enable line; one that writes on surprise is
implementing the dopaminergic rule.

**de Quervain, D. J.-F., Aerni, A., Schelling, G., & Roozendaal, B. (2009).**
Glucocorticoids and the regulation of memory in health and disease. *Frontiers
in Neuroendocrinology*, 30(3), 358–370.
DOI [10.1016/j.yfrne.2009.03.002](https://doi.org/10.1016/j.yfrne.2009.03.002).

The key asymmetry: glucocorticoids **enhance consolidation** of emotionally
arousing experiences but **impair retrieval**. That is a **phase-dependent**
modulation — the same signal that strengthens a write weakens a read.

**Lupien, S. J., McEwen, B. S., Gunnar, M. R., & Heim, C. (2009).** Effects of
stress throughout the lifespan on the brain, behaviour and cognition. *Nature
Reviews Neuroscience*, 10(6), 434–445.
DOI [10.1038/nrn2639](https://doi.org/10.1038/nrn2639).

Stress effects depend on developmental timing and on the subsystem. A **global
modulation signal can have opposite effects on different subsystems**, so a
single global "importance" or "learning rate" scalar is not a neutral design
choice.

---

## 5. Books

| Work | Citation | Why it earns a place |
|---|---|---|
| **Why We Forget and How to Remember Better** (2023) | Budson, A. E., & Kensinger, E. A. *Why We Forget and How to Remember Better: The Science Behind Memory*. Oxford University Press. ISBN 9780197607732. [OUP](https://global.oup.com/academic/product/why-we-forget-and-how-to-remember-better-9780197607732) | The best popular-but-rigorous treatment, organised around "forgetting is normal and useful". A neurologist and a cognitive neuroscientist. **Budson is first author.** |
| **Moonwalking with Einstein** (2011) | Foer, J. *Moonwalking with Einstein*. Penguin Press. [PRH](https://www.penguinrandomhouse.com/books/307431/moonwalking-with-einstein-by-joshua-foer/) | The best account of the method of loci — literally an indexing scheme. Memory athletes have ordinary memories and extraordinary *encoders*. |
| **The Art of Memory** (1966) | Yates, F. A. *The Art of Memory*. University of Chicago Press. [UCP](https://press.uchicago.edu/ucp/books/book/chicago/A/bo91674300.html) | The 2,500-year history of deliberate memory architecture, before the printing press made it unnecessary. |
| **Remember** (2021) | Genova, L. *Remember: The Science of Memory and the Art of Forgetting*. Harmony. [PRH](https://www.penguinrandomhouse.com/books/622269/remember-by-lisa-genova/) | Shortest path to the "everyday forgetting is a feature" framing. |
| **Memory: A Very Short Introduction** (2009) | Foster, J. K. Oxford University Press. ISBN 9780192806758. [OUP](https://global.oup.com/academic/product/memory-a-very-short-introduction-9780192806758) | Clean textbook-grade primer. |
| **Memory** (3rd ed. 2020; 4th ed. 2025) | Baddeley, A., Eysenck, M. W., & Anderson, M. C. Routledge. [Routledge](https://www.routledge.com/Memory/Baddeley-Eysenck-Anderson/p/book/9781032592084) | The reference work. |
| **How We Learn** (2020) | Dehaene, S. *How We Learn: Why Brains Learn Better Than Any Machine… for Now*. Viking. [PRH](https://www.penguinrandomhouse.com/books/606196/how-we-learn-by-stanislas-dehaene/) | The neuroscience-versus-ML comparison, and a useful counterweight to "scale is all you need". |
| **Forgetting** (2021) | Small, S. A. *Forgetting: The Benefits of Not Remembering*. Crown. [PRH](https://www.penguinrandomhouse.com/books/622823/forgetting-by-scott-a-small/) | Best single-authored case that forgetting is adaptive. |
| **Forgetting: Myths, Perils and Compensations** (2015) | Draaisma, D. Yale University Press. [Yale](https://yalebooks.yale.edu/book/9780300207286/forgetting/) | The historical/cultural companion. Cite it for "the idea that forgetting is failure is a modern prejudice." |

---

## 6. Mechanism → closest AI analogue

**Read this as analogy only.** Nothing here claims AI should copy biology.
Biological mechanisms are products of evolution under constraints — metabolic
cost, development time, survival — that do not apply to silicon. The value of
the mapping is **diagnostic**: it shows which design questions a system has
answered and which it has silently defaulted on.

| Biological mechanism | Closest AI analogue | Where it holds | Where it breaks |
|---|---|---|---|
| Ebbinghaus decay curve | Exponential/TTL decay; recency-weighted score | Both monotone, front-loaded, asymptoting | Biological decay is item-history-dependent (power-law from cascade metaplasticity); TTL uses time-since-write, the wrong variable |
| Müller & Pilzecker retroactive inhibition | Write buffer / post-write flush window | Both say a memory is vulnerable to what arrives right after it | Biological interference is content-mediated (similar material hurts more); write queues are content-blind |
| Atkinson–Shiffrin control processes | Retrieval/reranking policy, query rewriting, routing | Both separate architecture from tunable policy | Human control processes are learned and metacognitive; agent policies are usually hand-coded heuristics |
| Tulving episodic/semantic | Raw event logs + provenance vs distilled facts/schemas | Both distinguish context-bound from context-free | Biological semantic memory is co-constructed during encoding, not batch-summarised from episodic logs. The one-way "log → summary" pipeline is an AI invention |
| Baddeley's episodic buffer | A working-context object binding retrieved chunks + task state + current turn | Both limited-capacity, multimodal, exist to **bind** | **No widely adopted analogue. The largest genuine gap.** |
| Working-memory capacity (~4 chunks) | Context budget; top-k | Both hard limits on simultaneously active items | Chunking makes human capacity unbounded in information; context windows scale in tokens and degrade non-linearly with position |
| Levels of processing | The write/encode function: summarise, link, extract entities, attach provenance | Both: encoding depth sets durability | AI can re-encode cheaply and repeatedly; humans largely cannot. The AI lever is re-processing |
| Need probability | Retrieval score as estimate of P(item needed \| history) | The correct objective, directly estimable from logs | Environmental statistics differ across deployments; a global decay constant is a stationarity assumption |
| Storage vs retrieval strength | Persisted record vs current score under this query | Both separate "have it" from "can reach it" | Retrieval strength moves with **use** in biology, with **time** in most stores. Reads should update scores |
| Active forgetting | Explicit delete/suppress API driven by policy | Both controller-driven, not passive decay | Human suppression is cue-specific and reversible; AI deletes are global and irreversible |
| Engram accessibility | Stale index entries, tombstones, unreachable vectors | Both loss-of-access rather than loss-of-payload | Biological accessibility fluctuates with brain state; AI index availability is binary |
| Synaptic cascade (Fusi) | Metaplastic per-parameter learning rate; weight age | Both make overwrite cost depend on history | Cascade models need many discrete states; implementations approximate with one scalar |
| Neurogenesis as regulariser | Random unit reinitialisation; dropout | Both show destroying structure improves generalisation | Neurogenesis is spatially and temporally structured; random reinit is not |
| Intrinsic forgetting | Background compaction job competing with writes | Both frame erasure as default | Biological forgetting is dopamine-gated and salience-modulated; a cron job is not |
| Hippocampal index | Vector index holding **addresses** to content stored elsewhere | Both let a partial cue reinstate a full pattern | Biological index is sparse and pattern-separated; dense embeddings are the opposite and suffer interference |
| Complementary learning systems | Fast episodic store + slow weights, with replay | The two-timescale split is the best-validated structure in the literature | Biological replay happens offline during sleep; online experience replay has no such separation |
| Sleep consolidation | Offline batch job: dedupe, merge, abstract, re-index | Both need a window in which encoding is **off** | Human sleep is a whole-brain state change with neuromodulatory gating; a batch job shares the serving substrate and its failure modes |
| Synaptic homeostasis | Capacity renormalisation; periodic re-embedding | Both start from a finite plasticity budget | SHY predicts net **down-scaling**, which AI systems rarely do — they add capacity instead |
| Reconsolidation | Read-modify-write with a labilisation window | Both make retrieval an opportunity to change the record | Human reconsolidation requires protein synthesis and can be blocked; no AI analogue of that cost |
| NeoHebbian / dopamine-gated LTP | Write-enable gated on prediction error or reward | Both: commit only when the model was wrong | Biological RPE is a scalar broadcast; AI surprise estimates are per-token and often miscalibrated |
| Stress asymmetry | Separate salience weights for write and read paths | Both phase-dependent | Human stress is a global endocrine state with slow onset/offset; AI has no equivalent mode |
| Reconstructive memory (Bartlett) | Generation conditioned on retrieved context (RAG does this implicitly) | Both produce plausible reconstruction, not verbatim copy | Human reconstruction is **unmarked** — the rememberer cannot tell. AI systems *can* mark provenance, and should |
| Loftus effect | Query phrasing altering what is retrieved and re-written | Both: the read path contaminates the store | Human contamination is durable and hard to detect; AI can log it. Choosing not to is a design choice |

**Three rows worth pulling out as design principles.** (i) *Bjork:* reads should
move retrieval scores; a pure read path is biologically wrong and operationally
wasteful. (ii) *Ryan & Frankland:* the dominant failure to model is
**unreachable-but-present**, not deleted. (iii) *Bartlett:* if your retrieval
path is generative — and in an LLM agent it always is — you already have
reconstructive memory; you are just not marking it.

---

## 7. What AI people get wrong about human memory

**1. "Memory is retrieval."** It is **reconstruction**. Bartlett (1932)
established recall as schema-guided reconstruction; Schacter & Addis (2007)
reframed it as *constructive episodic simulation* — the same machinery supports
remembering the past and imagining the future. A system optimised for exact
playback would be the wrong design even if it were possible.

**2. "Forgetting is passive decay or a bug."** It is an **active, controlled
operation**. Prefrontal cortex exerts top-down inhibitory control over
hippocampal traces (Anderson & Hulbert 2021); in *Drosophila* the default state
may be erasure, with consolidation as the active competitor (Davis & Zhong 2017).

**3. "Forgetting means the information is gone."** The dominant mode is **loss
of accessibility**. Silent engrams were recovered optogenetically (Roy et al.
2017); forgetting is circuit remodelling that moves an engram from accessible to
inaccessible (Ryan & Frankland 2022). Modelling forgetting as deletion models
the rare case.

**4. "Capacity limits are a bug to be engineered away."** They are the
**mechanism of abstraction**. Knowledge acquisition and automatisation *require*
discarding instances (Nørby 2015); the focus-of-attention limit is ~4 chunks
(Cowan 2001), and chunking is how humans exceed it — by changing the unit, not
by enlarging the buffer. A system with unlimited context and no compression is
not superhuman memory; it is a system that cannot form a concept.

**5. "Consolidation is free, or can happen inline."** It is **compute-bounded**.
Plasticity saturates and must be renormalised (Tononi & Cirelli 2014); waking and
sleep are incompatible modes (Rasch & Born 2013). Müller & Pilzecker said the
same in 1900: the post-encoding window is fragile precisely because
consolidation is happening there.

**6. "Memory is a store you write to and read from."** Retrieval **is a write**
(Nader et al. 2000), and retrieval strengthens memory more than restudy does
(Bjork & Bjork 1992). A system whose read path has no side effects is not a
simplified brain — it is missing a mechanism biology uses for both learning and
corruption.

**7. "Similarity is the right retrieval signal."** The right signal is **need
probability** — an estimate of how likely this item is to be required *now*,
which is a function of frequency, recency and spacing, not of semantic distance
to the query (Anderson & Schooler 1991).

**8. "Memory is one module."** It is at minimum declarative/nondeclarative
(Squire 2004), further split into episodic/semantic/procedural, organised by SPI
relations (Tulving 1995). Weights, indexes and policies are different systems.
Treating "the memory layer" as a single component recurses the 1960s modal-model
mistake.

**9. "Storing more is strictly better."** Yalnizyan-Carson & Richards (2022) ran
the experiment: an agent that forgot a large fraction of older memories
performed *better* than an unbounded one. Tran et al. (2022) found the same at
the network level.

**10. "Memory fidelity is the goal."** The goal is **decision quality**
(Richards & Frankland 2017). Everything in Section 6 follows from taking that
sentence literally — including that a perfectly faithful memory is a
*pathological* one.

**11. "Salience is one knob."** It is at least two, with opposite signs.
Glucocorticoids enhance consolidation while impairing retrieval (de Quervain et
al. 2009); stress effects differ by subsystem and developmental timing (Lupien
et al. 2009). A single importance score applied to both write and read paths is
not a simplification of biology — it is a different computation.

**12. "Forgetting is time-based."** It is **history- and state-based**.
Retention follows from a cascade of per-synapse plasticity states producing
power-law rather than exponential forgetting (Fusi et al. 2005); accessibility
fluctuates with brain state on short timescales (de Snoo & Frankland 2025). A
`TTL` field computes `now - created_at`, which is the one variable neither paper
thinks is doing the work.

---

## The one-sentence thesis

If this repo carries only one sentence, it should be this:

> Memory is not a store but a **prediction machine about future demand**, and
> forgetting is the mechanism that keeps its predictions calibrated — so a
> system that never forgets is not a system with better memory, it is a system
> that has stopped estimating.

---

*See also: [02-cognitive-architectures.md](02-cognitive-architectures.md) for the
computational tradition that tried to build memory from these findings, and
[00-timeline.md](00-timeline.md) for the chronological thread.*
