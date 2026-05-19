# 📘 Day 22: Project Setup – Task Management System (DB Design & Entities)  
  
## 🎯 What We’re Building (Clear Vision)  
**Task Management System**  
Features (end goal):  
* User registration & login (JWT)  
* Create / update / delete tasks  
* Assign tasks to users  
* Task status (TODO, IN_PROGRESS, DONE)  
* Role-based access (ADMIN / USER)  
* Pagination, caching, async reminders  
  
👉 Interview framing:  
“I built a task management backend with Spring Boot, JWT security, JPA, caching, and async processing.”  
  
## 1. High-Level Architecture  
```
Controller → Service → Repository → DB
                    ↓
                 Cache / Events

```
You already know this — now we **apply it end-to-end**.  
  
## 2. Core Domain Entities (Very Important)  
We start with **minimum correct entities** (don’t overdesign).  
**Entities**  
1. User  
2. Task  
  
## 3. User Entity (Authentication & Ownership)  
**User Fields**  
* id  
* username  
* password  
* role (ADMIN / USER)  
  
**User Entity Code**  
```
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String username;

    @Column(nullable = false)
    private String password;

    @Enumerated(EnumType.STRING)
    private Role role;

    // getters & setters
}

```
  
**Role Enum**  
```
public enum Role {
    USER,
    ADMIN
}

```
👉 Interview line:  
Store roles as STRING, not ORDINAL, to avoid breaking changes.  
  
## 4. Task Entity (Core Business Object)  
**Task Fields**  
* id  
* title  
* description  
* status  
* createdAt  
* assignedTo (User)  
  
**TaskStatus Enum**  
```
public enum TaskStatus {
    TODO,
    IN_PROGRESS,
    DONE
}

```
  
**Task Entity Code**  
```
@Entity
@Table(name = "tasks")
public class Task {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    private String description;

    @Enumerated(EnumType.STRING)
    private TaskStatus status;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User assignedTo;

    private LocalDateTime createdAt;

    @PrePersist
    public void onCreate() {
        createdAt = LocalDateTime.now();
    }

    // getters & setters
}

```
👉 Interview line:  
Use LAZY loading for relationships to avoid unnecessary queries.  
  
## 5. Entity Relationships (Explain Clearly)  
* **One User → Many Tasks**  
* Each task belongs to **one user**  
DB-wise:  
```
users (id)
tasks (id, user_id)

```
👉 Interview explanation:  
This is a unidirectional Many-to-One relationship from Task to User.  
  
## 6. Repository Layer  
**UserRepository**  
```
public interface UserRepository
        extends JpaRepository<User, Long> {

    Optional<User> findByUsername(String username);
}

```
  
**TaskRepository**  
```
public interface TaskRepository
        extends JpaRepository<Task, Long> {

    List<Task> findByAssignedTo(User user);

    Page<Task> findByStatus(TaskStatus status, Pageable pageable);
}

```
  
## 7. Why We Start with DB First (Interview Insight)  
* DB schema defines everything  
* APIs evolve around data model  
* Prevents refactoring later  
👉 Interview line:  
Always design entities and relationships before writing controllers.  
  
## 8. Common Interview Questions (Day 22)  
**Q1. Why use enums for status and role?**  
Type safety and readability.  
  
**Q2. Why LAZY for ManyToOne?**  
Avoid fetching user data unless needed.  
  
**Q3. Why @PrePersist?**  
To automatically set timestamps.  
  
**Q4. Why not bidirectional mapping now?**  
Avoid complexity and N+1 issues initially.  
  
## 📝 Day 22 MCQs  
**Q1. Why prefer EnumType.STRING?**  
a) Faster b) Smaller storage c) Safer for schema changes d) Required by JPA  
✅ **Answer:** c)  
  
**Q2. Default fetch type for ManyToOne?**  
a) LAZY b) EAGER c) BOTH d) NONE  
✅ **Answer:** b) ⚠️ But we **explicitly set LAZY**.  
  
**Q3. Where should createdAt logic go?**  
a) Controller b) Service c) Entity d) Repository  
✅ **Answer:** c)  
  
**Q4. Which layer owns business rules?**  
a) Controller b) Service c) Repository d) Entity  
✅ **Answer:** b)  
  
## 🔨 Project Task – Day 22  
**Task Management System – Foundation**  
1. Create Spring Boot project (if not already)  
2. Add entities:  
    * User  
    * Task  
    * Enums  
3. Create repositories  
4. Configure MySQL / PostgreSQL  
5. Run app → verify tables created  
6. Push code to GitHub  
