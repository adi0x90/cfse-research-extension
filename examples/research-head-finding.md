---
id: FD-DEMO-RESEARCH-RT-LR-SWEEP-HEAD
title: "Research HEAD: RT-LR-SWEEP"
severity: info
description: "Rolling snapshot — learning rate sweep track"
status: ACTIVE
version: 0.1.0
extensions:
  research:
    workspace: demo-autoresearch
    project_id: DEMO
    track_id: RT-LR-SWEEP
    state: S2_INSTRUMENTED
    event:
      kind: evidence
      date: "2026-03-12"
      sequence: 1
      primary_direction: B_PROVE
    sources:
      paths:
        - results/run-001/metrics.json
      cmd: ""
      notes: ""
    links: []
    blockers: []
    next_actions:
      - "Run lr=0.0005 to test if lower rate improves val_bpb"
      - "Run lr=0.003 to test if higher rate diverges"
    confidence: "medium — single baseline run"
---

## Current State

Track has 1 event (baseline measurement). No refutation attempts yet.

## Summary

| Event | Kind | Direction | Date | Key Result |
|-------|------|-----------|------|------------|
| E0001 | evidence | B_PROVE | 2026-03-12 | val_bpb=0.9702 baseline |
