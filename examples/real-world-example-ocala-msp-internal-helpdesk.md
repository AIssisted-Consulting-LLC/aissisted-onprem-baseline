# Real-World Example: Internal Helpdesk Agent for a Small Managed IT Provider

This is a sanitized field example for a small managed service provider supporting local SMB clients.

The goal is not to show off a flashy agent demo. The goal is to show what an operator can actually deploy, support, and roll back without drama.

## Scenario

A managed IT provider with 8 staff supports around 45 small-business clients. They want an internal-use agent that helps technicians:

- summarize troubleshooting notes
- search approved SOPs and client-specific runbooks
- draft ticket updates
- answer "where is the current procedure for X?"
- suggest next checks during common incidents

They do **not** want a fully autonomous agent making production changes across client environments.

## Constraints

- Client documentation includes sensitive network and credential-adjacent material.
- Different clients should not see each other's runbooks.
- Junior techs need guardrails, not a shell with a smiley face.
- The owner needs a way to disable risky features fast if behavior drifts.
- The stack must stay maintainable by a small team without a full-time platform engineer.

## Baseline design

### Trust boundary

Use one control plane for the MSP's internal staff only.
Do not expose the same tool-enabled gateway directly to end-clients.
If client-facing access is added later, split it into separate boundaries.

### Access path

- gateway bound to loopback on a small dedicated host or VM
- access through SSH tunnel or tailnet
- named operator accounts only
- auth enabled on any non-loopback surface

### Tool policy

Start with:

- file read access to approved documentation paths
- ticket draft generation
- no shell access
- no browser automation
- no outbound messaging tools
- human approval required for anything that changes state

If the team later adds shell or browser tooling for internal workflows, keep it off by default and scope it to a narrower operator group.

### RAG layout

Split the corpus into clear buckets:

- global SOPs
- vendor procedures
- client-specific runbooks
- archived or superseded procedures

Minimum metadata per document:

- source path
- client or business unit
- owner
- revision date
- document class
- active vs archived status

This matters more than people think. Weak metadata is how old procedures keep winning retrieval.

## What worked

The most useful early workflow was not "AI solves tickets."
It was "AI finds the right SOP fast, summarizes it, and drafts the update for the technician to review."

That gave the team:

- faster ticket notes
- less time hunting through shared folders
- more consistent handoff language between technicians
- fewer misses on standard triage steps

The operator win was that the system stayed boring:

- the agent was easy to explain
- failure modes were visible
- rollback was simple because the agent was mostly retrieval + drafting

## What broke first

The first issues were not model intelligence problems.
They were corpus problems.

Examples:

- duplicate SOPs with slightly different dates
- one scanned PDF with terrible extraction quality
- outdated runbooks ranking above current ones
- missing metadata for client name and owner

This produced inconsistent answers that looked like model flakiness but were really indexing and source hygiene problems.

## Fixes that mattered

1. Archive superseded procedures instead of leaving them mixed into the active corpus.
2. Add required metadata checks before indexing new documents.
3. Spot-check scanned PDFs and replace bad OCR outputs.
4. Make the agent show the source document path in technician-facing answers.
5. Keep the first version read-heavy and approval-heavy.

## Operational guardrails

Before go-live, the MSP wrote down:

- who owns document freshness
- who can approve prompt or tool changes
- what gets logged
- what data is excluded from the corpus
- how to disable the system if retrieval quality drops

That last point matters. If your only shutdown plan is panic, you do not have an operational plan.

## Why this example matters

This is a realistic first deployment pattern for small teams:

- narrow scope
- local access path
- retrieval before autonomy
- explicit trust boundary
- simple rollback

That pattern transfers well to legal support, internal HR knowledge, home services dispatch support, and other small-business operations.

## If you want to copy this pattern

Start with these questions:

1. Who is actually allowed to use the system?
2. Which documents are approved for retrieval?
3. What must never be exposed in prompts or logs?
4. What can the agent read?
5. What can the agent change?
6. How do you roll back in under 30 minutes?

If those answers are fuzzy, keep narrowing scope until they are not.
