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
