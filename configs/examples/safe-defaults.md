# Safe Defaults

These defaults are intentionally conservative.

## Network

- Bind the gateway to loopback.
- Use SSH tunnel or tailnet for remote access.
- Do not publish WebSocket or admin ports directly to the public internet.

## Auth

- Require a long random token or equivalent auth on non-loopback access.
- Do not rely on obscurity, random ports, or private IP space alone.
- Rotate credentials when operators change.

## Tools

- Start with no shell access.
- Start with no browser automation.
- Start with workspace-only file access.
- Require explicit approval for destructive or high-impact tools.

## Storage

- Keep prompts, corpus, logs, and backups in separate paths.
- Do not let temp workspaces become permanent data stores.
- Keep raw uploads quarantined until processed.

## Models

- Route cheap models to low-risk classification and summarization.
- Route stronger models to complex reasoning only.
- Set timeouts and fallback behavior explicitly.
- Log provider rate-limit failures.

## RAG

- Prefer one clean corpus over many messy feeds.
- Keep metadata simple and consistent.
- Rebuild indexes from source documents, not from mystery blobs.

## OpenClaw-specific safe baseline

OpenClaw docs strongly support this pattern:

- one trusted operator boundary per gateway
- loopback bind by default
- auth on exposed surfaces
- per-peer DM session scoping
- deny-heavy tool profile to start
- approvals for dangerous exec paths

Good first checks:

- `openclaw security audit`
- `openclaw status`
- `openclaw gateway status`
- `openclaw doctor`
