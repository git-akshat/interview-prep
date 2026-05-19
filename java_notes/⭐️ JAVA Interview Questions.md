# ⭐️ JAVA Interview Questions  
  
## Day 1 Interview Q&A  
* JDK vs JRE vs JVM  
* Difference b/w Heap vs Stack  
* How does GC know what to delete?  
* What happens when you do new String("abc")  
* GC roots  
* Stop-the-world pause  
* Why cyclic references don’t leak in Java  
  
——————————————————————————————————————  
##   
## Day 2 Interview Q&A  
**Q1. Calling run() instead of start() does what?** Runs in current thread only  
  
**Q2. Runnable vs Callable difference?** Callable returns a value  
  
**Q3. ExecutorService advantage?** Reuses threads from a pool  
  
**Q4. Which keyword ensures thread safety?** synchronized  
  
**Q5. What does join() do?** Blocks current thread until another finishes  
  
**Q6. Will Main done always print last?**  
```
t.start();
System.out.println("Main done");

```
No. Execution order is **not guaranteed**.  
  
**Q7. Will count always be 2000 without synchronized? Why?**  
No. count++ is not atomic → race condition.  
  
——————————————————————————————————————  
  
## Day 3 Interview Q&A  
**Q1. How does HashMap work internally?**  
Uses hashCode → bucket → equals → value retrieval.  
  
**Q2. What happens if two keys have same hashcode?**  
Stored in same bucket (linked list / tree).  
  
**Q3. Why HashMap is not thread-safe?**  
No synchronization → race conditions.  
  
**Q4. Difference between HashMap and Hashtable?**  
Hashtable is synchronized, slower, legacy.  
  
**Q5. What method is used to find bucket index?** hashCode()  
  
**Q6. When does HashMap convert list to tree?** Bucket size > 8 (Java 8+)  
  
**Q7. Which is fail-safe?** ConcurrentHashMap  
  
**Q8. Can HashMap store null keys?** Yes (one null key)  
  
**Q9. What happens if equals is overridden but hashCode is not?** HashMap lookup breaks → key not found.  
  
——————————————————————————————————————  
#   
## Day 4 Interview Q&A  
**Q1. What is volatile?**  
* Ensures visibility of variable updates across threads.  
* fetch data from main/shared memory  
* don’t use cached memory  
  
**Q2. Does volatile guarantee thread safety?**  
No. It does not provide atomicity.  
  
**Q3. AtomicInteger vs synchronized?**  
AtomicInteger uses CAS(Compare-And-Swap) → faster, non-blocking.  
  
**Q4. What is deadlock?**  
Threads waiting indefinitely for resources held by each other.  
  
**Q5. volatile ensures?** Visibility  
  
**Q6. Which is atomic?** AtomicInteger.incrementAndGet()  
  
**Q7. Deadlock occurs when?** Circular waiting for locks  
  
**Q8. tryLock() belongs to?** Lock interface  
  
**Q9. Is this thread-safe? Why/why not?**  
```
volatile int count = 0;
count++;

```
Not thread-safe. count++ is not atomic.  
  
——————————————————————————————————————  
  
## Day 5 Interview Q&A  
**Q1. Runnable vs Callable?**  
Runnable → no return Callable → returns value + checked exceptions  
  
**Q2. What does Future.get() do?**  
Blocks until computation finishes.  
  
**Q3. Why BlockingQueue is better than wait/notify?**  
Less error-prone, thread-safe, cleaner design.  
  
**Q4. Can you get result from Runnable?**  
No.  
  
**Q5. Which interface returns a value?** Callable  
  
**Q6. What happens when Future.get() is called?** Blocks until result is available  
  
**Q7. Which queue blocks automatically?** BlockingQueue  
  
**Q8. Producer–Consumer is solved using?** BlockingQueue  
  
**Q9. What happens if queue is full and producer calls put()?** Producer thread blocks.  
  
——————————————————————————————————————  
  
## Day 6 Interview Q&A  
  
**Q1. What is a stream?**  
A sequence of elements supporting functional operations.  
  
**Q2. Difference between map and flatMap?**  
map transforms, flatMap flattens.  
  
**Q3. Are streams reusable?**  
No. Streams can be consumed only once.  
  
**Q4. When does stream execute?**  
Only when terminal operation is called.  
  
## Day 6 Quiz   
**Q1. Which of the following is a *terminal* stream operation?**  
a) filter() b) map() c) forEach() d) peek()  
✅ **Answer:** c) forEach() **Explanation:** Terminal operations trigger stream execution. filter, map, peek are intermediate.  
  
**Q2. Which stream operation is *lazy* in nature?**  
a) collect() b) forEach() c) filter() d) count()  
✅ **Answer:** c) filter() **Explanation:** Intermediate operations are lazy and execute only when a terminal operation is invoked.  
  
**Q3. What is the key difference between map() and flatMap()?**  
a) map() modifies elements, flatMap() filters them b) map() returns a stream, flatMap() returns a list c) map() produces one output per input, flatMap() flattens nested streams d) There is no difference  
✅ **Answer:** c) **Explanation:** flatMap() removes one level of nesting.  
  
**Q4. Can a Java Stream be reused once a terminal operation is called?**  
a) Yes, always b) Yes, if it’s a parallel stream c) No, streams can be consumed only once d) Only if the source is a List  
✅ **Answer:** c) **Explanation:** A stream is closed after a terminal operation.  
  
**Q5. Which functional interface takes no input and returns a value?**  
a) Consumer<T> b) Predicate<T> c) Function<T, R> d) Supplier<T>  
✅ **Answer:** d) Supplier<T> **Explanation:** Supplier provides a value without taking any input.  
  
**Q6. What will be the output?**  
  
```
Stream.of(1, 2, 3, 4)
      .map(n -> n * 2)
      .filter(n -> n > 4)
      .forEach(System.out::println);

```
a) 2 4 6 8 b) 4 6 8 c) 6 8 d) 8  
✅ **Answer:** c) **Explanation:** After doubling → 2,4,6,8 → filter > 4 → 6,8  
  
**Q7. Which statement about streams is TRUE?**  
a) Streams store data b) Streams modify the source collection c) Streams are evaluated eagerly d) Streams process data from a source  
✅ **Answer:** d) **Explanation:** Streams don’t store data; they process data from a source.  
  
## Coding Exercises (Day 6)  
**Employee Stream Operations**  
```
class Employee {
    int id;
    String name;
    String dept;
    int salary;
}

```
Tasks:  
1. Filter employees with salary > 50k  
2. Group by department  
3. Sort by salary descending  
4. Find highest paid employee  
5. Count employees per department  
  
——————————————————————————————————————  
  
## Day 7 Interview Q&A  
**Q1. Which memory area is shared across threads?**  
a) Stack b) Heap c) Program Counter d) Native Stack  
✅ **Answer:** b) Heap  
  
**Q2. What happens if equals() is overridden but hashCode() is not?**  
a) Compile-time error b) Runtime exception c) HashMap lookup may fail d) JVM auto-fixes it  
✅ **Answer:** c)  
  
**Q3. What does volatile guarantee?**  
a) Atomicity b) Mutual exclusion c) Visibility d) Thread scheduling  
✅ **Answer:** c)  
  
**Q4. Which method actually starts a new thread?**  
a) run() b) execute() c) start() d) call()  
✅ **Answer:** c)  
  
**Q5. Which collection is fail-safe?**  
a) ArrayList b) HashMap c) ConcurrentHashMap d) HashSet  
✅ **Answer:** c)  
  
**Q6. What happens when Future.get() is called?**  
a) Returns immediately b) Throws exception if not ready c) Blocks until result is available d) Cancels task  
✅ **Answer:** c)  
  
**Q7. What is true about Java Streams?**  
a) They store data b) They modify the source collection c) They are lazy until terminal operation d) They run in parallel by default  
✅ **Answer:** c)  
##   
**Q8. What’s the issue here?**  
```
volatile int count = 0;
count++;

```
✅ **Answer:** Not thread-safe. count++ is not atomic. Volatile ensures visibility, not atomicity.  
  
**Q9. Predict the output**  
```
Thread t = new Thread(() -> {
    for (int i = 1; i <= 3; i++)
        System.out.println("Child " + i);
});
t.start();
System.out.println("Main");

```
✅ **Answer:** Order is **not guaranteed**. "Main" may print before, after, or between child outputs.  
  
——————————————————————————————————————  
  
## Day 8 Interview Q&A  
**Q1. What is IoC?**  
Framework controls object creation.  
  
**Q2. What is Dependency Injection?**  
Injecting dependencies instead of creating them.  
  
**Q3. Autowiring vs Constructor Injection?**  
Constructor injection is safer and preferred.  
  
**Q4. What does @SpringBootApplication do?**  
Enables auto-configuration and component scanning.  
  
## 📝 Day 8 MCQs  
**Q1. What does Spring manage?**  
a) JVM memory b) Threads c) Objects and dependencies d) OS processes  
✅ **Answer:** c)  
  
**Q2. Which DI type is recommended?**  
a) Field b) Setter c) Constructor d) Static  
✅ **Answer:** c)  
  
**Q3. @Service is a specialization of?**  
a) @Controller b) @Component c) @Configuration d) @Bean  
✅ **Answer:** b)  
  
**Q4. Default embedded server in Spring Boot?**  
a) Jetty b) Netty c) Undertow d) Tomcat  
✅ **Answer:** d)  
  
——————————————————————————————————————  
##   
## Day 9 Interview Q&A  
**Q1. Why Service layer exists?**  
To keep business logic separate from controllers.  
  
**Q2. Controller vs RestController?**  
* @Controller → returns view  
* @RestController → returns JSON  
  
**Q3. What is idempotency?**  
Same request → same result.  
  
**Q4. Can controller call repository directly?**  
Bad practice. Should go through service.  
  
## Day 9 MCQs  
**Q1. Which layer contains business logic?**  
a) Controller b) Repository c) Service d) Entity  
✅ **Answer:** c)  
  
**Q2. Which HTTP method is NOT idempotent?**  
a) GET b) PUT c) DELETE d) POST  
✅ **Answer:** d)  
  
**Q3. Which annotation returns JSON by default?**  
a) @Controller b) @Component c) @RestController d) @Service  
✅ **Answer:** c)  
  
**Q4. What should a Repository contain?**  
a) Business logic b) HTTP handling c) DB access code d) UI logic  
✅ **Answer:** c)  
  
——————————————————————————————————————  
##   
## Day 10 Interview Q&A  
**Q1. What is ORM?**  
Mapping objects to relational tables.  
  
**Q2. What does @Entity do?**  
Marks class for persistence.  
  
**Q3. JPA vs Hibernate?**  
JPA is spec, Hibernate is implementation.  
  
**Q4. What is ddl-auto?**  
Controls schema generation behavior.  
  
## Day 10 MCQs  
**Q1. Which annotation marks a primary key?**  
a) @Column b) @Id c) @GeneratedValue d) @Table  
✅ **Answer:** b)  
  
**Q2. Which is NOT a JPA implementation?**  
a) Hibernate b) EclipseLink c) JDBC d) OpenJPA  
✅ **Answer:** c)  
  
**Q3. Default fetch type for @OneToMany?**  
a) EAGER b) LAZY c) BOTH d) NONE  
✅ **Answer:** b)  
  
**Q4. Which method saves entity to DB?**  
a) persist() b) insert() c) save() d) push()  
✅ **Answer:** c)  
  
——————————————————————————————————————  
##   
## Day 11 Interview Q&A  
**Q1. Why not handle exceptions in controller?**  
Leads to duplication and poor separation of concerns.  
  
**Q2. What is @ControllerAdvice?**  
Global exception handling mechanism.  
  
**Q3. Difference between @ControllerAdvice and @RestControllerAdvice?**  
@RestControllerAdvice returns JSON by default.  
  
**Q4. Why RuntimeException for custom exceptions?**  
Spring handles them automatically without mandatory try-catch.  
  
## 📝 Day 11 MCQs  
**Q1. Where should business exceptions be thrown?**  
a) Controller b) Repository c) Service d) Entity  
✅ **Answer:** c)  
  
**Q2. Which annotation handles exceptions globally?**  
a) @ExceptionHandler b) @ControllerAdvice c) @ErrorPage d) @TryCatch  
✅ **Answer:** b)  
  
**Q3. Which log level is most severe?**  
a) INFO b) DEBUG c) WARN d) ERROR  
✅ **Answer:** d)  
  
**Q4. Which is bad logging practice?**  
a) Logging errors b) Using log levels c) Using System.out.println d) Centralized logging  
✅ **Answer:** c)  
  
——————————————————————————————————————  
##   
## Day 12 Interview Q&A  
**Q1. Why use profiles?**  
Different configs for different environments.  
  
**Q2. @Value vs @ConfigurationProperties?**  
@ConfigurationProperties is cleaner for multiple values.  
  
**Q3. When does @PostConstruct run?**  
After bean creation and dependency injection.  
  
**Q4. Default bean scope?**  
singleton.  
  
## 📝 Day 12 MCQs  
**Q1. What is the default Spring profile?**  
a) prod b) test c) default d) dev  
✅ **Answer:** c)  
  
**Q2. Which annotation binds multiple properties to a POJO?**  
a) @Value b) @Bean c) @Profile d) @ConfigurationProperties  
✅ **Answer:** d)  
  
**Q3. Which scope creates a new bean every time?**  
a) singleton b) prototype c) session d) request  
✅ **Answer:** b)  
  
**Q4. When is @PreDestroy called?**  
a) Before bean creation b) After initialization c) Before application shutdown d) On every request  
✅ **Answer:** c)  
##   
——————————————————————————————————————  
##   
## Day 13 Interview Q&A  
**Q1. How does Spring Data JPA create queries?**  
By parsing method names into JPQL.  
  
**Q2. JPQL vs Native Query?**  
JPQL uses entities, native uses tables.  
  
**Q3. Why pagination is important?**  
Prevents memory and performance issues.  
  
**Q4. Page vs Slice?**  
Page gives total count, Slice doesn’t.  
  
## 📝 Day 13 MCQs  
**Q1. Which interface enables pagination?**  
a) Pageable b) Page c) Sort d) Repository  
✅ **Answer:** a)  
  
**Q2. Which query uses entity names?**  
a) SQL b) Native Query c) JPQL d) JDBC  
✅ **Answer:** c)  
  
**Q3. Which method name is valid?**  
a) findBytitle() b) findBy_Title() c) findByTitleContaining() d) findWithTitle()  
✅ **Answer:** c)  
  
**Q4. Which is faster when total count is not needed?**  
a) Page b) Slice c) List d) Set  
✅ **Answer:** b)  
##   
——————————————————————————————————————  
##   
## Day 14 Interview Q&A  
**Q1. What does IoC mean in Spring?**  
a) JVM controls memory b) Objects control dependencies c) Framework controls object creation d) Controller controls service  
✅ **Answer:** c)  
  
**Q2. Which annotation is best for injecting mandatory dependencies?**  
a) @Autowired on field b) Setter injection c) Constructor injection d) Static injection  
✅ **Answer:** c)  
  
**Q3. Which layer should throw business exceptions?**  
a) Controller b) Service c) Repository d) Entity  
✅ **Answer:** b)  
  
**Q4. JPA is:**  
a) An ORM tool b) A database c) A specification d) A JDBC replacement  
✅ **Answer:** c)  
  
**Q5. Which fetch type is default for @OneToMany?**  
a) EAGER b) LAZY c) BOTH d) NONE  
✅ **Answer:** b)  
  
**Q6. What is the main drawback of Page<T>?**  
a) No sorting b) No pagination c) Executes total count query d) Not thread-safe  
✅ **Answer:** c)  
  
**Q7. Which annotation enables global exception handling?**  
a) @ExceptionHandler b) @ControllerAdvice c) @ErrorHandler d) @TryCatch  
✅ **Answer:** b)  
  
**Q8. Which config approach is best for many related properties?**  
a) Hardcoding b) @Value c) @ConfigurationProperties d) Static constants  
✅ **Answer:** c)  
  
**Q9. What’s wrong here?**  
```
@RestController
public class NoteController {

    @Autowired
    private NoteRepository repository;
}

```
✅ **Answer:** Controller should depend on **service**, not repository. Field injection is also discouraged.  
  
**Q10. What happens if ddl-auto=update is used in production?**  
✅ **Answer:** Risky. Can silently change schema and cause data issues. Not recommended for production.  
##   
——————————————————————————————————————  
##   
## Day 15 Interview Q&A  
**Q1. What is JWT?**  
A stateless, signed token containing user info.  
  
**Q2. Why disable CSRF for APIs?**  
CSRF applies to browser-based sessions, not stateless APIs.  
  
**Q3. Where does Spring Security sit?**  
Before controllers, in filter chain.  
  
**Q4. Why token-based auth is scalable?**  
Server does not store session state.  
  
## 📝 Day 15 MCQs  
**Q1. What does authentication verify?**  
a) Permissions b) Roles c) Identity d) Access  
✅ **Answer:** c)  
  
**Q2. JWT is:**  
a) Encrypted b) Signed c) Hashed d) Compressed  
✅ **Answer:** b)  
  
**Q3. Where is JWT sent?**  
a) Request body b) Cookie c) Authorization header d) URL parameter  
✅ **Answer:** c)  
  
**Q4. Recommended password encoder?**  
a) MD5 b) SHA1 c) BCrypt d) Plain text  
✅ **Answer:** c)  
——————————————————————————————————————  
##   
## Day 16 Interview Q&A  
  
**Q1. Where is JWT validated?**  
In a filter before controller.  
  
**Q2. How does Spring know user roles?**  
From GrantedAuthority list.  
  
**Q3. Can JWT be revoked?**  
Not easily → needs blacklist or short expiry.  
  
**Q4. Why JWT is stateless?**  
Server stores no session data.  
  
## 📝 Day 16 MCQs  
**Q1. JWT validation happens in?**  
a) Controller b) Service c) Filter d) Repository  
✅ **Answer:** c)  
  
**Q2. Which annotation restricts method access?**  
a) @Secured b) @PreAuthorize c) @Authorize d) @RoleCheck  
✅ **Answer:** b)  
  
**Q3. Correct authority format?**  
a) ADMIN b) ROLEADMIN c) ROLE_ADMIN d) ADMIN_ROLE  
✅ **Answer:** c)  
  
**Q4. JWT expiry is important because?**  
a) Reduces DB load b) Prevents replay attacks c) Improves performance d) Enables caching  
✅ **Answer:** b)  
##   
——————————————————————————————————————  
##   
## Day 17 Interview Q&A  
**Q1. Difference between @Async and threads?**  
Spring manages thread pool and lifecycle.  
  
**Q2. Can @Async work on private methods?**  
❌ No. Must be public.  
  
**Q3. Why async event listeners?**  
Avoid blocking main flow.  
  
**Q4. fixedRate vs fixedDelay?**  
* fixedRate → start time based  
* fixedDelay → end time based  
  
## 📝 Day 17 MCQs  
**Q1. What does @Async do?**  
a) Creates new JVM b) Runs method in background thread c) Schedules job d) Enables security  
✅ **Answer:** b)  
  
**Q2. Which annotation enables scheduling?**  
a) @EnableAsync b) @EnableJobs c) @EnableScheduling d) @EnableTasks  
✅ **Answer:** c)  
  
**Q3. Which return type is NOT valid for @Async?**  
a) void b) Future c) CompletableFuture d) Optional  
✅ **Answer:** d)  
  
**Q4. Which improves loose coupling?**  
a) Direct method call b) Events c) Static methods d) Global variables  
✅ **Answer:** b)  
  
——————————————————————————————————————  
##   
## Day 18 Interview Q&A  
  
**Q1. Why use caching?**  
Reduce DB load and improve performance.  
  
**Q2. @Cacheable vs @CachePut?**  
* Cacheable → may skip method  
* CachePut → always executes  
  
**Q3. When should data NOT be cached?**  
Frequently changing data.  
  
## 📝 Day 18 MCQs  
**Q1. What does value in @Cacheable represent?**  
a) Method name b) Cache key c) Cache name d) Entity name  
✅ **Answer:** c)  
  
**Q2. What happens on cache hit?**  
a) DB query runs b) Method runs c) Method is skipped d) Cache is cleared  
✅ **Answer:** c)  
  
**Q3. Which cache works across multiple instances?**  
a) HashMap b) Caffeine c) Ehcache d) Redis  
✅ **Answer:** d)  
  
**Q4. Why is cache eviction important?**  
a) Improves speed b) Prevents stale data c) Reduces memory usage d) All of the above  
✅ **Answer:** d)  
##   
——————————————————————————————————————  
##   
## Day 19 Interview Q&A  
**Q1. Why mock dependencies?**  
To isolate the class under test.  
  
**Q2. Difference between @Mock and @MockBean?**  
* @Mock → pure unit tests  
* @MockBean → Spring context tests  
  
**Q3. What is Arrange–Act–Assert?**  
* Setup  
* Execute  
* Verify  
  
**Q4. Should services be tested with DB?**  
No. That’s integration testing.  
  
## 📝 Day 19 MCQs  
**Q1. What does Mockito do?**  
a) Runs tests b) Creates fake objects c) Starts Spring context d) Manages DB  
✅ **Answer:** b)  
  
**Q2. Which annotation injects mocks into class under test?**  
a) @Mock b) @Autowired c) @InjectMocks d) @Spy  
✅ **Answer:** c)  
  
**Q3. Which test should NOT use real DB?**  
a) Unit test b) Integration test c) System test d) UAT  
✅ **Answer:** a)  
  
**Q4. Purpose of @WebMvcTest?**  
a) Load full app b) Test repositories c) Test controller layer d) Test services  
✅ **Answer:** c)  
  
——————————————————————————————————————  
##   
## Day 20 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 21 Interview Q&A  
**Q1. What is the main advantage of JWT over sessions?**  
a) Faster DB queries b) Stateless authentication c) Automatic encryption d) Smaller payload  
✅ **Answer:** b)  
  
**Q2. Where should JWT validation happen?**  
a) Controller b) Service c) Filter d) Repository  
✅ **Answer:** c)  
  
**Q3. Why is @Async preferred over manually creating threads?**  
a) Threads are faster b) Spring manages thread pool & lifecycle c) JVM requires it d) It avoids exceptions  
✅ **Answer:** b)  
  
**Q4. What problem does caching mainly solve?**  
a) Security b) Concurrency c) Performance & scalability d) Memory leaks  
✅ **Answer:** c)  
  
**Q5. Why is cache eviction important?**  
a) Saves CPU b) Prevents stale data c) Improves GC d) Avoids deadlocks  
✅ **Answer:** b)  
  
**Q6. Which test uses real DB and real beans?**  
a) Unit test b) Mock test c) Integration test d) Controller test  
✅ **Answer:** c)  
  
**Q7. Why is Testcontainers preferred in CI?**  
a) Faster than unit tests b) Uses in-memory DB c) Provides consistent, isolated dependencies d) Avoids Docker  
✅ **Answer:** c)  
  
## 💻 Code Reasoning (High-Signal Interview Questions)  
**Q8. What’s wrong here?**  
```
@Async
private void sendEmail() {
    // logic
}

```
✅ **Answer:** @Async does **not work on private methods**. It must be public because Spring uses proxies.  
  
**Q9. Why is this dangerous?**  
```
@Cacheable("users")
public User getUser(Long id) {
    return repo.findById(id).get();
}

```
✅ **Answer:**  
* No eviction strategy  
* Stale data risk  
* .get() may throw exception  
  
**Q10. Why is this a bad unit test?**  
```
@SpringBootTest
class UserServiceTest {
}

```
✅ **Answer:** Loads full Spring context → **slow**. Unit tests should not start the container.  
  
——————————————————————————————————————  
  
## Day 22 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 23 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 24 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 25 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 26 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 27 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 28 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 29 Interview Q&A  
  
——————————————————————————————————————  
  
## Day 30 Interview Q&A  
  
——————————————————————————————————————  
