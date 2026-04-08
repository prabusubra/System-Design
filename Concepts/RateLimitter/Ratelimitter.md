| Algorithm                           | When to Use                              | Use Case                   | Example                                |
| ----------------------------------- | ---------------------------------------- | -------------------------- | -------------------------------------- |
| **Fixed Window Counter**            | Simple, low traffic, basic throttling    | Internal tools, admin APIs | Limit admin panel to 100 req/min       |
| **Sliding Window Log** ⭐            | High accuracy, strict enforcement        | Security-sensitive APIs    | Login attempts: max 5 tries / 10 sec   |
| **Sliding Window Counter**          | Balance between accuracy & performance   | Medium-scale APIs          | User API limit: 100 req/min            |
| **Token Bucket** ⭐                  | High performance + burst handling        | Public APIs, microservices | API Gateway allowing bursts of traffic |
| **Leaky Bucket**                    | Smooth, constant rate processing         | Traffic shaping, streaming | Video streaming or message processing  |
| **Distributed (Redis-based)**       | Multiple instances, scalable systems     | Cloud apps, microservices  | Shared rate limit across all servers   |
| **Concurrency Limiter (Semaphore)** | Limit parallel requests (not time-based) | Resource protection        | Limit DB connections to 10             |


---

## 🎯 Quick Understanding

* **Accuracy needed?** → Sliding Window Log
* **Performance + burst?** → Token Bucket
* **Simple use?** → Fixed Window
* **Smooth flow?** → Leaky Bucket
* **Distributed system?** → Redis
* **Limit parallel calls?** → Semaphore

---
