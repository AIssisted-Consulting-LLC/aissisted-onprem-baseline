# Handoff Policy

This document defines who can operate the system after build-out.

## Roles

### Builder

Can:

- change architecture
- enable new tools
- change model routing
- change network exposure
- rotate core secrets
- approve production rollouts

### Operator

Can:

- restart services
- inspect logs
- approve routine safe actions if policy allows it
- rotate non-core credentials with written procedure
- run documented rollback steps

Cannot:

- widen network exposure without approval
- enable dangerous tools ad hoc
- paste secrets into prompts or tickets
- bypass change logging

### Reviewer

Can:

- inspect logs and outputs
- run QA flows
- confirm rollback worked
- sign off on data quality and policy behavior

## Change classes

### Low risk

- copy changes
- log retention tweaks
- alert threshold tuning
- adding docs to approved corpus

### Medium risk

- model route changes
- prompt changes that affect tool use
- adding a new data source
- index parameter changes

### High risk

- enabling shell or browser automation
- exposing new network surfaces
- changing auth mode
- changing secrets backend
- changing filesystem scope

High-risk changes need:

- named approver
- rollback step written first
- backup or snapshot confirmed
- post-change verification

## Required records

Every production change should record:

- date and time
- operator name
- what changed
- why it changed
- rollback point
- verification result

## Handoff minimum

Do not hand off an on-prem agent system until the new operator can do these without guessing:

- find the logs
- confirm the process is running
- confirm the model/provider path is healthy
- identify whether failure is auth, network, model, tool, or data
- restart safely
- roll back safely
