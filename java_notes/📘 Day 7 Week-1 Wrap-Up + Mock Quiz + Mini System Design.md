# 📘 Day 7: Week-1 Wrap-Up + Mock Quiz + Mini System Design  
  
## ✅ What You’ve Covered in Week 1  
* **Day 1:** JVM, Heap vs Stack, GC, GC Roots, memory leaks  
* **Day 2:** Threads, Runnable, ExecutorService, synchronization, join()  
* **Day 3:** Collections internals, HashMap, collisions, fail-fast vs fail-safe  
* **Day 4:** synchronized vs Lock, deadlock, volatile, Atomic variables  
* **Day 5:** Callable, Future, thread pools, BlockingQueue, producer–consumer  
* **Day 6:** Java 8 lambdas, functional interfaces, streams, map vs flatMap  
  
## 🧠 Mini System Design: URL Shortener (Conceptual)  
This is *not* about drawing boxes. It’s about thinking clearly.  
  
**1. Requirements**  
**Functional**  
* Convert long URL → short URL  
* Redirect short URL → original URL  
**Non-functional**  
* Low latency  
* Highly available  
* Scalable  
* No duplicate short URLs  
  
**2. High-Level Flow**  
1. Client sends long URL  
2. Backend generates unique short key  
3. Store mapping in DB  
4. Return short URL  
5. On access, redirect to original URL  
  
**3. Short URL Generation**  
Options:  
* Counter + Base62 encoding  
* UUID (less readable)  
* Hash (needs collision handling)  
👉 Interview-friendly choice: **Auto-increment ID + Base62**  
  
**4. Data Model (Simple)**  
```
URL_MAPPING
-------------------
id (PK)
short_key
long_url
created_at

```
Index on short_key.  
  
**5. Cache Layer**  
* Use Redis  
* Key: short_key  
* Value: long_url  
Why?  
* Avoid DB hit on every redirect  
* Massive read traffic optimization  
  
**6. Failure & Scaling Notes**  
* Cache miss → DB lookup → populate cache  
* Multiple app instances behind load balancer  
* DB replication for reads  
👉 Interview closing line:  
“Reads dominate writes, so caching is critical.”  
  
## 🔍 How Interviewers Judge You Here  
They don’t expect perfection. They look for:  
* Clear thinking  
* Trade-offs  
* Simplicity first  
If you explain this calmly → you pass.  
  
## ✅ Week-1 Final Self-Check  
You’re in good shape if you can:  
* Explain GC roots without hesitation  
* Explain HashMap collisions confidently  
* Explain race condition vs visibility  
* Explain why streams are lazy  
* Explain producer–consumer with BlockingQueue  
