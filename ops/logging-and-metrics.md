# Logging and Metrics

The point of observability is to answer operator questions fast.

## Minimum logs to keep

- service start and stop
- config version or checksum at startup
- auth failures
- inbound request receipt
- routing decision
- model/provider selection
- tool start, tool end, tool failure
- approval requested, granted, denied
- retrieval hit counts
- provider errors and rate limits
- rollback events

## Log fields that matter

- timestamp
- request or correlation ID
- operator or sender identity when appropriate
- channel or entry point
- model name
- tool name
- duration
- status
- error class

## Metrics worth graphing

- requests per minute
- p50 / p95 / p99 latency
- tool failure rate
- provider error rate
- queue depth
- token or cost usage if available
- retrieval hit rate
- empty retrieval rate
- process restarts
- disk free space
- CPU and memory pressure

## Alerts worth sending

- process down
- auth failures spike
- provider 429/5xx spike
- queue delay above threshold
- repeated tool timeout
- disk under safe threshold
- backup failure
- index build failure

## Suggested starting thresholds (tune to your environment)

These are conservative defaults meant to catch "boring" outages early. Adjust after you have a week of baseline data.

- **Disk free:** alert at < 15% (warning) and < 10% (critical)
- **CPU pressure:** sustained > 85% for 10 minutes (warning), > 95% for 5 minutes (critical)
- **Memory pressure / swap:** any sustained swap activity or OOM events = critical
- **Queue delay:** p95 queue delay > 30s (warning), > 2m (critical)
- **Tool timeouts:** > 3 timeouts for the same tool in 10 minutes (warning), > 10 (critical)
- **Provider errors:** 429/5xx rate > 2% over 5 minutes (warning), > 10% (critical)
- **Restarts:** > 2 restarts in 30 minutes (warning), > 5 (critical)
- **Backup:** any scheduled backup missed = warning; 2 consecutive misses = critical
- **Index build:** failure to complete within your normal window (or any repeated failure) = warning/critical depending on impact

## Retention and redaction (boring but critical)

Decide this before go-live, while you still have the leverage to keep it sane.

- **Retention:** pick a default (e.g., 7–30 days) and a reason to keep anything longer.
- **Redaction:** never log secrets; be intentional about logging prompts, tool args, and tool outputs.
- **Access:** logs are production data. Restrict who can read them.

Operator-friendly rule of thumb:

- If you would not paste it into a ticket, do not log it.
- If you must log it to debug, log a hash, ID, or a bounded excerpt.

## Logging mistakes

### Logging too little

You cannot tell whether the problem is policy, transport, tool, or model.

### Logging too much

You leak secrets, prompts, customer data, or regulated content into places nobody cleans up.

### Logging the wrong thing

A giant blob dump is not observability.
Structured events beat random paragraphs.

## If something breaks, check this order

1. process health
2. auth and transport
3. provider health
4. queue depth
5. tool failures
6. retrieval quality
7. recent config change
