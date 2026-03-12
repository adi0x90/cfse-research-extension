---
id: FD-DEMO-RESEARCH-RT-LR-SWEEP-E0003
title: "Research: RT-LR-SWEEP — evidence (refutation attempt)"
severity: info
description: "Higher learning rate diverges — refutes monotonic improvement hypothesis"
status: ACTIVE
version: 0.1.0
extensions:
  research:
    workspace: demo-autoresearch
    project_id: DEMO
    track_id: RT-LR-SWEEP
    state: S3_CONTESTED
    event:
      kind: evidence
      date: "2026-03-12"
      sequence: 3
      primary_direction: B_REFUTE
    sources:
      paths:
        - results/run-003/metrics.json
      cmd: "python train.py --lr 0.003 --epochs 50"
      notes: "val_bpb=1.0214, diverged at epoch 22"
    links:
      - "refutes:FD-DEMO-RESEARCH-RT-LR-SWEEP-E0001"
    blockers: []
    next_actions:
      - "Try lr=0.002 to find the transition point"
    confidence: "high — clear divergence signal"
---

## Observations

lr=0.003 causes divergence at epoch 22. val_bpb worse than baseline by 0.05.

This refutes the implicit hypothesis from E0001 that increasing lr would monotonically improve results. The optimal lr is bounded above by ~0.003.

## Evidence

- val_bpb: 1.0214 (worse than baseline 0.9702)
- Loss curve: stable until epoch 22, then divergent oscillation
- Gradient norm spike at epoch 22: 14.3x baseline
