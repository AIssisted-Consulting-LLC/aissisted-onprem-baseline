# Deployment Checklist

Use this before any production rollout.

## Scope and ownership

- [ ] One named system owner exists
- [ ] One named backup owner exists
- [ ] Trust boundary is defined
- [ ] Target users are defined
- [ ] Supported channels and tools are listed
- [ ] Data sources are listed
- [ ] Secrets owner is listed

## Host and network

- [ ] Host is dedicated or risk-accepted
- [ ] OS patch level is current enough for production use
- [ ] Disk free space alert exists
- [ ] Time sync is working
- [ ] Gateway binds to loopback by default
- [ ] Remote access path is documented
- [ ] Firewall policy is documented
- [ ] No public unauthenticated control surface exists

## Identity and secrets

- [ ] Provider keys are stored outside the repo
- [ ] Tokens are scoped to least privilege
- [ ] Rotation owner and schedule are defined
- [ ] Break-glass credential path is documented
- [ ] Test credentials are not reused in production

## Agent policy

- [ ] Default tool policy is deny-heavy
- [ ] Dangerous tools require approval
- [ ] Filesystem scope is narrow
- [ ] Network egress rules are intentional
- [ ] Shared-user scenarios are explicitly reviewed
- [ ] Prompt templates are versioned

## RAG and data

- [ ] Document source list is approved
- [ ] Stale document handling is defined
- [ ] Rebuild procedure is documented
- [ ] Metadata schema exists
- [ ] OCR quality has been spot-checked
- [ ] Duplicate detection exists or is manually reviewed

## Logging and monitoring

- [ ] Structured logs enabled
- [ ] Correlation ID available per request
- [ ] Tool execution logs enabled
- [ ] Prompt and tool-output logging scope is defined (what is stored vs redacted)
- [ ] Auth failure logs retained
- [ ] Alerts exist for process down, disk pressure, and provider failure spikes
- [ ] Log retention rule is defined
- [ ] PII handling is defined (redaction, retention, access)

## Backups and rollback

- [ ] Config backup exists
- [ ] Prompt/template backup exists
- [ ] Corpus backup exists
- [ ] Index rebuild path is tested
- [ ] Last known good version is recorded
- [ ] Rollback owner is named

## Go-live tests

- [ ] Happy path tested
- [ ] Denied tool path tested
- [ ] Bad credentials path tested
- [ ] Provider outage path tested
- [ ] Remote access recovery tested
- [ ] Restart and reboot behavior tested
- [ ] Restore from backup tested enough to trust

## Post go-live: first hour checks

Do these right after you turn it on for real users.

- [ ] Confirm logs show real traffic (not just health checks)
- [ ] Confirm at least one request has a correlation ID end-to-end
- [ ] Confirm one denial is logged (prove policy is enforced)
- [ ] Confirm queue depth and p95 latency are within your expected range
- [ ] Confirm disk free space and log growth rate look normal
- [ ] Confirm the rollback artifact exists (config snapshot + last-known-good version)

## Final stop/go question

If this breaks at 7:10 AM tomorrow, who gets paged, what do they check first, and how do they roll back in under 30 minutes?

If you cannot answer that, do not call it ready.
