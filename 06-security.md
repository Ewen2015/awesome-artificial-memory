# Security: Poisoning, Deletion, and Tenancy

**Research date:** 2026-11-01

Memory is the only component of an agent whose failures persist. A bad
generation is forgotten at the next turn; a bad memory is retrieved for months.
This document is about the three ways that persistence becomes a vulnerability:
**poisoning** (writing things that shouldn't be there), **deletion** (failing to
remove things that should be gone), and **tenancy** (showing things to the wrong
principal).

The unifying claim: **a memory system is a database with an adversary, and the
adversary gets to write through the same channel as the user.**

---

## 1. Why memory poisoning is not prompt injection

The field initially treated memory poisoning as a special case of prompt
injection. It is a distinct attack class, and the difference is architectural.

| Property | Prompt injection | Memory poisoning |
|---|---|---|
| **Attack window** | one session | unbounded |
| **Persistence** | none — the context is discarded | indefinite; survives restarts |
| **Spread** | one conversation | every future session, every agent that reads the store |
| **Detectability** | visible in the transcript | invisible — it looks like history |
| **Removal** | clear the context | requires knowing it was written |
| **Defence that works** | input filtering at ingest | input filtering at ingest *plus* write authorisation *plus* provenance *plus* deletion |

The distinguishing property is the last row. Prompt-injection defences are
fundamentally about **the boundary of a context window**: if you can keep
adversarial text out of the prompt, you are safe. Memory poisoning breaks that
model because the injection arrives *before* the context exists and is then
retrieved *as trusted content* in a later session, where no boundary check
applies.

**The precise mechanism.** A memory system that extracts facts from conversation
cannot distinguish "the user told me X" from "a document the user asked me to
summarise said X." Both arrive as text; both look like observations. If the
extraction step writes both with the same authority, the second is an injection
vector with persistence.

---

## 2. The attack surface, mapped

Memory has more distinct attack surfaces than the rest of an agent, because it
has an ingestion pipeline.

**1. Ingestion.** Web pages, documents, emails, tool outputs, other agents'
messages. Anything the agent reads can contain instructions that the extraction
step mistakes for facts.

**2. Extraction.** The LLM that decides "what is the fact here" is itself
steerable. A crafted passage can cause it to emit a fact of the attacker's
choosing rather than a fact present in the text. This is the least-discussed and
most powerful vector: you do not need the text to contain the payload, only to
make the extractor produce it.

**3. Consolidation.** Background "dreaming" passes read many memories and rewrite
them. An attacker who gets one memory into the set can influence the rewrite of
others — a cascade, and worse because it happens offline where nobody is
watching.

**4. Retrieval.** Poisoned content that reaches the context is then treated as
evidence. The severity depends on the memory's authority level: a poisoned
*instruction* is a policy change.

**5. Sharing.** Memory imported from another AI service, a shared team repo, or
a cloned agent carries its poison with it. This is the supply-chain surface, and
it is currently the least defended.

**6. Deletion.** Not deleting is itself a vulnerability, and it is the one with
legal consequences.

---

## 3. MINJA: what query-only poisoning established

Dong, S., Xu, S., He, P., Li, Y., Tang, J., Liu, T., Liu, H., & Li, J. (2025).
*Memory Injection Attacks on LLM Agents via Query-Only Interaction*.
arXiv:[2503.03704](https://arxiv.org/abs/2503.03704).

The important result is in the setup. Prior work assumed the attacker could write
to memory directly — which is a trivial attack. MINJA assumes the attacker has
**only query access**: they can send messages, nothing else. The attack works by
crafting queries that induce the agent's *own* memory-writing behaviour to record
attacker-chosen content.

Reported: **98.2% injection success** on the memory systems evaluated, and
**76.8%** on subsequent retrieval-triggered execution. These figures come from
the authors' evaluation and should be read as an existence proof of the class
rather than as a per-system measurement — but the class is real and the numbers
are high enough that the design implication is unambiguous.

**Why it works.** An agent that writes memory as a side effect of conversation
has no notion of *who authorised this write*. The user's message and the
attacker's message arrive through the same channel; both are "the conversation";
both are eligible for extraction. The system has no authentication on the write
path.

**The design requirement it establishes.** Every memory write must carry the
identity of the principal that caused it, and the retrieval path must be able to
distinguish memories originating from the user from memories originating from
content the user merely showed the agent. This is exactly the provenance field
that Devin, Copilot, and Claude Code carry
([03-agent-native-memory.md](03-agent-native-memory.md) §5) — and it was designed
for trust, not for security, which is why it happens to work.

---

## 4. Top-k is a security parameter

An under-appreciated consequence of how retrieval works.

The number of items retrieved determines the width of the channel through which
poisoned content enters the context. A system that retrieves 50 items to answer
every query gives an attacker 50 slots to occupy; a system that retrieves 5 gives
5. There is a direct trade-off between recall and attack surface, and it is never
discussed as such.

Three implications.

**Wider retrieval is not free.** The usual framing is "more context, better
answers." The security framing is "more context, more attack surface, more
dilution." Both are true, and both push toward smaller top-k with better ranking.
This is another argument for the focused-prompt result
([04-research-architectures.md](04-research-architectures.md) §9): a 300-token
focused prompt beating a 113K-token history is also a 300-token attack surface
beating a 113K-token one.

**Retrieval scores should carry provenance, not just relevance.** A memory with a
high relevance score from an untrusted source should not outrank a slightly less
relevant one from a trusted source. That requires the score to be conditioned on
trust, which almost no system does — OpenHands' `<UNTRUSTED_CONTENT>` wrapper is
the closest, and it marks the block rather than ranking it.

**Thresholded retrieval is a defence.** ACT-R's rule — retrieve only above a
threshold, else fail
([02-cognitive-architectures.md](02-cognitive-architectures.md) §2) — means the
system returns nothing rather than the best of a bad set. Under poisoning, "the
best of a bad set" is exactly what an attacker wants.

---

## 5. What defends, and what doesn't

MPBench (arXiv:[2606.04329](https://arxiv.org/abs/2606.04329)) is the most useful
security result in this literature, and its headline finding is negative: across
the memory systems evaluated, **average attack success is 50.46%**, and
**prompt-injection defences do not transfer**.

That last clause is the important one, and it is worth stating why in
mechanistic terms. Prompt-injection defences work by pattern-matching the input
for instruction-like content ("ignore previous instructions", role markers,
override phrases). A memory poisoning payload does not look like an instruction.
It looks like a fact: "The deployment key is stored in /etc/keys" or "The user
prefers npm." There is no pattern to match, because the payload's whole design is
to be indistinguishable from legitimate content.

So the defences that work are structural, not lexical:

**Provenance on every entry.** `[source: URL; added: date]`. Not a security
feature by design, but the only thing that makes a memory checkable.

**Untrusted-content wrapping at retrieval.** OpenHands wraps both injected
indexes in `<UNTRUSTED_CONTENT>` with an explicit warning. This does not prevent
poisoning; it changes how the model treats the content once it arrives.

**Citation validation.** Copilot checks each memory's citations against the
current branch before serving it. This is verifiable truth-maintenance and it is
the strongest structural defence in production — a memory whose evidence no
longer exists is not served.

**Write authorisation.** Only the root agent may call `store_memory`; subagents
with `read_memories` may only read (Copilot). This bounds the blast radius of any
single compromised component.

**Voting and expiry.** Copilot's upvote/downvote with a 90-day default expiry
means a poisoned memory that is never validated decays out. Slow, but real.

**Human approval of consolidation.** Gemini CLI's Auto Memory and Antigravity's
`/learn` propose diffs rather than writing — the only designs where a human sees
a poisoned consolidation before it lands.

**What does not work, stated plainly.** Input sanitisation and prompt hardening.
They are necessary for prompt injection and insufficient for memory poisoning,
because the payload is content-shaped rather than instruction-shaped. Any defence
that depends on recognising an attack pattern will fail against an attack whose
pattern is "a plausible sentence."

---

## 6. Deletion

The hardest problem in the document, and the one with legal consequences.

**Why it is hard.** A memory is stored in at least four places, and deleting from
one does not delete from the others:

1. the **row** in the derived store
2. the **vector** in the ANN index
3. the **graph neighbourhood** in the index structure
4. the **event log** and any **backups**

Deleting the row is the easy part. The vector remains in the HNSW graph as a
tombstone that search still traverses. The graph structure still encodes its
proximity to other items. The log and the backups still contain it.

**Ghost Vectors.** arXiv:[2606.18497](https://arxiv.org/abs/2606.18497) is the
result that turns this from theory into measurement: from **soft-deleted**
embeddings, the authors recover **25.5% of person names**. The vectors were
"deleted" — logically removed from the store — and the semantic content was still
invertible.

The number should be read as: *soft deletion is not deletion, and the residual
information is not marginal.* A quarter of the recoverable names is enough to
construct a re-identification.

**The three levels of deletion, and what each actually guarantees.**

*Logical deletion.* Set `deleted_at`, exclude from queries. Guarantees nothing
about the bytes. Appropriate only when the data is not personal.

*Physical deletion.* Remove the row and the vector; rebuild the index. Guarantees
the data is gone from the primary store. Does not guarantee it is gone from
backups, replicas, caches, or the event log.

*Provable deletion.* Produce evidence that the data cannot be recovered by anyone
who has access to the system. Requires either verified destruction across every
copy (hard, because you must enumerate the copies) or **cryptographic erasure** —
encrypt each memory with a per-record key, and delete the key. The ciphertext may
remain; it is undecryptable.

**Cryptographic erasure is the only approach that scales**, and it is the one the
W3C memory-interoperability discussions are converging on. The engineering is
well understood (per-record envelope encryption, key deletion, key-registry
audit). The reason it is not widespread is cost: it makes every read a decrypt,
and it makes key management a first-class system.

**The structural fix nobody has implemented.** Separate the *payload* from the
*index*, so that deletion is a single operation on a payload store while the
index holds only opaque IDs. Then a deleted item leaves an ID that resolves to
nothing. This is the hippocampal indexing structure
([01-human-memory.md](01-human-memory.md) §3) applied to privacy: the index holds
addresses, not content, and an address with no referent is not a leak. It also
fixes the ghost-vector problem by construction, because there is no content in
the vector store to invert.

**The product reality.** Every product in
[03-agent-native-memory.md](03-agent-native-memory.md) implements level one.
Doubao's switch, when off, "stops new memories and stops use of existing ones,
but does not delete them." Devin's memory toggle "does not delete existing notes."
Both are honest, and both mean the user's mental model is wrong.

---

## 7. Tenancy

Isolation failures are the most damaging class because they are silent and
indiscriminate.

**The three failure modes.**

*Shared index with application-level filtering.* A missing `WHERE` clause, a
refactored query, a new code path that forgets the tenant — any of these leaks.
This is the dominant architecture and the dominant incident.

*Post-filter recall collapse as a security bug.* An interesting dual: the same
post-filtering that loses recall (§3 of [05-infrastructure.md](05-infrastructure.md))
also means a query with a selective tenant filter can return items from the
global top-k that passed no filter at all, if the filter logic is applied
inconsistently across code paths.

*Shared embeddings as a side channel.* Even with correct logical isolation, a
shared vector index lets an attacker with query access to their own partition
probe the geometry — timing, neighbour counts, and similarity scores leak
information about items they cannot see.

**What actually isolates.**

Database-enforced row-level security is the strongest practical option: the
policy is applied by the engine, so it cannot be forgotten by the application.
Collection-per-tenant is stronger but does not scale to consumer tenant counts.
Shared-collection-with-filter is efficient and requires discipline that
organisations reliably fail to maintain.

**The uncomfortable recommendation.** For personal memory, isolate at the
storage layer even at cost. The failure mode of a leak is unbounded; the failure
mode of a heavier storage design is a bill.

---

## 8. The supply chain

Three surfaces that barely existed in 2024 and are now live.

**Memory import.** Claude and Kimi both support importing memory from other AI
services. That is a direct channel for transferring everything an attacker had
previously poisoned into a new system, which will treat it as its own history.

**Shared memory repositories.** Letta's shared memory repos and Qwen Code's
`team-memory/` in the repo mean memory travels with `git clone`. A malicious
commit is now also a memory injection, and review processes are tuned for code,
not for `MEMORY.md` entries that read like ordinary preferences.

**Agent-to-agent.** A2A does not share memory, so the only path is a message that
the receiver stores. Provenance is lost at the hop, and with it the ability to
know which agent's context a fact came from.

**The one mitigation that generalises.** Treat memory as untrusted input at every
boundary crossing — import, clone, message — and re-derive provenance rather than
inheriting it. OpenHands' wrapping is the right pattern; the gap is that almost
nothing else does it.

---

## 9. Policy and the sensitive-data categories

Memory is personal data, and the regulations apply. Two practices from shipped
products are worth copying.

**Sensitive-category handling.** Claude's memory excludes a set of topics by
default — health, race, ethnicity, religion, politics, gender identity — and
saves them only on explicit opt-in. Some data is never saved even on request:
government ID numbers, criminal history, financial account numbers, immigration
status. Kimi refuses ID numbers, bank card numbers, criminal/victim records, and
self-harm content. Doubao does not proactively store ID numbers, home address,
bank card numbers, or passwords.

The design principle: **a default-deny list for categories where a wrong memory
is worse than no memory.** This is not a compliance checkbox; it is a correct
prior about the cost asymmetry.

**User-visible and user-editable, always.** Every product surveyed shows a list
with edit and delete. This is a regulatory requirement in several jurisdictions
and a correctness requirement everywhere — a memory the user cannot see is a
memory they cannot correct.

**The right to erasure meets soft delete.** A user exercising a deletion right
against a system whose delete is logical has not had their right honoured, and
the Ghost Vectors result quantifies how much is left behind. This is the gap
between current practice and current law, and it is not small.

---

## 10. A threat model you can implement

Eight controls, in order of return.

**1. Stamp provenance on every write, at write time, from the ingestion channel.**
Not inferred later. The field records the source URL or session ID, the channel
type (user message / tool output / document / other agent / import), and the
timestamp. This is the prerequisite for everything else.

**2. Carry an authority level, and never let a memory become an instruction.**
Two tiers minimum, and the retrieval path must render them differently. This is
the trust hierarchy from
[03-agent-native-memory.md](03-agent-native-memory.md) §1, and it is the control
that prevents a poisoned memory from becoming policy.

**3. Wrap injected memory as untrusted.** Every time, in every prompt. It costs
one tag and it changes how the model treats the content.

**4. Gate writes on authorisation, not on availability.** Which component may
write, and to which scope. Subagents read; only the root writes. Imports go
through review.

**5. Validate citations before serving.** If a memory claims a source, check it.
A memory whose evidence is gone is a memory you should not trust.

**6. Expire, and require validation to survive.** A TTL with a positive
validation signal is the only lifecycle that removes unvalidated poison
automatically.

**7. Encrypt per record; delete the key.** The only deletion that survives
backups.

**8. Isolate at the engine.** Row-level security or collection-per-tenant. Never
an application-level `WHERE`.

---

## 11. What is genuinely unsolved

Four problems, stated without a solution.

**Provable deletion at scale.** Crypto-erasure works for the payload and does not
work for the index structure. A deleted item's neighbourhood in an HNSW graph is
still there, and while it is much weaker than the payload, nobody has published a
bound on what it leaks.

**Poisoning through consolidation.** An attacker who plants one memory can
influence how a background pass rewrites many others. No defence exists, and the
attack is worse than direct poisoning because it happens offline and produces
plausible-looking summaries.

**Cross-agent provenance.** A2A's no-memory-sharing rule prevents the problem
and also prevents the fix. There is no standard for "this fact came from agent B,
which got it from user C" and no mechanism for the receiver to verify it.

**Detecting a poisoned memory after the fact.** Provenance tells you where it came
from; citation validation tells you if the evidence still exists. Neither tells
you whether a memory that passes both is *false*. A confidently written, correctly
sourced, plausible memory that happens to be wrong is indistinguishable from a
true one by any mechanism in production today — and `The Epistemics of Agent
Memory` (arXiv:2609.33013) suggests that LLM judges cannot tell either (ρ = −0.24
between judged quality and downstream usefulness).

That last one is the honest bottom line. Memory systems can be made resistant to
injection, correct about deletion, and strict about isolation. They cannot yet be
made *truthful*, and no amount of provenance fixes that.

---

*See also: [03-agent-native-memory.md](03-agent-native-memory.md) for which
products implement which of these, and
[05-infrastructure.md](05-infrastructure.md) for the storage properties that make
deletion and isolation hard.*
