# 📘 Day 21: Week-3 Wrap-Up + Mock Quiz + Mini System Design  
  
## ✅ What You Covered in Week 3  
* **Day 15:** Spring Security basics, authentication vs authorization  
* **Day 16:** JWT implementation, filters, role-based access  
* **Day 17:** Async processing, events, scheduling  
* **Day 18:** Caching, Spring Cache, Redis concepts  
* **Day 19:** Unit testing (JUnit, Mockito)  
* **Day 20:** Integration testing, Testcontainers  
  
  
## 🧠 Mini System Design: E-Commerce Checkout (Conceptual)  
This is a **very common interview problem**.  
  
**1. Requirements**  
**Functional**  
* Add items to cart  
* Place order  
* Process payment  
* Reduce inventory  
**Non-Functional**  
* Consistency  
* Reliability  
* Idempotency  
* Scalability  
  
**2. High-Level Flow**  
```
Cart → Checkout → Payment → Order → Inventory

```
  
**3. Key Design Considerations**  
**Idempotency**  
* Same payment request should not charge twice  
* Use idempotency key  
  
👉 Interview line:  
Payments must be idempotent.  
  
**Transaction Boundaries**  
* Order creation + inventory update  
* Use DB transactions  
  
**Async Processing**  
* Email confirmation  
* Invoice generation  
Use:  
* Events  
* @Async  
  
**Failure Handling**  
* Payment success, inventory fail?  
* Compensating actions (refund / retry)  
  
👉 Interview line:  
Distributed systems need failure recovery strategies.  
  
**4. Caching & Performance**  
* Cache product catalog  
* Don’t cache cart totals blindly  
* Cache read-heavy data only  
  
**5. Security**  
* JWT-based authentication  
* Only authenticated users can checkout  
* Validate ownership of cart  
  
## 🔍 How Interviewers Judge You Here  
They’re checking:  
* Can you think in **flows**, not just code?  
* Do you consider **failure scenarios**?  
* Do you understand **real-world trade-offs**?  
Not perfection — **clarity**.  
  
## ✅ Week-3 Final Self-Check  
You’re solid if you can explain:  
* JWT lifecycle end-to-end  
* Why async improves responsiveness  
* Cache hit vs miss vs eviction  
* Unit vs integration tests  
* Why Testcontainers matter  
If yes → you’re **production-ready backend-wise**.  
