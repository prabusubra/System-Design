# Token Bucket Rate Limiter

## Overview

The **Token Bucket** algorithm is a widely used rate-limiting mechanism for controlling the number of requests that can be processed over a period of time. It provides flexibility for **bursty traffic** while enforcing a maximum request rate.

**Key Concepts:**
- **Bucket Capacity (`maxTokens`)**: Maximum number of tokens the bucket can hold.
- **Refill Rate (`refillRate`)**: Number of tokens added per unit time (e.g., per second).
- **Token Consumption**: Each request consumes one or more tokens. Requests exceeding available tokens are **rejected** or **delayed**.

---

## Implementation Strategies

Two main strategies exist for refilling the token bucket:

1. **Scheduler-based Refill**  
2. **Lazy (On-Demand) Refill**

---

### 1. Scheduler-Based Refill

**Description:**  
A background scheduler (cron or `@Scheduled` in Spring) periodically adds tokens to the bucket at a fixed interval, independent of incoming requests.

**Example (Spring Boot):**

```java
@Service
public class TokenBucketScheduler {

    private final int maxTokens = 100;
    private final int refillRate = 10; // tokens per second
    private int currentTokens = 100;

    @Scheduled(fixedRate = 1000) // every 1 second
    public synchronized void refillTokens() {
        currentTokens = Math.min(maxTokens, currentTokens + refillRate);
    }

    public synchronized boolean tryConsume() {
        if (currentTokens > 0) {
            currentTokens--;
            return true;
        }
        return false;
    }
}
````

**Use Cases:**

* IoT devices sending data at predictable intervals
* Low-traffic APIs with minor bursts
* Throttling scheduled batch jobs
* Embedded systems with deterministic timing

**Pros:**

* Simple implementation
* Deterministic token refill
* Works even when no requests occur

**Cons:**

* Less efficient for low-traffic systems
* Slightly inaccurate under high system load

---

### 2. Lazy (On-Demand) Refill

**Description:**
Tokens are refilled **only when a request arrives**, based on the **elapsed time** since the last refill.

**Example (Spring Boot):**

```java
@Service
public class LazyTokenBucket {

    private final int maxTokens = 100;
    private final double refillRate = 10; // tokens per second
    private double currentTokens = 100;
    private long lastRefillTime = System.currentTimeMillis();

    public synchronized boolean tryConsume() {
        long now = System.currentTimeMillis();
        long elapsedMillis = now - lastRefillTime;

        double tokensToAdd = (elapsedMillis / 1000.0) * refillRate;
        currentTokens = Math.min(maxTokens, currentTokens + tokensToAdd);
        lastRefillTime = now;

        if (currentTokens >= 1) {
            currentTokens -= 1;
            return true;
        }
        return false;
    }
}
```

**Use Cases:**

* High-throughput REST APIs
* Serverless/cloud functions (no background scheduler)
* Distributed microservices with local buckets
* Bursty user-facing traffic (login, search, uploads)
* Dynamic per-user rate limits

**Pros:**

* Accurate token accounting
* Efficient (no continuous background job)
* Handles bursts and irregular traffic well

**Cons:**

* Slightly more complex implementation
* Tokens only refill when a request arrives

---

## Comparison Table

| Feature         | Scheduler-Based                   | Lazy (On-Demand)                           |
| --------------- | --------------------------------- | ------------------------------------------ |
| Refill Timing   | Fixed intervals                   | On request                                 |
| Traffic Pattern | Predictable                       | Bursty / unpredictable                     |
| Complexity      | Simple                            | Slightly complex                           |
| Efficiency      | Low if traffic is low             | High, no wasted CPU                        |
| Precision       | Approximate                       | High (matches elapsed time)                |
| Use Case        | IoT, batch jobs, low-traffic APIs | REST APIs, serverless, distributed systems |

---

## Best Practices

1. **Centralized Token Management:** Ensure thread-safe access using `synchronized` blocks or `AtomicInteger`.
2. **Logging & Monitoring:** Track rejected requests for metrics.
3. **Idempotency:** Ensure repeated requests under rate limiting don’t produce side effects.
4. **Resilience:** Combine with circuit breakers for downstream APIs.
5. **Configuration:** Externalize `maxTokens` and `refillRate` in `application.yml` for flexibility.

---

## References

* [Token Bucket Algorithm - Wikipedia](https://en.wikipedia.org/wiki/Token_bucket)
* [Rate Limiting in Microservices](https://martinfowler.com/articles/rate-limiting.html)
* [Spring @Scheduled Documentation](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/Scheduled.html)

---

