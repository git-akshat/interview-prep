# 📘 Day 14: Week-2 Wrap-Up + Mini System Design  
  
# ✅ What You Covered in Week 2  
* **Day 8:** Spring, IoC, Dependency Injection, first REST API  
* **Day 9:** Controllers, Services, Repositories, REST principles, CRUD  
* **Day 10:** JPA, Hibernate, Entities, MySQL integration  
* **Day 11:** Exception handling, @ControllerAdvice, logging  
* **Day 12:** Profiles, configuration, bean lifecycle  
* **Day 13:** Spring Data JPA queries, pagination, sorting  
#   
  
# 🧠 Mini System Design: Blogging Platform (Conceptual)  
**1. Requirements**  
**Functional**  
* Users can create blogs  
* Users can comment  
* Users can like posts  
* Fetch blogs with pagination  
**Non-functional**  
* Scalable reads  
* Secure  
* Low latency  
  
**2. High-Level Entities**  
* User  
* BlogPost  
* Comment  
* Like  
Relationships:  
* User → BlogPost (One-to-Many)  
* BlogPost → Comment (One-to-Many)  
* User → Like → BlogPost (Many-to-Many)  
  
**3. API Design (Sample)**  
```
POST   /users
POST   /blogs
GET    /blogs?page=0&size=10
POST   /blogs/{id}/comments
POST   /blogs/{id}/like

```
  
**4. DB & Performance Thoughts**  
* Index on:  
    * blog_id  
    * user_id  
* Pagination for blogs  
* Cache popular blogs (Redis)  
👉 Interview line:  
Read-heavy systems benefit from caching and pagination.  
  
**5. Common Follow-ups Interviewers Ask**  
* How to prevent duplicate likes? → Unique constraint (user_id + blog_id)  
* How to scale reads? → Cache + DB read replicas  
* How to handle deletes? → Soft delete (is_deleted flag)  
  
# 🔍 Week-2 Final Self-Check  
You’re in good shape if you can confidently explain:  
* Why constructor injection is preferred  
* JPA vs Hibernate  
* Lazy vs eager loading  
* Page vs Slice  
* Where exceptions should be handled  
* Why configs should be externalized  
