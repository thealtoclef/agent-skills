---
name: commit-guideline
description: Provides the What-Why-How commit message standard with Conventional Commits subject format. Use when asked to commit changes, write a commit message, prepare a commit, or describe changes for version control.
---

# Commit Guideline

## Subject

Format: `<type>(<scope>): <imperative summary>`

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `perf`

Include a mechanism hint when it aids `git log --oneline` scanning:
- Good: `refactor(connector/spanner-cdc): cross-partition ordering via ReorderBuffer + BFS scheduling`
- Bad: `refactor: rewrite spanner cdc reader`

Subject alone suffices for typo fixes, dependency bumps, one-line config changes, or trivial formatting.

## Body

For non-trivial commits, write freeform prose. The diff shows the code; the
body supplies **why**. Every body must have **three paragraphs** corresponding to
the What-Why-How framework:

1. **What** — the observable problem or gap. What was broken, missing, or
   needed. Symptoms the user/operator saw. Multiple distinct issues → number
   them. Start this paragraph by naming the thing: the component, the behavior,
   the symptom. E.g. "The Spanner CDC source had a fundamental ordering bug:"
   or "Postgres array columns appeared as raw text strings".

2. **Why** — the root cause. The code path, design gap, or architectural
   limitation that produced the What. This is the load-bearing paragraph.
   Someone reading `git log` must verify the fix addressed the actual cause,
   not just the symptom. Start by naming the cause: the missing abstraction,
   the wrong assumption, the upstream limitation. E.g. "No reorder buffer
   existed between concurrent partition tasks and the downstream channel." or
   "Upstream Debezium's resolveValue() loses array type context when recursing".

3. **How** — the action taken. What was added, changed, removed, or replaced.
   Not limited to fixes — for a `feat` it's what was built, for a `refactor`
   it's what was replaced, for a `fix` it's what was changed. Name the key
   mechanism: the new component, the changed algorithm, the removed indirection.
   E.g. "ReorderBuffer (reorder_buffer.rs), modeled after PostgreSQL's..." or
   "Added parsePostgresTextArray() to parse Postgres text array syntax..." or
   "Switched to gRPC streaming. The caller opens a persistent stream...".

Each paragraph opens with a **lead-in phrase** that signals what it covers — not
a literal `What:`/`Why:`/`How:` label, but a natural sentence that anchors the
reader. The reader should instantly know "this paragraph is the problem", "this
is the cause", "this is what was done" from the first words.

Bullet points are fine for cleanup/removal items at the end of the How paragraph.

## Anti-patterns

- **Labeled sections** (`What:`, `Why:`, `How:`) — ugly in `git log`. Use natural lead-in phrases.
- **Restating the subject** as the What paragraph. Subject already says what.
- **Skipping Why.** Why is the load-bearing paragraph — without it the fix is unmoored.
- **Changelog listing** every file/method. The diff already shows this.
- **Forcing body on trivial commits.** Typo, dep bump, one-liner — subject alone.

## Examples

### Bug fix

```
fix(cdc): convert Postgres text array values to proper list types

Postgres array columns (e.g. _float4, _int4) appeared as raw text strings
like "{1.5,2.3,NULL}" instead of typed lists in CDC output. TOAST array
columns triggered a DataException from Kafka Connect's Struct.put()
because a String leaked into an ARRAY-typed schema slot.

Upstream Debezium's resolveValue() loses array type context when recursing
from _float4→float4, leaving the raw text unconverted. Separately,
UnchangedToastedPlaceholder maps UNCHANGED_TOAST_VALUE to a String, which
handleUnknownData() passes through to array schemas unresolvable.

Added parsePostgresTextArray() to parse Postgres text array syntax directly
into List<Object> with typed elements. Non-TOAST arrays bypass
resolveValue() and use the parser directly. In the TOAST path, null is
returned for array columns so convertArray() falls back to
Collections.emptyList().
```

### Feature

```
feat(iceberg): add snapshot_expiration_retain_max to cap snapshot count

Snapshot expiration previously only considered age, so tables with frequent
commits could accumulate unbounded snapshots regardless of the
snapshot_expiration_retain_secs window.

The retain_secs threshold is a time-based gate only — it has no awareness
of snapshot density. A high-frequency writer produces thousands of snapshots
within any retention window, all of which survive expiration.

Added snapshot_expiration_retain_max config enforcing a hard ceiling on
retained snapshots. When current count exceeds the limit, oldest snapshots
are expired even if they haven't reached the age threshold. Expiration
respects live branches and tags, and integrates into the existing
background GC loop.
```

### Refactor

```
refactor(connector/spanner-cdc): cross-partition ordering via ReorderBuffer

The Spanner CDC source had a fundamental ordering bug: concurrent
partition readers emitted records to a shared mpsc channel with no
cross-partition commit-timestamp coordination. A slow partition's
old-schema record could arrive after a fast partition had advanced the
schema tracker, flipping it back across a DDL boundary.

No reorder buffer existed between concurrent partition tasks and the
downstream channel. The old per-table watermark in SchemaTracker was a
symptom patch, not a root-cause fix — it masked ordering violations
per-table but couldn't prevent cross-partition regression.

ReorderBuffer (reorder_buffer.rs), modeled after PostgreSQL's logical
replication ReorderBuffer: BTreeMap keyed by commit_ts, drained in order
when the global watermark advances. Sibling group gating prevents
incomplete partition groups from polluting the watermark. SchemaTracker
deleted entirely — schema tracking inlined into reorder_buffer.rs as a
small HashMap with no RwLock, correctness guaranteed by BTreeMap drain
ordering.
```
