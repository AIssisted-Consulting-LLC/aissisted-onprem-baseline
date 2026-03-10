# Rollback Playbook

Rollback is a feature.
If you cannot revert cleanly, you are still testing in production.

## Before any risky change

- record current version
- snapshot config
- snapshot prompts and templates
- snapshot corpus metadata or source manifest
- confirm backup path exists
- define stop condition for rollback

## Rollback triggers

Roll back when any of these happen and quick triage does not clear them fast:

- repeated auth failures after a config change
- tool behavior widens unexpectedly
- retrieval quality drops sharply
- model routing starts failing over incorrectly
- operator loses remote access
- latency or cost spikes beyond agreed threshold

## Fast rollback order

1. Stop new risky change rollout.
2. Revert config to last known good.
3. Revert prompt or tool policy changes.
4. Restore prior service version if needed.
5. Reconnect using the known-good remote access path.
6. Validate one happy-path request.
7. Validate one denied-path request.
8. Check logs for leftover failures.

## What to snapshot

- runtime config
- environment variable source or secret references
- prompt templates
- allowlists and approval rules
- corpus manifest
- index version metadata
- deployment version or container image tag

## What not to trust during rollback

- memory
- tribal knowledge
- old screenshots
- half-written wiki pages

## If rollback does not restore service

Check:

- hidden environment variable drift
- expired provider credential
- network ACL or firewall changes
- DNS or tunnel changes
- index corruption that survived config rollback
- client-side auth caching

## After rollback

- mark the failed version clearly
- save the error signatures
- write the actual root cause
- update the checklist so the same mistake is harder next time
