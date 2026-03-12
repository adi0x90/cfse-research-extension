---
id: FD-DEMO-RESEARCH-RT-LR-SWEEP-E0001
title: "Research: RT-LR-SWEEP — evidence (baseline)"
severity: info
description: "Baseline measurement for learning rate sweep on Shakespeare character-level model"
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
      cmd: "python train.py --lr 0.001 --epochs 50"
      notes: "val_bpb=0.9702, train_bpb=0.9531, 50 epochs, 4min on M2"
    links: []
    blockers: []
    next_actions:
      - "Run lr=0.0005 to test if lower rate improves val_bpb"
      - "Run lr=0.003 to test if higher rate diverges"
    confidence: "medium — single run, needs replication"
---

## Observations

Baseline run with lr=0.001 on Shakespeare character-level model.

- val_bpb: 0.9702
- train_bpb: 0.9531
- No overfitting signal (train-val gap < 0.02)
- Loss curve plateaued at epoch ~40

## Next

Two branches: lower lr (0.0005) and higher lr (0.003) to map the landscape.
