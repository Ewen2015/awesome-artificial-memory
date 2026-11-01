# Agent-Native Memory: How Agents Actually Manage Themselves

**Research date:** 2026-11-01

This document is an independent survey of how nineteen shipped agent products
manage memory, built from each vendor's own documentation rather than from
marketing or third-party write-ups. Every claim below was read from a page
listed in the [sources](#sources) section. Where a detail is not in the docs, it
is either absent or marked unverified.

It is also an argument. The empirical finding is that **six independent teams
converged on the same architecture without coordinating**, and understanding why
tells you more about agent memory than any of the research papers do.

---

## 1. The vocabulary problem comes first

"Memory" is used by vendors to mean at least four unrelated things, and most
confusion in this field — including in the academic literature — comes from
conflating them.

| Artefact | Who writes it | Persists | Survives session end | Authority |
|---|---|---|---|---|
| **Instructions** (`CLAUDE.md`, `AGENTS.md`, `QWEN.md`, `GEMINI.md`, rules files) | the human | yes | yes | high — treated as standing orders |
| **Durable memory** (agent-authored notes, extracted facts) | the agent | yes | yes | low — treated as recall, explicitly *not* policy |
| **Episode store** (transcript, event log, session state) | deterministic code | yes | yes | none — evidence, not instruction |
| **Reference material** (docs, files, retrieved context) | the human or a system | yes | yes | context-dependent |

The distinction that matters most, and that every mature product states
explicitly, is **instructions vs memory**. It is not a naming convention; it is a
trust hierarchy.

ChatGPT's documentation says to keep required guidance in `AGENTS.md` and treat
memories as "a helpful recall layer, not… the only source for rules that must
always apply." GitHub Copilot says repository facts "should not be relied on as a
rule that will always be followed… use custom instructions instead." OpenHands
distinguishes instructions "addressed to *any* agent" from what this agent
learned. Qwen Code states the policy in one line: "auto-memory is best-effort,
`QWEN.md` is guaranteed."

**Why this hierarchy exists.** An agent-authored memory is written by a system
that can be manipulated by content it processes. An instruction file is written
by a human who is accountable. Collapsing the two means a poisoned memory
silently becomes policy — which is precisely the attack class documented in
[06-security.md](06-security.md). The trust hierarchy is a security control that
happens to also be a usability feature.

**The consequence for design.** Any memory system should be able to answer: *is
this a fact I recall, or a rule I must follow?* If the answer is stored in the
same place with the same authority, the system has no answer.

---

## 2. The convergence: an index plus topic files

Six products, built by six unrelated teams, arrived at the same architecture.

**Claude Code.** `~/.claude/projects/<project>/memory/MEMORY.md` is an index;
"one topic file per memory." Only `MEMORY.md` is loaded at session start — its
first 200 lines or 25 KB, whichever comes first — and topic files are read on
demand with ordinary file tools.

**Qwen Code.** `~/.qwen/projects/<project>/memory/` with `MEMORY.md` as "a
compact index, not the store." Budget: **200 lines and 25,000 UTF-16 code
units** shared between the generated body and the prompt-loaded portion.

**Devin.** A memory drive — "a persistent Git repository of Markdown notes" —
with `MEMORY.md` holding general preferences plus an index of the rest. Every
session gets its own checkout and receives `MEMORY.md` as context.

**OpenHands.** Two tiers (`~/.openhands/memory/`, `<workspace>/.openhands/memory/`),
each with a `MEMORY.md` that is "the only file injected into the prompt," plus
daily logs `YYYY-MM-DD.md` that are "never injected automatically."

**Letta.** A git repository projected as a filesystem. Files at the memory root
are in the system prompt on every turn; subdirectories with their own `MEMORY.md`
"stay out of context until they are needed."

**Kimi API.** A memory store mounted at `/mnt/agents/memories/<store-name>/`. At
session start the platform supplies only limited content of the root `README.md`
and `index.md`, **at most 16 KiB each**; everything else must be read with file
tools.

| Product | Index file | Budget | Overflow policy |
|---|---|---|---|
| Claude Code | `MEMORY.md` | 200 lines or 25 KB | error on write; tail silently dropped on load |
| Qwen Code | `MEMORY.md` | 200 lines / 25,000 UTF-16 units | entries ≤150 units fit first; whole skipped, never truncated mid-line |
| Devin | `MEMORY.md` | not published | Dreaming reorganises |
| OpenHands | `MEMORY.md` | ~6,000 characters combined | whole oldest lines dropped per tier, with a notice |
| Letta | `MEMORY.md` | 5 MiB / 50 MiB repo quota (not context) | directory nesting instead of truncation |
| Kimi API | `README.md` + `index.md` | 16 KiB each | remainder requires explicit file reads |

**This is the finding.** Not "everyone uses Markdown" — that is a substrate
choice, and it is partly an accident of the current toolchain. The finding is
that **everyone converged on a two-level memory with a hard budget on the first
level and lazy loading of the second**, and everyone independently discovered
that the budget is necessary.

**Why it converged.** Consider what an agent actually needs at session start. It
has a fixed and expensive context budget, and it must decide what to know before
it knows what it will be asked. Every option trades off differently:

*Load everything.* Cost is linear in memory size, quality degrades with position
and distractor density, and the system stops working at some size. This is the
design everyone had first and abandoned.

*Retrieve by similarity.* Requires a query, and at session start there isn't
one. Worse, it makes behaviour depend on an embedding model that changes
underneath you, and it makes provenance and deletion much harder.

*Load a small index, fetch the rest on demand.* Cost is fixed and small. The
agent's own reasoning decides what to fetch — which uses the one component
guaranteed to see the actual task. The index is a **table of contents**, and a
table of contents is the cheapest possible interface between a large store and a
small working set.

The third option wins for a reason that generalises: **an index converts a
retrieval problem into a navigation problem, and navigation is something the
model is already good at.** No new component, no embedding model in the critical
path, no tuning.

**The hippocampus got there first.** A small fast structure holding pointers into
a large slow store, where a partial cue reinstates the whole
([01-human-memory.md](01-human-memory.md) §3). The convergence is not a
coincidence of fashion; it is the same solution to the same problem.

**The dissenting designs are instructive.** Gemini CLI uses three `GEMINI.md`
tiers with no index, and the whole hierarchy is concatenated and sent with every
prompt — which works only because the expected size is small. Cline uses six
fixed documents (`projectbrief.md`, `productContext.md`, `activeContext.md`,
`systemPatterns.md`, `techContext.md`, `progress.md`) with no index and no
runtime support at all: the model is instructed to "read ALL memory bank files
at the start of EVERY task." Both work, both are bounded by the same constraint,
and neither scales the way an index does.

---

## 3. The four questions, answered by shipped code

### 3.1 What is stored — the taxonomies

This is where products differ most, and where the field has made real progress
since 2023.

**Claude Code** uses four types, declared in YAML frontmatter: `user` (role,
expertise, working preferences), `feedback` (corrections and confirmed
approaches), `project` (ongoing work, deadlines, decisions not derivable from
code or git history), `reference` (where to find things outside the project).
Claude is instructed to skip anything derivable from the codebase or already in
`CLAUDE.md`.

**Qwen Code** uses the same four in substance — About you, Your feedback, Project
context, External references — plus an opt-in structured mode adding `category`,
`keywords`, and `usage scenarios` as frontmatter.

**GitHub Copilot** uses two, split by *ownership rather than content*: repository
facts (belong to one repo, managed in that repo's settings) and user preferences
(belong to the user, across all repositories, owned by the billing entity on
managed plans). The model decides which based on "who it applies to" and asks
when ambiguous.

**Alibaba Model Studio** uses three, split by *mutability*: factual memory
(events; auto-updating, expiring), user profile (fixed attributes from a custom
schema), and skill memory ("reusable task methods extracted from trajectories").

**Devin** has no enum but publishes an inclusion policy: saved are preferences,
corrections *with the rule to follow*, decisions and reasons, environment
gotchas. Not saved are session summaries, task state (PR numbers, statuses),
anything cheap to rediscover, and secrets.

**Doubao** uses four functional buckets — profile, preferences, interests, habits
— plus a short-term/long-term split.

**The pattern across these.** Four axes of distinction recur, and each is
answering a different question:

| Axis | Question it answers | Example |
|---|---|---|
| **Subject** | who is this about? | Claude Code `user` vs `project`; Copilot user prefs vs repo facts |
| **Provenance of the content** | did I learn this from being told or from being corrected? | Claude Code `user` vs `feedback` |
| **Mutability** | does this change, or is it fixed? | Alibaba profile (fixed) vs factual (expiring) |
| **Derivability** | could I just look it up? | Devin's "anything cheap to rediscover" exclusion |

The fourth is the most underrated. Every mature product has an explicit
**exclusion** policy — don't store what the code says, what git history says,
what the filesystem says. This is not tidiness; it is a correct application of
levels-of-processing in reverse: if re-deriving is cheap, storing it adds a
staleness risk with no retrieval benefit. Devin's docs say it outright, and
Copilot's expiry mechanism enforces it structurally.

### 3.2 What triggers a write

This is the question the research literature cares most about and products
answer most differently. Ranked by how much they write:

**Write everything (extracted).** Alibaba Model Studio extracts on every
conversation and trajectory input. Doubao passively picks up preferences that
"recur in conversation and look useful."

**Write in-loop as the agent works.** Claude Code, Qwen Code, Devin, OpenHands and
Letta all let the working agent edit memory during the session. Devin: "Devin
saves these on its own as you work."

**Write in a background pass after idleness.** Codex CLI waits until a chat "has
been idle long enough to avoid summarizing work that's still in progress" —
explicitly *not* at end-of-chat, because the end of a chat is not the end of the
work. Gemini CLI's Auto Memory requires a session **idle ≥ 3 hours and ≥ 10 user
messages** before it will extract.

**Write only when a human approves.** Gemini CLI drafts memory updates as
unified-diff `.patch` files and procedures as `SKILL.md`; it "cannot directly
edit active memory files," patches are target-allowlisted and applied atomically
only on approval. Antigravity's `/learn` is the same shape: "the agent never
edits configuration files immediately. It drafts a proposal artifact for your
review first."

**Write only at failure.** This is SOAR's impasse rule
([02-cognitive-architectures.md](02-cognitive-architectures.md) §3) and no
shipped product implements it. More on this in §7.

**The trigger table is really a table of who is accountable.** In-loop writes are
cheap and immediate but happen before the session's outcome is known — Devin's
docs are explicit that "new memories apply to future sessions, not the one that
wrote them," which is a way of admitting the write is unvalidated. Background
passes can see the outcome and consolidate, which is why every one of them
converged on the word *dreaming*. Approval gates are for when a wrong memory is
expensive.

### 3.3 Index and retrieval

The index is settled (§2). The retrieval path is not, and this is where the
products diverge along a real architectural fault line: **does the system give
the agent a search tool, or does it pre-inject?**

**Agent-driven navigation.** Claude Code, Devin, OpenHands, Letta, and Kimi API
inject only the index and give the agent normal file tools. Letta does **not**
build a vector index by default — the agent uses ordinary file search and read;
semantic search requires opting into indexing.

**Service-side retrieval.** Alibaba Model Studio does semantic search and injects
the top-k, exposing `top_k` and `min_score` (default 0.3 on the Pro plan).
Copilot relevance-filters server-side and, crucially, **checks citations against
the current branch before use** — a retrieved memory whose evidence no longer
exists is not served.

**Nothing.** Gemini CLI sends the whole `GEMINI.md` hierarchy with every prompt.
Cline relies on the model following an instruction to read six files.

Three design points worth extracting.

*Copilot's citation check is the most interesting retrieval mechanism in the
survey, and nobody else does it.* Every memory carries citations to code; at
retrieval time they are validated against the current branch. This is a
**truth-maintenance** step, not a relevance step, and it is the only shipped
implementation of the invalidation principle from
[01-human-memory.md](01-human-memory.md) §2 (Ryan & Frankland: forgetting is
loss of access, not loss of payload).

*OpenHands wraps injected memory in `<UNTRUSTED_CONTENT>`.* The whole block —
both `MEMORY.md` indexes — arrives with an explicit warning that memory may
contain prompt injection. This is the correct default and it is nearly unique.

*Kimi API's 16 KiB × 2 root files is the only published budget that is
explicitly a context budget rather than a file budget.* The store can hold 20 MiB
per file; only 32 KiB of index reaches the model.

### 3.4 Forgetting

Three mechanisms exist in shipped code, and only one of them is what the
literature calls forgetting.

**Expiry.** GitHub Copilot: **default 90 days**, reset on upvote but *not* on
retrieval, with a downvote causing deletion after disuse; organisations can set
7–180 days for repository facts. Alibaba Model Studio: factual rules carry
**180-day validity** by default. These are real, enforced, and — note — **reset
by validated quality rather than by use**, which is a deliberate departure from
the biological rule.

**Decay under capacity.** Doubao: 「存储容量有限，久远或低频的记忆可能会被弱化或遗忘」 —
bounded capacity, distant or low-frequency memories weaken or are forgotten. And
explicitly: 「记忆召回是概率性的，不是确定性的」 — recall is probabilistic, so the same
question can surface different memories.

**Consolidation passes.** Devin's Dreaming runs "about once a day when you've
been using Devin," merging overlapping notes, removing transient or stale notes
"that no session has used," adding missed lessons, and reorganising the index —
while preserving source links and explicit preferences. Qwen Code has `/dream`.
Gemini CLI's Auto Memory is the proposal-only variant. Letta's dreaming runs on a
**step-count or compaction event** rather than a schedule, and its second review
pass "does not ask you for approval." Copilot's Dreaming runs over eligible
sessions and PR review feedback to "update context, not the underlying AI model."
Kimi's 梦境 has activity triggers (`min_changed_sessions`) and/or a daily schedule
(`HH:mm` + timezone).

**What is absent everywhere.** No product implements retrieval-dependent decay
(the Bjork rule that reading should strengthen). No product implements the
distinction between "still stored" and "currently reachable" as separate fields
— the closest is Copilot's votes, which is a quality signal, not an accessibility
signal. And nothing implements **provable** deletion; see
[06-security.md](06-security.md).

**Soft delete is worse than it looks.** Doubao's memory switch, when turned off,
"stops new memories and stops use of existing ones, but does not delete them."
Devin: turning memory off "does not delete existing notes and stops dreaming."
Both are reasonable product decisions. Both mean the data is present and
recoverable after the user believes it is gone.

---

## 4. Eight mechanism families

Grouping the nineteen products by mechanism rather than by vendor produces eight
families, and the families cut across company size and geography.

| Family | Members | Signature |
|---|---|---|
| **Instruction-file hierarchy** | Aider, Amp, Zed, Cline (rules layer), Antigravity, Devin Local, Cursor (rules), Codex/ChatGPT (`AGENTS.md`) | Bounded set of Markdown files at fixed scopes, concatenated; only humans write; portability is the point |
| **Index + topic-file store** | Claude Code, Qwen Code, Devin, OpenHands, Letta, Kimi API, Codex CLI, Antigravity `/learn` (degenerate), Gemini CLI (index-less) | Small always-loaded index, detail on demand, explicit budget |
| **Git-backed memory filesystem** | Letta, Devin, Qwen Code (team tier), OpenHands (optional), Kimi API (versions, not git) | Commit is the durability boundary; merge replaces last-write-wins; memory is reviewable as a PR |
| **Background consolidation** | Devin, Qwen Code, Gemini CLI, Letta, Copilot, Kimi API | Scheduled or trigger-driven; split by autonomy (propose vs commit) |
| **Typed record stores** | Copilot, Alibaba Model Studio, Claude Code, Qwen Code, Kimi API, Claude apps | Memory as data with fields — type, provenance, votes, expiry — not prose |
| **Opaque cloud behavioural memory** | ChatGPT, Claude apps, Gemini app, Doubao, Kimi app, Cursor Grok Bot | No path, no format, no API; the product reserves the right to summarise, weaken or probabilistically recall |
| **Prompt-enforced pseudo-memory** | Cline (Memory Bank); historically Roo Code (unverifiable) | The model is instructed to read and rewrite files; correctness depends on instruction-following |
| **Session store as queryable artefact** | Copilot (SQLite + `/chronicle`), OpenHands (event stream + Condenser), Letta (`messages search`), Claude Code (retention), Cursor SDK (`journal.jsonl`) | The transcript is indexed and queried rather than compressed — an explicit alternative to extraction |

Two things to notice.

**Family 2 and Family 3 are converging.** Every git-backed implementation
(Letta, Devin, Qwen team tier) also uses an index. The git substrate is not an
alternative to the index architecture; it is a durability layer underneath it.

**Family 6 is a product category, not a technical one.** ChatGPT, Claude apps,
Gemini app, Doubao, Kimi app and Cursor's Grok Bot are all opaque for the same
reason: they are consumer services where the memory is a personalisation feature
and the substrate is an implementation detail. The technical neglect is not
accidental — a retrievable memory file is an interface commitment, and consumer
products avoid interface commitments.

---

## 5. The design differences that actually matter

Six differences separate the products that will age well from those that won't.

**1. Freeze-at-session-start vs live update.** Claude Code loads the index once
at start. Devin states the rule explicitly: "New memories apply to future
sessions, not the one that wrote them." Doubao admits the lag — 「记忆的更新需要一定的
加载时间」 — and that an old memory can still be recalled after a correction.
Letta and Gemini CLI (`/memory reload`) are the live-update outliers.

Freezing is defensible: a memory that changes mid-task makes behaviour
irreproducible. But it has a cost the products don't document — the agent cannot
learn from a correction within the session that produced it, so the user must
repeat themselves once before the system adapts.

**2. Who can write.** Agent self-editing (Claude Code, Qwen, Devin, OpenHands,
Letta) vs background extraction (Codex, Gemini Auto Memory, Copilot Dreaming) vs
proposal-only (Gemini, Antigravity). Only the proposal-only designs make the
human accountable, and only they are compatible with regulated environments.
Note Letta's second pass explicitly "does not ask you for approval" — autonomy is
a spectrum and vendors quietly move along it.

**3. Provenance.** Devin embeds `[source: …; added: …]` on every bullet. Copilot
stores citations and validates them. Claude Code stamps an ISO-8601 `modified`
field. OpenHands wraps the whole injected block as untrusted. Cursor warns that
automation memory can be poisoned by untrusted input. Doubao and Kimi have no
provenance at all.

Provenance is the single best predictor of whether a memory system is safe. A
memory without provenance cannot be verified, and cannot be invalidated when its
evidence disappears.

**4. Scope granularity.** The range is wide and the choice is consequential:

| Scope | Products | Consequence |
|---|---|---|
| machine-local | Claude Code, Codex CLI | memory does not follow the user |
| per worktree | Qwen Code | isolation is free; sharing is manual |
| per repo, git-shared | Qwen team tier, OpenHands (optional) | reviewable, but commits leak into the repo |
| per agent | Letta | memory is identity, not property |
| per account | ChatGPT, Claude apps, Gemini app, Kimi, Doubao | convenient; no project isolation |
| per automation / bot | Cursor | memory belongs to a job |
| repo **and** user, separately | Copilot | the only product with two simultaneous scopes and different owners |

Copilot's split is the most principled: a fact about the code belongs to the
code, a preference about the person belongs to the person, and the two have
different lifetimes, different permission models, and different billing
entities. Everyone else picks one and loses the other.

**5. Third-party readability.** Perfectly correlated with substrate, not with
vendor size. Every coding agent with a file substrate is transparent; every
consumer assistant is opaque. The interesting middle cases are Copilot
(server-side memory, file-side sessions) and Cursor (file-side journal,
cloud-side automation memory).

**6. Import/export.** Claude apps ship experimental memory import/export. Kimi
supports importing memory from other AI services. Devin publishes the format as
an open spec (Agent Memory Repo). Nobody else offers a portable memory artefact.

That last one matters more than it reads. A memory you cannot export is a memory
you cannot audit, cannot migrate, and cannot take to a competitor. Portability is
the precondition for the user owning their own memory — and it is currently a
differentiator rather than a baseline.

---

## 6. What they all get right

Three things, and they are not obvious.

**They all separate instructions from memory.** Every mature product states the
hierarchy, and every one of them treats memory as weaker than policy. This is a
genuine advance over 2023, when a "memory" was just text injected into the
prompt with no authority level.

**They all bounded the index, and all bounded it harshly.** 200 lines, 25 KB,
6,000 characters, 16 KiB. Nobody published a generous budget, because everyone
discovered empirically that a large index destroys quality. The field arrived at
Cowen's four-chunk limit by measurement rather than by citation, and it arrived
at roughly the same number.

**They all, or nearly all, made the memory inspectable by the human.** Even the
opaque products show a list with edit and delete. The plain-text products go
further, and it is clear this was a deliberate product decision rather than a
side effect of using Markdown: Devin explicitly says memory files are **read-only
in the app** so that changes must go through a session, because a memory edited
by hand would break the provenance chain.

---

## 7. What they all get wrong

Four failures, all traceable to the same root: **the field implemented the store
and skipped the compiler.**

**1. No episodic-to-procedural compilation.** A frequently retrieved memory stays
a memory forever. No product has a mechanism by which a repeatedly used episode
becomes a rule and stops being retrieved — ACT-R's production compilation and
SOAR's chunking
([02-cognitive-architectures.md](02-cognitive-architectures.md) §2–3). What exists
is the *inverse*: Antigravity's `/learn` and Gemini's Auto Memory turn a session
into a rule or a skill, but by explicit one-off user action, not by a policy
triggered on repetition.

The consequence is measurable. `Library Drift` (arXiv:2605.19576) finds that
unbounded accumulation degrades retrieval while benchmark averages stay flat,
and that LLM-authored skills give +0.0 pp where human-curated ones give
+16.2 pp. The absence of compilation is why skill libraries rot.

**2. The read path does not write.** No product updates a memory's score,
validity, or confidence when it is retrieved. Copilot comes closest — it votes on
memory quality, and a downvote shortens effective life — but the vote is cast by
a separate judgement, not by use. Bjork's retrieval-strength rule (reading
strengthens, and strengthens most when storage is high and retrieval is low) has
no implementation anywhere.

The cost is that a memory retrieved a thousand times is indistinguishable from
one retrieved once, so the system cannot tell which memories are load-bearing.
That is data every system already has and throws away.

**3. The index is a single point of failure.** Every architecture here depends on
`MEMORY.md` being a good table of contents. What happens when it isn't? Claude
Code errors on write when the index exceeds its budget. Qwen prefers short
entries. OpenHands drops whole oldest lines. These are all forms of **silent
degradation** — the memory is present, the index no longer points at it, and
nothing tells anyone. This is exactly the "unreachable but present" failure Ryan
& Frankland describe as the dominant biological mode, arrived at by accident
rather than design.

**4. No cross-agent memory, by policy.** A2A explicitly disclaims memory sharing.
MCP's 2026-07-28 revision went stateless, removing the session assumption a
memory server would need. Claude Code's subagents do not inherit the main
conversation's auto memory. The field has decided that agents do not share
memory, and it made that decision in a standards body rather than in a design
review. Whether it is right is genuinely open — but the consequence, that agent
teams cannot accumulate shared knowledge, is a limit on what multi-agent systems
can become.

**The root cause, stated once.** Every product in this survey treats memory as
*retention*. None treats it as *compilation* — the process by which what was
learned stops needing to be remembered. The biological systems in
[01-human-memory.md](01-human-memory.md) all have both. That is the gap.

---

## 8. Product notes

Condensed from the full survey; details and source URLs for each product are in
[sources](#sources).

**Claude Code** — typed Markdown notes in a repo-scoped directory, `MEMORY.md`
index capped at 200 lines / 25 KB, machine-local, subagents excluded. The cleanest
instance of the index architecture.

**Claude apps** — account-level topic memory plus per-project memory, with
project isolation and an explicit **sensitive-topic** class (health, race,
ethnicity, religion, politics, gender identity) excluded by default and saved
only on opt-in. Some data is never saved even on request.

**Codex CLI** — off by default. `~/.codex/memories/` holds summaries, durable
entries, recent inputs and evidence; generated in a background pass after
idleness. `AGENTS.md` chain truncated at 32 KiB.

**ChatGPT** — account-level memories positioned explicitly below instructions;
per-project instructions and Sources; per-dot notes that are separate from saved
memory and survive its settings changing. Computer History (macOS, opt-in) mines
accessibility text and interaction events into memories, without screenshots or
audio.

**Cursor** — "memory" means three unrelated things: an append-only per-turn
`journal.jsonl` for hosted agents (2,000-character truncation per message), a
per-automation `MEMORIES.md` outside the working filesystem, and an opaque
per-bot profile. The IDE itself has rules, not memory.

**GitHub Copilot** — two server-side stores with different owners, citations
validated against the branch, a voting system, 90-day default expiry reset by
upvote, and a hard rule that only the root agent may write while subagents may
only read. The most complete lifecycle model in the survey.

**Windsurf → Devin Local** — the clearest case of a withdrawn feature. Legacy
Cascade Memories survive only under a `/windsurf/plugins/cascade/` path; the
successor's documentation has rules, skills, plugins, MCP and hooks, and no
memory.

**Gemini CLI** — three `GEMINI.md` tiers sent with every prompt, plus a JIT
discovery mechanism when a tool touches a directory. Auto Memory is the most
carefully gated background pass in the survey: idle ≥ 3 hours, ≥ 10 user
messages, lock file to serialise extraction across CLI instances, state file to
track processed sessions, patches applied atomically on approval only, secrets
redacted.

**Gemini app** — opt-in, US-only, requires an AI plan and a personal account.
Two buckets: memory items and instructions. Google warns that asking it to forget
a topic "doesn't always work perfectly" and advises deleting the underlying
chats.

**Antigravity** — no memory at all. The `/learn` command opens with "Agents don't
remember conversations" and converts session lessons into rules or skills, with
human approval and a preference for updating an existing artefact over creating a
duplicate.

**Cline** — Memory Bank is a "documentation methodology," not a storage engine:
six Markdown files in the repo, driven by instructions pasted into
`.clinerules`. It exists to survive context overflow, and correctness depends
entirely on the model following the instruction to read all six.

**Aider** — no memory. A user-supplied conventions file added with `/read` so it
is read-only and cacheable, and a per-repo transcript that is never read back.

**OpenHands** — two tiers, agent-curated, injected as explicitly untrusted text
under a ~6,000-character budget, with whole-line truncation and a notice. The
best security posture of any open implementation.

**Devin** — git-backed memory drive with per-entry provenance, daily Dreaming,
personal-per-organisation scope, and an open spec (Agent Memory Repo) that is the
only attempt to standardise the format across vendors.

**Amp** — no memory; a five-level `AGENTS.md` loader (cwd-to-`$HOME` parents,
subtree-on-read, personal, system, workspace) with glob-scoped fragments and
fallbacks to `AGENT.md`/`CLAUDE.md`.

**Zed** — no memory; a single always-on instruction file (personal plus first
match among eight tool filenames) with on-demand behaviour moved to Skills.

**Letta** — an agent-owned git repository projected as a filesystem, where path
position decides whether something is always in context or fetched on demand. No
vector index by default. Subagents use git worktrees so dreaming can update
memory concurrently without blocking the main agent.

**Doubao** — default-on, opaque, capacity-bounded, decay-prone extraction from
chat text only (not images, files or links), with probabilistic recall and an
explicit admission of update lag.

**Kimi app** — account-synced memory space of file-organised facts plus past
conversations, cited at answer time, with import/export. New experience rolling
out from October 2026.

**Kimi API** — a versioned, mountable, project-scoped file store; ≤ 8 stores per
session, binding frozen at session creation, 16 KiB × 2 index injection, 409 on
concurrent write conflict, and Dreaming that rewrites the same store in place
with no automatic rollback.

**Qwen Code** — four content types, a 200-line / 25,000-unit index, a protected
`pinned/` area, a git-shared team tier (off by default), `/dream`, and an opt-in
hierarchical "structured recall" mode that injects only the relevant subtree.

**Alibaba Model Studio** — a billable service with the most explicit type system
(factual / profile / skill), per-rule expiry, 50-rule caps, `top_k`/`min_score`
retrieval, before/after audit snapshots, and four integration channels (REST,
MCP, CLI, Skill).

**Roo Code** — dropped. No official memory documentation resolves at any URL as
of 2026-11-01; the domain redirects to Roomote and both the docs SPA and the
GitHub raw paths 404. Not reconstructed from third-party write-ups.

---

## 9. What this survey implies

Four things, in order of how much they should change what you build.

**1. The index is the design.** If you are building agent memory, the artefact
that matters is the table of contents, not the store. Its size, its format, its
update policy, and what happens when it overflows are the four decisions that
determine whether the system works. Everything else is storage.

**2. Budget in characters, and fail loudly.** Every product that published a
budget made it small. Every product that overflows silently loses memory. Claude
Code's error-on-write is the right instinct — an index that doesn't fit is a
design failure the writer should know about, not a load-time surprise.

**3. Provenance is not optional, it is the safety mechanism.** A memory with a
source can be checked, invalidated, and audited. A memory without one is an
unverifiable claim from a manipulable process. Devin's one-line format —
`[source: …; added: …]` — is the cheapest correct implementation in the field and
should be copied.

**4. Build the compiler.** This is the one thing nobody has done and everybody
needs. The missing mechanism is not better retrieval or bigger context; it is a
policy by which a memory that keeps being used becomes a rule, a skill, or a code
path, and then stops being retrieved. Without it, memory grows monotonically and
degrades — which is the failure state that most of the products in this survey
are currently in, and that their own dreaming passes are an attempt to patch.

---

## Sources

All retrieved 2026-11-01.

**Anthropic** — [Claude Code memory](https://code.claude.com/docs/en/memory) ·
[chat search and memory](https://support.claude.com/en/articles/11817273-use-claude-s-chat-search-and-memory-to-build-on-previous-context) ·
[Projects](https://support.claude.com/en/articles/9517075-what-are-projects)

**OpenAI** — [memories](https://learn.chatgpt.com/docs/customization/memories.md) ·
[personalization](https://learn.chatgpt.com/docs/customization/personalize.md) ·
[projects](https://learn.chatgpt.com/docs/customization/projects.md) ·
[dot tasks and memory](https://learn.chatgpt.com/docs/dots/tasks-and-memory.md) ·
[AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md.md)

**Cursor** — [rules](https://cursor.com/docs/rules.md) ·
[automations](https://cursor.com/docs/automations.md) ·
[agent SDK memory](https://cursor.com/docs/agent-sdk/reference/memory.md) ·
[Grok Bot memory](https://cursor.com/help/grok-bot/memory.md)

**GitHub** — [Copilot memory](https://docs.github.com/en/copilot/concepts/prompting-and-context/copilot-memory) ·
[dreaming](https://docs.github.com/en/copilot/concepts/prompting-and-context/copilot-dreaming) ·
[session data](https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/session-data)

**Cognition** — [Devin memory and dreaming](https://docs.devin.ai/product-guides/memory) ·
[rules](https://docs.devin.ai/product-guides/rules) ·
[knowledge (deprecated)](https://docs.devin.ai/product-guides/knowledge) ·
[Devin Local customizations](https://docs.devin.ai/desktop/devin-local/customizations.md) ·
[legacy Cascade memories](https://docs.devin.ai/windsurf/plugins/cascade/memories.md) ·
[Agent Memory Repo spec](https://github.com/AgentMemoryRepo/agentmemoryrepo)

**Google** — [GEMINI.md](https://geminicli.com/docs/cli/gemini-md/) ·
[memory files](https://geminicli.com/docs/cli/memory/) ·
[memory management tutorial](https://geminicli.com/docs/cli/tutorials/memory-management/) ·
[auto memory](https://geminicli.com/docs/cli/auto-memory/) ·
[Gemini app personal context](https://support.google.com/gemini/answer/16598625?hl=en&p=saved_info) ·
[Antigravity rules](https://antigravity.google/docs/rules) ·
[Antigravity /learn](https://antigravity.google/docs/commands/learn) ·
[Antigravity projects](https://antigravity.google/docs/projects)

**Others** — [Cline Memory Bank](https://docs.cline.bot/prompting/memory-bank) ·
[Aider conventions](https://aider.chat/docs/usage/conventions.html) ·
[Aider options](https://aider.chat/docs/config/options.html) ·
[OpenHands persistent memory](https://docs.openhands.dev/sdk/guides/persistent-memory) ·
[OpenHands Condenser](https://docs.openhands.dev/sdk/architecture/condenser) ·
[Amp AGENTS.md](https://ampcode.com/docs/markdown/customize/agents-md) ·
[Zed instructions](https://raw.githubusercontent.com/zed-industries/zed/main/docs/src/ai/instructions.md) ·
[Zed rules](https://raw.githubusercontent.com/zed-industries/zed/main/docs/src/ai/rules.md) ·
[Letta memory](https://docs.letta.com/agent-sdk/memory/index.md) ·
[Letta MemFS](https://docs.letta.com/concepts/memfs/index.md) ·
[Letta memory configuration](https://docs.letta.com/configuration/memory/index.md)

**Chinese products** — [Doubao memory](https://lf9-cdn-tos.draftstatic.com/obj/ies-hotsoon-draft/grace_legal/doubao_member_copywrite_for_ai.html) ·
[Kimi memory (app)](https://www.kimi.com/help/kimi/memory) ·
[Kimi memory store (API)](https://platform.kimi.com/docs/hosted-agents/memory.md) ·
[Kimi dreams](https://platform.kimi.com/docs/hosted-agents/dreams.md) ·
[Qwen Code memory](https://qwenlm.github.io/qwen-code-docs/en/users/features/memory/) ·
[Alibaba Model Studio memory](https://help.aliyun.com/en/model-studio/memory-library)

---

*See also: [04-research-architectures.md](04-research-architectures.md) for the
research systems these products are partly descended from,
[05-infrastructure.md](05-infrastructure.md) for the storage layer underneath,
and [06-security.md](06-security.md) for why the trust hierarchy in §1 is load-bearing.*
