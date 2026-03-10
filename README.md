# AIssisted On-Prem Baseline

A practical baseline for running AI agents locally without turning your network into a science project.

This repo is for SMB operators, local-first teams, and IT consultants who need a sane starting point. It is stack-agnostic. OpenClaw is the primary example because it exposes the right operational questions, but the controls here generalize to other agent stacks.

If you want the short version: keep the control plane local, keep secrets out of prompts, narrow tool access, log enough to debug, and make rollback boring.

## Who this is for

- Small businesses that want local-first or self-hosted agent workflows
- Consultants building on-prem or hybrid agent systems for clients
- Teams that need RAG, automations, or messaging agents without loose security
- Operators who will have to maintain this after launch

## What this repo is

- A field guide
- A baseline set of docs and checklists
- Safe defaults to adapt, not a product install script
- Opinionated about operations, not opinionated about one vendor

## What this repo is not

- A full reference architecture for every environment
- A compliance certification package
- A promise that one host can safely serve many adversarial users
- A replacement for vendor docs, backups, or change control

## Reference architecture

```text
                         +------------------------+
                         |  Admin workstation     |
                         |  SSH / VPN / Tailscale |
                         +-----------+------------+
                                     |
                                     v
+------------------+      +----------+-----------+      +------------------+
| User channels    | ---> | Agent gateway        | ---> | Model backends   |
| Web / chat / API |      | routing + policy     |      | local or remote  |
+------------------+      +----------+-----------+      +------------------+
                                     |
                   +-----------------+------------------+
                   |                 |                  |
                   v                 v                  v
          +----------------+  +-------------+  +------------------+
          | Tool runner    |  | RAG service |  | Logs / metrics   |
          | exec/browser   |  | embed/index |  | traces / alerts  |
          +--------+-------+  +------+------+  +---------+--------+
                   |                 |                   |
                   v                 v                   v
          +----------------+  +-------------+  +------------------+
          | Workspace /    |  | Document    |  | Backup / rollback|
          | temp storage   |  | corpus      |  | snapshots        |
          +----------------+  +-------------+  +------------------+
```

### OpenClaw example mapping

- **Agent gateway** = OpenClaw Gateway
- **User channels** = Telegram, WhatsApp, Discord, web, or local UI
- **Tool runner** = OpenClaw tools with policy gates
- **Admin workstation** = loopback UI, SSH tunnel, or tailnet access
- **Logs / metrics** = OpenClaw logs plus host-level monitoring

## Start here

1. Pick one trust boundary. One client or one team per gateway.
2. Pick one host owner. No shared root access.
3. Keep the gateway bound to loopback unless you have a real reason not to.
4. Put remote access behind SSH or a tailnet. Do not open raw agent ports to the internet.
5. Start with a deny-heavy tool policy. Re-enable tools one by one.
6. Separate secrets, prompts, workspaces, and customer data stores.
7. Decide what gets logged before go-live. Prompts, tool args, outputs, and PII all need a rule.
8. Build rollback before broad rollout. Snapshot config, prompts, indexes, and dependencies.
9. Test failure modes on purpose. Kill the model backend. Break DNS. Fill disk. Expire a token.
10. Write the handoff policy before you let non-builders operate the system.

## Baseline operating rules

### 1. One trust boundary per control plane

Do not run one agent gateway for multiple mutually untrusted users and call it isolated.

Per-user chat sessions are not the same as host authorization.
If several people can steer the same tool-enabled agent, they share the same blast radius.
If the users do not trust each other, split the deployment.

For OpenClaw, this lines up with the documented personal-assistant model: one trusted operator boundary per gateway, not hostile multi-tenant isolation on one shared gateway.

### 2. Keep the gateway local by default

The safest default is:

- gateway on loopback
- token or password auth enabled
- remote operator access through SSH tunnel or tailnet
- browser automation and device control off until needed

If you expose the control plane directly, your incident plan needs to get much better.

### 3. Treat tools like production privileges

Most agent incidents are not model bugs. They are permission bugs.

Questions to answer before enabling any tool:

- What data can this tool read?
- What state can it change?
- Can it reach the network?
- Can it escape the working directory?
- Does it need human approval every time?
- What logs prove what it did?

If you do not have answers, do not enable the tool.

### 4. Keep secrets out of the model path

Do not paste secrets into prompts.
Do not store long-lived credentials in repos.
Do not hand the agent a `.env` file just because it is convenient.

Use short, scoped credentials where possible.
If a tool needs a secret, inject it at execution time and log the call without logging the secret.

### 5. Treat RAG as a data pipeline, not a magic folder

Bad document hygiene causes most RAG failures.

Common problems:

- duplicate documents
- stale copies that out-rank current policy
- PDF text extraction that shreds tables
- missing metadata like owner, date, source, or revocation status
- giant chunks that bury the answer
- tiny chunks that lose meaning

If your retrieval quality is poor, the model is often not the first thing to blame.

### 6. Logging is for operators, not just developers

You need enough detail to answer these questions fast:

- Did the request reach the gateway?
- Did the policy block it?
- Which model handled it?
- Which tools ran?
- What failed first?
- Was the issue data, auth, transport, or capacity?

Log correlation IDs. Log tool start and end. Log model/provider errors. Log queue delay. Log approval decisions.

Do not log raw customer data forever because it might be useful later.

## Security posture

### What we do

- Prefer one gateway per trust boundary
- Keep control plane local by default
- Put remote access behind SSH, VPN, or tailnet
- Use auth on any non-loopback surface
- Start from least privilege
- Keep workspaces narrow
- Require approval for dangerous actions
- Separate data planes: prompts, docs, secrets, logs, backups
- Rotate tokens and document who owns them
- Snapshot before risky changes
- Review audit logs after config changes

### What we do not do

- We do not assume chat session isolation equals OS isolation
- We do not expose agent control ports publicly by default
- We do not give broad shell access to a general-purpose agent on day one
- We do not call a shared multi-user bot a hardened tenant boundary
- We do not trust scraped docs, OCR, or user uploads without review
- We do not store secrets in prompts, tickets, or screenshots
- We do not upgrade core pieces without a rollback path

## Common operator failures

### "The agent replied yesterday and is silent today"

Check these first:

- channel auth or webhook health
- expired provider key
- policy change blocking sender, room, or mention rules
- gateway process up but probe failing
- queue backed up by a slow tool or long-context model call

For OpenClaw specifically, the usual first ladder is:

- `openclaw status`
- `openclaw gateway status`
- `openclaw logs --follow`
- `openclaw doctor`
- `openclaw channels status --probe`

### "The model is bad"

Usually not the first problem.
Check:

- missing retrieval hits
- stale index
- wrong model route for the task
- broken prompt variable
- tool timeout before generation
- provider rate limits
- context overflow or silent truncation

### "RAG is inconsistent"

Check:

- chunk size and overlap
- source freshness
- duplicate docs
- OCR quality
- metadata filters
- whether images, tables, and captions survived extraction
- whether the answer should have come from retrieval at all

### "The deployment is secure because it is on-prem"

No.
On-prem reduces some exposure. It does not fix bad auth, bad permissions, flat networks, weak secrets, or sloppy operator habits.

## Troubleshooting field notes

### If the gateway is reachable but acting wrong

Check config drift first.
Look for:

- changed bind address
- changed auth source
- changed default model route
- newly enabled tools
- stale environment variables still winning over config

### If browser or desktop automation is flaky

Check the human layer before the code layer.
Look for:

- wrong browser profile
- missing extension attachment
- stale session cookies
- app not in foreground
- OS permission prompts never approved

### If remote access is unstable

Check transport before blaming the agent.
Look for:

- SSH tunnel drops
- tailnet ACL changes
- TLS mismatch
- DNS split-horizon weirdness
- reverse proxy timeouts on WebSocket traffic

### If cost or latency spikes

Check:

- fallback model loop
- long contexts on small tasks
- retry storms
- tool loops
- duplicate event delivery
- oversized retrieval payloads

## A sane baseline stack

Use equivalents if your tooling differs.

- **Gateway/orchestrator:** OpenClaw, or another gateway with strict tool policy and session routing
- **Model path:** local model for low-risk work, hosted model for hard reasoning, explicit routing between them
- **RAG store:** one well-understood vector store, not three
- **Secrets:** OS secret store, Vault, 1Password CLI, Doppler, or cloud KMS
- **Remote access:** SSH tunnel or Tailscale
- **Logs:** structured logs to local files plus host metrics
- **Backups:** config snapshots, document corpus snapshots, and index rebuild procedure

## Files in this repo

- `checklists/deployment-checklist.md` — preflight and go-live checks
- `checklists/rag-quality-checklist.md` — retrieval quality review
- `checklists/handoff-policy.md` — who may change what
- `configs/examples/.env.template` — example variables only
- `configs/examples/safe-defaults.md` — conservative baseline settings
- `examples/real-world-example-ocala-msp-internal-helpdesk.md` — sanitized small-team deployment pattern
- `ops/logging-and-metrics.md` — what to log and alert on
- `ops/rollback-playbook.md` — how to revert without panic
- `CONTACT.md` — if you want help implementing this

## How to use this repo

1. Read the README once.
2. Copy the checklists into your actual project tracker.
3. Adapt the safe defaults to your stack.
4. Make your exceptions explicit.
5. Run a tabletop failure review before rollout.
6. Hand the repo to the person who will be on call.

## Final advice

Small systems fail in boring ways.
Bad defaults. Loose access. Silent drift. No rollback. No owner.
Fix those first.

Maintained by AIssisted Consulting (Ocala, FL) — on-prem AI for small businesses.