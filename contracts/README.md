# Contracts (cfse-research)

These contracts enable fast querying of `extensions.research.*` fields via ASIQL.

## Recommended contract

Use the combined contract:

- `cfse_spec_math_research.yaml`

If you also need Lean formal verification fields queryable without composing a second overlay contract, use:

- `cfse_spec_math_research_lean.yaml`

Rationale: ASIQL currently uses the same `strict` flag for both ingestion strictness and contract merge strictness. In strict mode, differing type specs across multiple contracts are rejected, so a single combined contract is the simplest robust setup.

## Suggested queryable workflow fields

If you want methodology-grade tracking (lifecycle + branches + promotion governance), ensure your findings consistently populate:

- `extensions.research.track_id`
- `extensions.research.state`
- `extensions.research.event.kind`
- `extensions.research.event.date`
- `extensions.research.event.sequence` (recommended for append-only timelines)
- `extensions.research.event.primary_direction` (recommended: `B_PROVE|B_REFUTE|B_RELAX|B_TRANSFER`)
- `extensions.research.links[]` (recommended: governance labels like `label:autonomy:*`, etc.)

## Quick queries

```text
# All events on a track
Finding[extensions.research.track_id:"RT-HYPOTHESIS-ONE"] | limit 10

# All refutation attempts
Finding[extensions.research.event.primary_direction:"B_REFUTE"] | limit 20

# All branch events
Finding[extensions.research.event.kind:"branch"] | limit 20

# Tracks at S5 (ready for promotion)
Finding[extensions.research.state:"S5_PROMOTABLE"] | limit 10
```
