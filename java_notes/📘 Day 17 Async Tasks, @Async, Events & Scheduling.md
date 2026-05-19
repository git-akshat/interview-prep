# 📘 Day 17: Async Tasks, @Async, Events & Scheduling  
  
## 1. Why Asynchronous Processing Matters  
Synchronous processing:  
* Request waits until task completes  
* Slow APIs  
* Poor user experience  
Asynchronous processing:  
* Return response immediately  
* Work happens in background  
* Better throughput  
👉 Interview line:  
Async processing improves responsiveness and scalability.  
  
## 2. @Async (Simple Async Execution)  
Spring provides @Async to run methods asynchronously.  
  
**Enable Async Support**  
```
@EnableAsync

```
  
**Async Method Example**  
```
@Service
public class EmailService {

    @Async
    public void sendEmail(String to) {
        try {
            Thread.sleep(3000);
            System.out.println("Email sent to " + to);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

```
Calling this method:  
* Returns immediately  
* Executes in separate thread  
👉 Interview line:  
@Async methods run in a separate thread pool.  
  
## 3. Async Return Types  
Async methods can return:  
* void  
* Future<T>  
* CompletableFuture<T>  
Example:  
```
@Async
public CompletableFuture<String> process() {
    return CompletableFuture.completedFuture("Done");
}

```
  
## 4. Thread Pool for @Async  
By default:  
* Spring uses SimpleAsyncTaskExecutor  
* Not ideal for production  
Define your own executor:  
```
@Configuration
public class AsyncConfig {

    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor =
                new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.initialize();
        return executor;
    }
}

```
👉 Interview line:  
Always configure a custom thread pool for async tasks.  
  
## 5. Spring Events (Loose Coupling)  
Events allow:  
* Decoupled communication  
* One event → many listeners  
  
**Custom Event**  
```
public class UserCreatedEvent {
    private final String username;

    public UserCreatedEvent(String username) {
        this.username = username;
    }

    public String getUsername() {
        return username;
    }
}

```
  
**Publish Event**  
```
@Component
public class UserService {

    private final ApplicationEventPublisher publisher;

    public UserService(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    public void createUser(String username) {
        publisher.publishEvent(
                new UserCreatedEvent(username));
    }
}

```
  
**Event Listener**  
```
@Component
public class WelcomeEmailListener {

    @EventListener
    public void handle(UserCreatedEvent event) {
        System.out.println(
            "Send welcome email to " +
             event.getUsername());
    }
}

```
  
## 6. Async Event Listener  
```
@Async
@EventListener
public void handle(UserCreatedEvent event) {
    // runs asynchronously
}

```
👉 Interview line:  
Events improve decoupling; async events improve performance.  
  
## 7. Scheduling Tasks  
Used for:  
* Cleanup jobs  
* Report generation  
* Reminders  
  
**Enable Scheduling**  
```
@EnableScheduling

```
  
**Scheduled Method**  
```
@Scheduled(fixedRate = 5000)
public void runTask() {
    System.out.println("Task running every 5 sec");
}

```
Types:  
* fixedRate  
* fixedDelay  
* cron  
  
**Cron Example**  
```
@Scheduled(cron = "0 0 0 * * ?")
public void dailyJob() {
    System.out.println("Runs at midnight");
}

```
  
## 🔨 Project Task – Day 17  
**Background Email Notification**  
Scenario:  
* When a note is created  
* Send email notification in background  
Steps:  
1. Create NoteCreatedEvent  
2. Publish event after note creation  
3. Create async listener  
4. Simulate email sending  
5. Ensure API responds immediately  
  
**✅ What is CompletableFuture?**  
**Interview definition:**  
**CompletableFuture represents a future result of an asynchronous computation and allows you to attach callbacks, combine tasks, and handle errors without blocking.**  
Old way (Future) → blocking New way (CompletableFuture) → non-blocking + composable  
  
**🔥 Simple Example**  
  
CompletableFuture<String> future =  
    CompletableFuture.supplyAsync(() -> "Hello");  
  
System.out.println(future.get());   // blocks only here  
  
**❌ Problem with Old Future**  
  
Future<String> f = executor.submit(task);  
String result = f.get();   // BLOCKS  
You must wait.  
  
**✅ CompletableFuture = CALLBACKS**  
You can say:  
👉 when done, do this 👉 then do that 👉 if error, handle it  
  
**⭐ Core Methods (VERY IMPORTANT)**  
  
**1️⃣ runAsync (no return)**  
  
```
CompletableFuture.runAsync(() ->
    System.out.println("Running async")
);

```
Returns CompletableFuture<Void>.  
  
**2️⃣ supplyAsync (returns value)**  
  
```
CompletableFuture<Integer> cf =
    CompletableFuture.supplyAsync(() -> 10);

```
  
**3️⃣ thenApply (transform result)**  
  
```
CompletableFuture<Integer> cf =
    CompletableFuture.supplyAsync(() -> 10)
                     .thenApply(x -> x * 2);

```
Result = 20  
  
**4️⃣ thenAccept (consume result)**  
  
```
cf.thenAccept(System.out::println);

```
No return.  
  
**5️⃣ thenRun (ignore result)**  
  
```
cf.thenRun(() -> System.out.println("Done"));

```
  
**🔥 thenApply vs thenAccept vs thenRun**  

| Method     | Input | Output |
| ---------- | ----- | ------ |
| thenApply  | yes   | yes    |
| thenAccept | yes   | no     |
| thenRun    | no    | no     |
  
Interview favorite.  
  
**✅ Chaining Example**  
  
```
CompletableFuture.supplyAsync(() -> 5)
    .thenApply(x -> x * 2)
    .thenApply(x -> x + 1)
    .thenAccept(System.out::println);

```
Output:  
  
```
11

```
  
**✅ Exception Handling**  
  
**exceptionally**  
```

future.exceptionally(ex -> {
    System.out.println(ex);
    return "fallback";
});

```
  
**handle (success + failure)**  
  
```
future.handle((res, ex) -> {
    if(ex != null) return "error";
    return res;
});

```
  
**✅ Combining Futures**  
  
**thenCompose (flatMap)**  
Used when second future depends on first.  
  
```
cf.thenCompose(id -> getUser(id));

```
  
**thenCombine (independent)**  
  
```
cf1.thenCombine(cf2, (a,b) -> a + b);

```
  
**✅ Run Multiple in Parallel**  
  
```
CompletableFuture.allOf(f1, f2, f3).join();

```
Waits for all.  
  
  
```
CompletableFuture.anyOf(f1, f2).join();

```
Waits for first.  
  
**🔥 Custom Thread Pool**  
  
```
Executor executor = Executors.newFixedThreadPool(5);

CompletableFuture.supplyAsync(() -> task(), executor);

```
  
**⚠️ Common Pitfalls**  
  
**❌ Blocking defeats purpose**  
  
```
future.get();   // avoid inside chain

```
  
**❌ Using shared mutable state**  
Parallel execution → race conditions.  
  
**🧠 Interview Questions (Rapid Fire)**  
  
**Q: Difference between Future and CompletableFuture?**  
Future blocks. CompletableFuture supports callbacks + chaining.  
  
**Q: thenApply vs thenCompose?**  
thenApply → wraps future thenCompose → flattens future  
  
**Q: Does CompletableFuture use threads?**  
Yes — ForkJoinPool by default.  
  
**Q: How to handle exception?**  
exceptionally(), handle()  
  
**Q: Can we run tasks in parallel?**  
Yes using allOf / supplyAsync  
  
**Q: Is it reactive?**  
No — async but not reactive.  
  
**🔥 Real Use Case (Microservices)**  
Calling multiple services:  
  
```
CompletableFuture<User> user = getUser();
CompletableFuture<Order> order = getOrder();
CompletableFuture.allOf(user, order).join();

```
Instead of sequential REST calls.  
  
