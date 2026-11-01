# Infrastructure: Stores, Indexes, and Protocols

**Research date:** 2026-11-01

Everything above this document is about what memory should do. This one is about
what breaks when you build it.

The central claim is uncomfortable: **almost every production failure of an agent
memory system is a storage-layer failure, not a model failure.** The retrieval
returns the wrong thing because the filter was applied after the top-k. The
memory silently disappears because the index exceeded a budget and truncated. The
tenant sees another tenant's data because isolation was a `WHERE` clause on a
shared collection. The deletion doesn't delete because the vector is still in the
HNSW graph.

None of these are reasoning problems. All of them are the reason a system that
scores well on a benchmark fails in production.

---

## 1. What the stack actually is

An agent memory system is not a database. It is at minimum six things, and they
fail independently:

| Layer | Job | Typical choice | Failure mode |
|---|---|---|---|
| **Event log** | durable record of what happened | append-only table, object store, Iceberg | compaction loses provenance |
| **Derived store** | extracted facts, entities, summaries | Postgres, document DB | drift from the log; no rebuild path |
| **Vector index** | similarity search | HNSW / IVF / DiskANN | recall collapse under filtering |
| **Lexical index** | exact and rare-term match | BM25, FTS5, Tantivy | vocabulary mismatch across sessions |
| **Graph** | relations, multi-hop | property graph, RDF | stale edges; unbounded traversal |
| **Context assembler** | select and order what enters the prompt | application code | position effects, budget overflow |

**The architectural point.** The log is the only thing that must be durable;
everything else is derivable. Systems that get this right can rebuild an index
after changing embedding models. Systems that don't are permanently coupled to
their first model choice, and the coupling only becomes visible when they try to
upgrade.

This is the same distinction as storage strength vs retrieval strength
([01-human-memory.md](01-human-memory.md) §1), applied to infrastructure: the log
is what you still have; the indexes are what you can currently reach. Rebuilding
is the engineering equivalent of relearning from a preserved trace, and it is
only possible if you kept the trace.

---

## 2. Vector indexes: the three-way trade-off

Every approximate nearest-neighbour structure trades **recall** against
**latency** against **memory**, and you cannot have all three. Knowing which one
you gave up is the whole skill.

### HNSW

Malkov, Y. A., & Yashunin, D. A. (2020). Efficient and robust approximate nearest
neighbor search using Hierarchical Navigable Small World graphs. *IEEE TPAMI*,
42(4), 824–836. DOI
[10.1109/TPAMI.2018.2889473](https://doi.org/10.1109/TPAMI.2018.2889473).

A multi-layer proximity graph: sparse upper layers for long jumps, dense lower
layers for local refinement. Search descends greedily from the top. Three
parameters matter:

`M` — edges per node. Higher means better recall and more memory; typical 16–64.
`efConstruction` — the candidate list size during build. Higher means a better
graph and slower ingestion.
`efSearch` — the candidate list size during query. Higher means better recall and
worse latency, and **it is the only one you can change at query time.**

Two properties that matter for memory systems specifically.

*HNSW is bad at deletion.* Removing a node means repairing its neighbourhood or
leaving it as a tombstone that search still traverses. Most implementations
tombstone and rebuild periodically. This is why "delete" in a vector store is
usually "mark and compact later," and why provable deletion
([06-security.md](06-security.md)) is hard: the payload may be gone while the
graph still encodes its presence.

*HNSW degrades gracefully but not monotonically with duplicates.* Near-duplicate
embeddings cluster into dense regions and the graph search spends its budget
there. Deduplication before indexing is not an optimisation; it is a recall
requirement.

### IVF and quantization

**IVF** k-means partitions the space into cells and searches only the nearest
`nprobe` cells. Cheap to build, easy to update incrementally, but recall falls
off badly at partition boundaries and `nprobe` must be tuned per dataset.

**Product quantization** compresses each vector into a sequence of centroid IDs,
trading memory for precision. **Scalar quantization** (int8) is a 4× reduction
with modest loss and is usually the right default. **Binary quantization** is 32×
with a large loss, workable only with rescoring.

The combination that dominates at scale is **IVF + PQ** or **HNSW + SQ**, and the
practical rule is that quantization errors are acceptable *if you rescore the top
candidates exactly*. Rescoring is the cheap fix almost everyone forgets.

### The GraphANN observation

Disk-based graph indexes (DiskANN, and the `Vamana` graph it uses) exist because
at a certain scale the index no longer fits in RAM, and a memory-resident HNSW
that has spilled to swap is slower than a well-designed disk-resident graph.
Subbareddy, S., et al. (2019), *DiskANN: Fast Accurate Billion-point Nearest
Neighbor Search on a Single Node*, NeurIPS.

The practical threshold: an HNSW index at 1M × 1536-dim float32 costs roughly
6 GB before overhead — fits comfortably. At 100M it does not, and the design
choices change completely.

---

## 3. Filtering: where recall silently dies

This is the single most consequential and least-discussed detail in agent memory
infrastructure, and it is worth stating precisely.

A memory query is almost never pure similarity. It is "similar to this query,
**among memories belonging to this user, in this project, not invalidated, created
after date D**." Those are filters, and where you apply them changes the answer
completely.

**Post-filtering (naive).** Search the index for top-k, then discard results that
fail the filter. The problem: if the filter selects 1% of the corpus, the top-k
global results contain almost none of the relevant items, and the query returns
two results instead of ten. Recall collapses toward zero *exactly when the
filter is most selective* — that is, in the multi-tenant case where correctness
matters most.

**Pre-filtering (exact).** Compute the filter first, then search only within the
matching subset. Correct, but it destroys the index: HNSW cannot efficiently
search an arbitrary subset, so implementations either fall back to brute force
over the subset (fine for small subsets, catastrophic for large ones) or build a
separate index per filter value (fine for a handful of values, impossible for
millions of users).

**Single-stage filtered search.** The correct answer, and what modern engines
implement: traverse the graph but only accept nodes passing the filter, with the
candidate list sized to compensate. Recall is preserved at the cost of a slower
query, and performance degrades with filter selectivity in a *predictable* way
rather than catastrophically. Qdrant calls this filtered HNSW and documents the
trade-off explicitly
([qdrant.tech/documentation/concepts/filtering](https://qdrant.tech/documentation/concepts/filtering/));
pgvector exposes it as iterative index scans
([github.com/pgvector/pgvector](https://github.com/pgvector/pgvector)).

**The design rule.** If your filter selects less than ~10% of the corpus, and you
are post-filtering, you have a correctness bug that will appear as "the assistant
forgets things" in the multi-user case and never in the demo, because the demo
has one user.

**What this means for agent memory specifically.** Agent memory is *intrinsically*
heavily filtered — by user, by project, by agent, by validity, by recency. It is
the worst possible workload for a naive vector store, and most agent frameworks
use exactly that. This is one reason the index-plus-files architecture
([03-agent-native-memory.md](03-agent-native-memory.md) §2) outperforms: a
filesystem is *all* pre-filter, and the filter is free because the path is the
filter.

---

## 4. Hybrid search: lexical plus vector

Pure vector search fails on exactly the queries agents care most about: identifiers,
error codes, function names, proper nouns, and rare terms. `ERR_CONN_RESET` has no
useful embedding. Pure lexical search fails on paraphrase. Every serious memory
system runs both and fuses.

**Reciprocal Rank Fusion** (Cormack, G. V., Clarke, C. L. A., & Büttcher, S.
(2009), *Reciprocal rank fusion outperforms Condorcet and individual rank learning
methods*, SIGIR) is the default because it is parameter-light and robust:

```
score(d) = Σ_r  1 / (k + rank_r(d))
```

with `k = 60` as the conventional constant. It uses only ranks, so it needs no
score calibration between systems — which is its virtue, because cosine
similarities and BM25 scores are not comparable and never will be.

**Score fusion** (normalising and summing) is better when you can calibrate and
worse when you cannot. In practice most teams start with RRF and stay there.

**The failure mode both share.** Fusion can promote a document that is mediocre on
both signals above one that is excellent on one. RRF's `k` controls how much a
top rank can dominate; small `k` rewards a single strong signal, large `k`
flattens toward agreement. `k = 60` is a compromise, not a law.

**The detail worth knowing.** RRF is rank-based, so it **discards magnitude** — a
document at rank 1 with cosine 0.95 and one at rank 1 with cosine 0.55 are
identical. For agent memory, where the difference between "this is the user's
name" and "this is vaguely about names" is exactly the magnitude, that loss
matters. The practical fix is a hybrid: RRF for the candidate set, then a
cross-encoder or a raw score for the final ordering.

---

## 5. Choosing a store: the honest comparison

| Option | Vector | Lexical | Graph | Transactions | Best for | Fails at |
|---|---|---|---|---|---|---|
| **PostgreSQL + pgvector** | HNSW / IVFFlat | `tsvector` + GIN | recursive CTE | yes | one system that must be correct | very large vector counts; filtered recall needs tuning |
| **SQLite + FTS5 + sqlite-vec** | brute force / small HNSW | FTS5 BM25 | no | yes | single-agent, local, portable | scaling past ~1M vectors |
| **Qdrant / Weaviate / Milvus** | first-class HNSW + payload filters | varies | varies | limited | dedicated vector workloads with heavy filtering | not a system of record |
| **Neo4j / Memgraph** | secondary | secondary | first-class, Cypher | yes | relation-heavy memory (Zep-style) | vector recall; operational weight |
| **Elasticsearch / OpenSearch** | secondary (kNN) | first-class BM25 | no | limited | text-heavy with existing infra | consistency; cost |
| **ClickHouse** | experimental | tokenbf / ngram | no | no | append-only event analytics | point updates |
| **Iceberg + Trino/Spark** | no | no | no | no (ACID on the table) | the durable log; replay and rebuild | anything interactive |

**Three recommendations that are actually opinions.**

*Start with Postgres plus pgvector.* One system, real transactions, real
filtering, real joins, and a query planner that will tell you when you are doing
something stupid. It handles agent memory at the scale almost every agent
actually operates at. Move off it when you have a measured reason.

*Keep the log separate from the index.* An append-only table (or object storage
with a manifest) that records every write, including supersessions. The index is
rebuildable; the log is not. This is what makes embedding-model migration, index
migration, and audit possible at all.

*Do not use a graph database for the graph.* For most agent memory, the
relations you need are shallow (one or two hops) and the queries are simple. A
`relations` table with the right indexes outperforms a graph database on cost and
operational burden until you genuinely need variable-length path queries — at
which point you will know, because you will have written the recursive CTE and
hit its limits.

---

## 6. Temporal modelling

Three designs, in increasing order of correctness and cost.

**Overwrite.** Update the row. Cheapest, and loses the ability to answer "what did
we believe last week." Almost every early memory system does this and then
cannot explain a wrong answer.

**Soft delete with a tombstone.** Keep the row, set `deleted_at`. Answers "was
this ever true" but not "when did it stop being true," and it makes deletion
harder ([06-security.md](06-security.md)).

**Bi-temporal.** Two time axes per fact: **valid time** (when it was true in the
world) and **transaction time** (when the system recorded it). Contradiction sets
`t_invalid` rather than deleting; query "as of" either axis. This is what Zep /
Graphiti implements
([04-research-architectures.md](04-research-architectures.md) §4), and it is the
correct model.

The reason bi-temporality is worth the cost: it is the only design that can answer
the three questions an agent actually gets asked. *What do I know now?* Filter
`t_invalid IS NULL`. *What did I know when I answered that?* Filter on transaction
time. *What was true in the world then?* Filter on valid time. A single-timestamp
schema can answer one of the three.

**The implementation that fits Postgres.** Two `timestamptz` columns plus an
exclusion constraint on `(entity_id, tstzrange(valid_from, valid_to))` is the
whole thing, and it costs essentially nothing until you need it — which is the
argument for doing it from the start.

---

## 7. Multi-tenancy and isolation

Memory is per-user, per-project, per-agent, and sometimes per-worktree
([03-agent-native-memory.md](03-agent-native-memory.md) §5). Getting isolation
wrong is a data breach, not a bug.

**Three levels, and the trade-off.**

*Collection / schema / database per tenant.* Perfect isolation, trivial deletion
(drop the collection), expensive when tenant count is large — each HNSW index has
fixed overhead, so 10,000 tenants × 10,000 vectors each is 10,000 small indexes
and a lot of waste.

*Shared collection with a tenant field.* Efficient, and the only design that
scales to consumer-scale tenant counts. Requires disciplined filtering on **every**
query, and inherits the post-filter recall collapse of §3.

*Row-level security.* Postgres RLS gives you the shared-collection efficiency with
enforced isolation — the policy is applied by the engine, so a missing `WHERE`
clause is a permission error rather than a leak. This is the best available
compromise and is badly underused in agent frameworks.

**The rule that matters.** Isolation must be enforced at a layer the application
cannot forget. Application-level `WHERE tenant_id = ?` is a convention; a
database policy is an invariant. The first produces incidents.

---

## 8. The KV cache as a memory tier

The most under-exploited insight in this document.

A transformer's KV cache for a prefix is a **content-addressed, reusable
computation over that prefix**. If two requests share a prefix, the second can
skip the prefill entirely. Providers price this explicitly — cached input tokens
cost a fraction of uncached ones — and the discount is large enough that prefix
design has become an optimisation discipline in its own right.

**Why this belongs in a memory document.** MemOS treats activation memory as a
first-class memory substrate alongside plaintext and parameters
([04-research-architectures.md](04-research-architectures.md) §4), and it is
right. A KV cache entry is a memory: it encodes content, it can be stored and
reloaded, and holding it is the difference between a fast system and a slow one.

**The design consequence.** **Put stable content first.** System prompt, then
instructions, then the memory index, then retrieved material, then the query.
Everything before the first volatile token is cacheable across requests; anything
after it invalidates the cache. A memory system that injects a timestamp at the
top of the prompt has made its entire prompt uncacheable for no reason.

This is a real cost: at scale the difference between a cacheable and a
non-cacheable prompt is a large multiple of the bill. It is also entirely a
*ordering* decision, which means it costs nothing to get right and is invisible
when you get it wrong.

---

## 9. Protocols: what standardises, and what doesn't

Memory has no standard, and the standards that exist were written for adjacent
problems. This is the current state as of late 2026.

### MCP

The Model Context Protocol standardises how a model reaches tools and resources.
Its **2026-07-28 revision removed sessions** — the protocol is now stateless,
which means a memory server cannot assume continuity between calls and must key
everything on explicit identifiers passed in the request.

The consequence is real and mostly unremarked: **MCP was the natural place for a
standard memory interface, and the stateless revision closed that door.** A
memory server over MCP is a database with a tool-shaped API, not a memory. Every
implementation does the same thing — re-implement extraction, consolidation, and
retrieval inside its own server, with no interoperability.

### A2A

The Agent2Agent protocol standardises agent-to-agent delegation. It **explicitly
does not share memory** — the specification disclaims it, and the design gives
each agent its own context.

This is a decision, and it is defensible on security grounds. It also means a
team of agents cannot accumulate shared knowledge, and the only way to share is
for one agent to send another a message that the second stores as its own memory
— losing provenance in the process.

### AGENTS.md

A convention, not a protocol: a Markdown file at the repo root that coding agents
read as instructions. It reached >60,000 open-source projects and was donated to
the Linux Foundation's Agentic AI Foundation, which makes it the most widely
adopted agent standard of any kind.

Its significance for memory is negative: it standardised the **instructions**
layer and left the memory layer unstandardised. That is why every product in
[03-agent-native-memory.md](03-agent-native-memory.md) reimplements memory
independently while sharing a common instructions format.

### The stalled attempts

The IETF `aimem-bundle` draft proposes a portable memory container format. The
W3C memory-interoperability community group is discussing the same problem. Both
exist; neither has adoption. **Agent Memory Repo** (Devin's spec: a git repo with
`MEMORY.md` as entry point, one-line entries with `[source: …; added: …]`, and
`[[path]]` links) is the only format with a shipping implementation behind it,
and it is one vendor's.

**The honest assessment.** Memory interoperability will not be solved by a
protocol until someone has a product reason to make their memory exportable.
Claude and Kimi already offer import/export; that is where the pressure is
coming from, and it is a consumer-protection pressure rather than an engineering
one.

---

## 10. The failure modes, ranked by how often they happen

**1. Post-filter recall collapse.** §3. Present in most agent frameworks. Invisible
with one user, catastrophic with many.

**2. No rebuild path.** The index is the only copy, the embedding model is
deprecated, and there is no way to migrate. Every system that stored embeddings
without storing the source text is in this position.

**3. Silent truncation.** The index overflows its budget and the oldest entries
are dropped without a log line. Every product with a budget has some version of
this ([03-agent-native-memory.md](03-agent-native-memory.md) §7).

**4. Unbounded traversal.** Graph retrieval that expands until it hits a
neighbourhood of the whole graph. HippoRAG's PageRank is bounded by construction;
hand-rolled graph memory usually is not.

**5. Duplicate flooding.** The same fact written a hundred times, filling the
top-k and crowding out everything else. Deduplication at write time is the fix
and almost nobody does it because it requires a similarity check on every write.

**6. Timestamp-as-validity.** Using `created_at` to decide what is currently true.
This is why systems insist the user still lives in the city they mentioned two
years ago.

**7. Cache-hostile prompt assembly.** §8. Costs money and nobody notices.

**8. The deletion that isn't.** §6 and [06-security.md](06-security.md). A
soft-deleted vector still contains the semantic information, and a HNSW graph
still encodes its neighbourhood.

---

## 11. If you are building this

A short, opinionated sequence.

**Store the raw event first, in an append-only table with a monotonic ID.** Do
this before you have an opinion about schema. It is the only decision that is
expensive to reverse.

**Put extracted facts in Postgres with two timestamp columns and a `t_invalid`
range.** Bitemporal from day one; it costs nothing and buys the three queries
that matter.

**Add pgvector on the facts table, with the filter columns indexed.** Use
iterative index scans for filtered queries; verify recall with a filtered query
at 1% selectivity before you ship.

**Add FTS5 or `tsvector` for lexical, and fuse with RRF.** Then rescore the top
20 exactly.

**Build the index the products built.** A `MEMORY.md` with a hard budget, topic
files behind it, and a `[source: …; added: …]` line on every entry. It is simple
and it works, and it costs one afternoon.

**Put stable content at the front of the prompt and volatile content at the
back.** Then measure your cache hit rate, because you will be surprised.

**Write the rebuild script before you need it.** A function that takes the event
log and produces the store, runnable from scratch. If you cannot rebuild, you do
not own your memory.

---

*See also: [03-agent-native-memory.md](03-agent-native-memory.md) for what shipped,
[06-security.md](06-security.md) for deletion and tenancy as security properties,
and [04-research-architectures.md](04-research-architectures.md) for the systems
these layers support.*
