# To avoid cascade failure in microservices when one service is down
- Use resilience patterns so failures stay local instead of spreading
    - Timeouts
    - Circuit breaker
    - Bulkheads
    - Retries with backoff
    - Fallback responses
    - Load shedding
        1. Rate limiting - Reject requests above a threshold.
        2. Priority-based dropping - Keep important requests, drop less important ones.
        3. Queue limits - If the queue is full, reject new work.
        4. Circuit breaker + fallback - If a dependency is failing, stop sending all traffic to it.
        5. Adaptive throttling - Reduce traffic automatically when latency/error rate rises.
    - Queue asynchronous work
    -  Rate limiting
    -  Health checks and service discovery
    -  Observability
    -  

## Comparison

| Pattern | Protects from | Main use |
|---|---|---|
| Load shedding | Overload in your system | Drop excess work |
| Rate limiting | Too many requests from clients | Control traffic |
| Circuit breaker | Failing downstream service | Stop dependency cascades |
