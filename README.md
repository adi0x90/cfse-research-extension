# cfse-research

A **research provenance and progress tracking** extension for [CFSE](https://github.com/adi0x90/cfse) (Concept Flow Scenarios Explorations).

Track what you tried, what worked, what failed, and why — across autonomous research sessions, LLM agents, or manual investigation. All state lives as git-tracked markdown files, not in API sessions or cloud context.

## The Problem

Autonomous research tools (LLM agents, autoresearch loops, multi-session investigations) generate results but lose *methodology*:

- Which hypotheses were tested?
- Which were refuted, and by what evidence?
- What's blocked, and what's next?
- Where exactly was I when the session ended?

When the API goes down, the OAuth token expires, or you switch models — the research state disappears with the session.

## The Fix

Separate research state from compute. Every hypothesis, evidence event, prove/refute direction, and blocker lives as an append-only markdown file on disk. The LLM is a stateless worker: it reads state, does work, and writes events back. Any new session cold-starts from the exact sequence number.

**cfse-research** provides the metadata schema for this pattern, extending CFSE Findings with:

- **Provenance** — project, workspace, track ID
- **Lifecycle** — 8-state machine from `S0_SEEDED` to `S7_RETIRED`
- **Research events** — typed snapshots (`hypothesis`, `evidence`, `insight`, `branch`, `gap`)
- **Prove/refute discipline** — direction labels (`B_PROVE`, `B_REFUTE`, `B_RELAX`, `B_TRANSFER`, `B_CONSTRUCT`)
- **Traceability** — structured links (`supports:`, `refutes:`, `supersedes:`, `depends_on:`)
- **Gap tracking** — explicit blockers and next actions per track

## Requirements

- [CFSE core spec](https://github.com/adi0x90/cfse) (for artifact type definitions: Finding, Invariant, etc.)

## Quick Start

### 1. Define a track

Create a track registry in your project (e.g., `.cfse/research/tracks.yaml`):

```yaml
project_id: MY-PROJECT
workspace: my-research
tracks:
  - id: RT-HYPOTHESIS-ONE
    priority: HIGH
    description: "Testing whether X causes Y under condition Z"
```

### 2. Create a research event

Each event is a CFSE Finding with `extensions.research.*` metadata:

```yaml
---
id: FD-MY-RESEARCH-RT-HYPOTHESIS-ONE-E0001
title: "Research: RT-HYPOTHESIS-ONE — evidence"
severity: info
status: ACTIVE
version: 0.1.0
extensions:
  research:
    workspace: my-research
    project_id: MY-PROJECT
    track_id: RT-HYPOTHESIS-ONE
    state: S2_INSTRUMENTED
    event:
      kind: evidence
      date: "2026-03-12"
      sequence: 1
      primary_direction: B_PROVE
    sources:
      paths:
        - experiments/run-001/results.json
      cmd: "python train.py --lr 0.001"
      notes: "val_bpb improved 0.9702 -> 0.9697"
    links: []
    blockers: []
    next_actions:
      - "Test with lr=0.0005 to check if improvement continues"
    confidence: "medium — single run, needs replication"
---

## Observations

Learning rate 0.001 produced measurable improvement...
```

### 3. Maintain a HEAD finding per track

A rolling snapshot (e.g., `FD-MY-RESEARCH-RT-HYPOTHESIS-ONE-HEAD.md`) that mirrors the latest event's state, blockers, and next actions. This is what a new session reads to cold-start.

### 4. Query your research state

With grep:
```bash
grep -r "track_id: RT-HYPOTHESIS-ONE" .cfse/findings/ --include="*.md" -l
grep -r "primary_direction: B_REFUTE" .cfse/findings/ --include="*.md" -l
```

With ASIQL (if available):
```
Finding[extensions.research.track_id:"RT-HYPOTHESIS-ONE"] | limit 10
Finding[extensions.research.event.primary_direction:"B_REFUTE"] | limit 20
```

## Lifecycle States

```
S0_SEEDED                    — Lead identified
S1_FORMALIZED                — Hypothesis precisely written
S2_INSTRUMENTED              — Falsifier battery / verifier scripts exist
S3_CONTESTED                 — Active dispute; no proof yet
S4_CONDITIONALLY_SUPPORTED   — Strong evidence, missing a lemma
S5_PROMOTABLE                — Ready for review
S6_PROMOTED                  — Approved, externally communicable
S7_RETIRED                   — Disproven or superseded
```

## Direction Labels

Every research event carries a `primary_direction`:

| Direction | Meaning |
|---|---|
| `B_PROVE` | Attempt to confirm the hypothesis |
| `B_REFUTE` | Attempt to break / falsify |
| `B_RELAX` | Loosen constraints to find a weaker true statement |
| `B_TRANSFER` | Port the pattern to a different domain |
| `B_CONSTRUCT` | Build new hypothesis from scratch |

The prove/refute discipline ensures tracks don't silently accumulate untested assumptions. Before promoting to `S5+`, log at least one `B_REFUTE` attempt.

## Link Vocabulary

Structured links in `extensions.research.links[]`:

- `supports:<ID>` — this event supports a finding/invariant
- `refutes:<ID>` — this event refutes a finding/invariant
- `supersedes:<ID>` — replaces an earlier result
- `narrows:<ID>` — reduces scope of earlier event
- `depends_on:<ID>` — prerequisite dependency

## Contracts

ASIQL query contracts are provided in `contracts/`:

| Contract | Use case |
|---|---|
| `cfse_spec_math_research.yaml` | **Recommended.** Full CFSE types + math + research searchable paths |
| `cfse_spec_math_research_lean.yaml` | Same + Lean formal verification bindings |

See [`contracts/README.md`](contracts/README.md) for details.

## Design Principles

- **No collisions** with `extensions.math.*` — math truth status stays at `extensions.math.status`, research workflow state at `extensions.research.state`
- **Append-only events** — never mutate a finding, always create a new event linking to previous ones
- **Schema-light** — 7 lines of YAML per event, not 50. Conventions over enforcement.
- **Local-first** — all state is files on disk, git-tracked. Survives API outages, model switches, session loss.
- **Queryable** — grep works. ASIQL works. Both give you "where are we" in seconds.

## Extension Metadata

```yaml
name: research
extension_version: 0.1.0
cfse_spec_version: 1.0.0
status: draft
namespace: extensions.research.*
primary_artifact: Finding
```

## License

Same license as [CFSE](https://github.com/adi0x90/cfse).
