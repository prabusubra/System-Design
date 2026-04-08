# Sliding Window Log Rate Limiter

## 📌 Definition

A Sliding Window Log Rate Limiter:
- Stores timestamp of each request  
- Allows a request only if the number of requests within a sliding time window is less than the defined limit  

---

## 🧠 Core Idea

- Maintain a queue of request timestamps  
- Continuously remove expired timestamps  
- Check current size before allowing a request  

---

## ⚙️ Java-style Pseudocode

```java
boolean allowRequest(String userId) {

    long currentTime = System.currentTimeMillis();

    Queue<Long> queue = requestLog.computeIfAbsent(
        userId, k -> new ArrayDeque<>()
    );

    synchronized (queue) {

        // Step 1: Remove expired timestamps
        while (!queue.isEmpty() &&
               currentTime - queue.peek() > windowSize) {
            queue.poll();
        }

        // Step 2: Check limit
        if (queue.size() < requestLimit) {
            queue.offer(currentTime);
            return true;
        }

        return false;
    }
}
```

---

## 🏗️ Data Structure

```java
Map<String, Queue<Long>> requestLog;
```

- Key → User ID / IP / API key  
- Value → Queue of timestamps  

---

## 🔁 Example

**Limit:** 5 requests / 10 seconds  

```
t=0   → request1 ✅  
t=1   → request2 ✅  
t=2   → request3 ✅  
t=3   → request4 ✅  
t=4   → request5 ✅  
t=5   → request6 ❌ (limit reached)  
t=11  → request6 ✅ (old entries removed)  
```

---

## ⚙️ Key Properties

- Sliding window → dynamic (last N milliseconds)  
- Log → stores every request  
- High accuracy → no boundary burst issue  

---

## ✅ When to Use

- Accuracy is critical  
- Security-sensitive APIs:
  - Login attempts  
  - OTP validation  
  - Payment APIs  
- Moderate traffic systems  
- Single-instance applications  

---

## ❌ When NOT to Use

- High traffic / large-scale systems  
- Distributed systems (in-memory limitation)  
- Memory-sensitive environments  
- Performance-critical paths  

---

## ⚖️ Pros

- High accuracy  
- Fair request handling  
- No burst issue at window boundaries  
- Simple to implement  
- Easy to debug (timestamps available)  

---

## ❌ Cons

- High memory usage (stores all timestamps)  
- O(n) cleanup per request  
- Not scalable  
- Requires synchronization  
- Not suitable for distributed systems  

---

## ⚠️ Concurrency

### Problem:
- Multiple threads accessing shared queue  
- Leads to race conditions  

### Fix:

```java
synchronized (queue)
```

---

## 🧪 Testing Strategies

### Basic
- ExecutorService with multiple threads  

### Intermediate
- ConcurrentUnit (JUnit-based concurrency testing)  

### Advanced
- jcstress (OpenJDK concurrency testing tool)  

---

## 🎯 Decision Rule

```
Use when: Accuracy > Performance
```

---

## 🆚 Comparison (Quick)

| Feature     | Sliding Window Log |
|------------|------------------|
| Accuracy   | High             |
| Memory     | High             |
| Performance| Medium           |
| Scalability| Low              |

---

## 🧾 Interview One-Liner

Sliding Window Log stores timestamps of each request and allows requests only if the number of requests in the current sliding window is below the defined limit.

---

## 🔥 Key Takeaway

- Sliding → moving time window  
- Log → store each request  
- Accurate but memory-heavy  
