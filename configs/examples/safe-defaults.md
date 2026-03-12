# Safe Defaults

A conservative starting point for an on-prem agent deployment.

Use this as a review checklist, not as cargo-cult config.

## Network

- Bind the gateway to `127.0.0.1` by default
- Put remote access behind SSH or a tailnet
- Do not expose raw control-plane ports publicly unless you have a documented reason
- Keep inbound paths simple; avoid stacking multiple reverse proxies on day one

## Identity and access

- One trust boundary per gateway
- Enable authentication on any non-loopback surface
- Limit who can change prompts, tools, model routes, and channel bindings
- Require human approval for destructive actions
- Prefer named operator accounts over shared admin credentials

## Tools

Start with the smallest useful set.

Default posture:

- Shell / exec: off
- Browser automation: off
- Device control: off
- File write outside workspace: off
- Networked tools: off unless clearly needed
- Dangerous actions: approval required

Then re-enable tools one at a time with clear ownership.

## Workspace and data boundaries

- Keep a narrow workspace root
- Separate prompts, documents, logs, secrets, and backups
- Do not mix client datasets in the same retrieval store without explicit isolation rules
- Treat uploads and OCR output as untrusted until reviewed

## Secrets

- Never place long-lived secrets in prompts
- Never commit real secrets to git
- Prefer runtime injection from a secret store or OS keychain
- Rotate channel tokens and document the owner of each token

## Logging

You need enough logs to answer what happened without turning logs into a second data leak.

Recommended defaults:

- Structured JSON logs
- Correlation IDs enabled
- Tool start/end logging enabled
- Policy decision logging enabled
- Prompt logging disabled unless there is a documented reason
- Sensitive tool outputs redacted or omitted
- Retention kept short and intentional

## RAG

- Require source metadata: owner, date, source, version, status
- Start with moderate chunk sizes, then tune from real misses
- Rebuild indexes intentionally, not automatically on every random file event
- Remove stale or duplicate documents before blaming the model

## Change control

- Snapshot config before risky changes
- Keep a rollback playbook next to the deployment docs
- Test one change at a time when debugging
- Record exceptions to the baseline in writing

## Good first production stance

If you need a plain-English default:

- local gateway
- authenticated access
- remote admin via SSH tunnel or Tailscale
- deny-heavy tool policy
- local or low-risk model for routine work
- hosted reasoning model only where it clearly helps
- documented rollback before broad rollout

## Why this matters

Most failures in small on-prem agent systems are not exotic.
They come from broad permissions, drift, stale data, weak rollback, and nobody owning the operational details.

Safe defaults reduce the blast radius while you learn where the real complexity is.
