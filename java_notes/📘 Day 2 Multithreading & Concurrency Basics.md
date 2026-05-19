# 📘 Day 2: Multithreading & Concurrency Basics  
  
## 1. What is a Thread?  
* A **thread** is the smallest unit of execution in Java.  
* Every Java program starts with **one thread** → the main thread.  
* Multiple threads allow tasks to run **concurrently**.  
* Threads **share heap memory** but each thread has its **own stack**.  
  
👉 Interview line:  
A process can have multiple threads sharing the same memory space.  
  
## 2. Ways to Create a Thread  
**a) Extending Thread**  
```
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running: " +
                Thread.currentThread().getName());
    }
}

public class Test {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start(); // creates a new thread
    }
}

```
Key points:  
* start() → creates a **new OS-level thread**  
* run() → contains the task logic  
* Calling run() directly ≠ multithreading  
  
**b) Implementing Runnable (Preferred)**  
```
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable running: " +
                Thread.currentThread().getName());
    }
}

public class Test {
    public static void main(String[] args) {
        Thread t = new Thread(new MyRunnable());
        t.start();
    }
}

```
Why Runnable is better:  
* Java supports **single inheritance**  
* Separates **task** from **thread**  
* More flexible and reusable  
  
**c) Using Lambda (Modern Java)**  
```
public class Test {
    public static void main(String[] args) {
        Thread t = new Thread(() ->
                System.out.println("Lambda Thread"));
        t.start();
    }
}

```
This is just Runnable + lambda syntax.  
  
## 3. Thread Lifecycle  
A thread goes through these states:  
* **NEW** → created, not started  
* **RUNNABLE** → ready to run (waiting for CPU)  
* **RUNNING** → executing  
* **WAITING / TIMED_WAITING** → paused (wait(), sleep(), join())  
* **TERMINATED** → execution completed  
👉 Interview tip: Execution order between threads is **never guaranteed**.  
  
## 4. Executors (Better Than Raw Threads)  
ExecutorService manages threads for you using thread pools, while traditional multithreading requires manual thread handling. ThreadPoolExecutor is the core class that controls how threads are created, reused, and queued.  
```
import java.util.concurrent.*;

public class Test {
    public static void main(String[] args) {

        ExecutorService service =
                Executors.newFixedThreadPool(2);

        service.submit(() -> System.out.println("Task 1"));
        service.submit(() -> System.out.println("Task 2"));

        service.shutdown();
    }
}

```
Common executors:  
* **newFixedThreadPool(n)** → fixed number of reusable threads  
* **newCachedThreadPool()** → creates threads as needed  
* **newSingleThreadExecutor()** → single-thread execution  
👉 Interview line:  
Executors manage thread lifecycle and prevent uncontrolled thread creation.  
  
## ✅ What is CompletableFuture?  
**CompletableFuture** is a Java class used for **asynchronous, non-blocking programming**. It lets you run tasks in the background and **chain multiple operations** without blocking the main thread.  
In simple words:  
It helps you write async code in a clean, readable way.  
  
## ✅ Why do we need it?  
Before CompletableFuture:  
* You had Future.get() → **blocks**  
* Hard to combine async tasks  
* No proper callbacks  
CompletableFuture fixes this by providing:  
* **Callbacks**  
* **Chaining**  
* **Exception handling**  
* **Parallel execution**  
  
## ✅ Simple example  
```
CompletableFuture
    .supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenAccept(System.out::println);

```
Runs asynchronously and prints:  
```
Hello World

```
No blocking.  
  
## ✅ Common methods (interview favorites)  
* supplyAsync() → returns value  
* runAsync() → no return  
* thenApply() → transform result  
* thenAccept() → consume result  
* thenCompose() → chain async calls  
* thenCombine() → combine two futures  
* exceptionally() → handle errors  
* allOf() / anyOf() → multiple futures  
  
## ✅ CompletableFuture vs ExecutorService  
ExecutorService:  
* Runs tasks  
* Returns Future  
* Blocking get()  
CompletableFuture:  
* Async callbacks  
* Functional chaining  
* Non-blocking  
* Much cleaner async flow  
  
  
## 5. Synchronization  
When multiple threads access **shared mutable data**, race conditions occur.  
  
**Example: Counter WITHOUT synchronization (Bug)**  
```
class Counter {
    private int count = 0;

    public void increment() {
        count++; // not atomic
    }

    public int getCount() {
        return count;
    }
}

```
count++ = read → modify → write Multiple threads can interleave here.  
  
**Thread-safe Counter using synchronized**  
```
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Test {
    public static void main(String[] args)
            throws InterruptedException {

        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++)
                counter.increment();
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++)
                counter.increment();
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final count = " +
                counter.getCount());
    }
}

```
  
## 6. What does join() do?  
```
t1.start();
t2.start();
t1.join();
t2.join();

```
Meaning:  
* join() blocks the **current thread** (main)  
* Waits until the specified thread finishes execution  
Without join():  
* Main thread may print result **before child threads finish**  
👉 Think of join() as:  
“Pause here until this thread completes.”  
  
## 7. Common Interview Questions (Day 2)  
**1. Runnable vs Callable**  
* Runnable → no return value, no checked exceptions  
* Callable → returns value (Future<V>), can throw checked exceptions  
  
**2. start() vs run()**  
* start() → creates a new thread  
* run() → runs in current thread  
  
**3. What happens if run() is called directly?**  
* No new thread  
* Code runs in main thread only  
  
**4. What is a race condition?**  
When multiple threads modify shared data **without synchronization**, causing inconsistent results.  
  
  
  
## 9. Project Task – Day 2  
🔨** Multithreaded Log Processor**  
**Scenario:** Multiple threads process logs from different sources.  
**Requirements:**  
* Use ExecutorService with fixed thread pool  
* Each thread simulates a log source (DB, API, UI)  
* Logs stored in a **shared, thread-safe collection**  
Starter code (same as before):  
```
import java.util.*;
import java.util.concurrent.*;

public class LogProcessor {

    private static List<String> logs =
            Collections.synchronizedList(new ArrayList<>());

    public static void main(String[] args)
            throws InterruptedException {

        ExecutorService service =
                Executors.newFixedThreadPool(3);

        Runnable dbLogger = () -> {
            for (int i = 0; i < 5; i++) {
                logs.add("DB Log " + i + " from " +
                        Thread.currentThread().getName());
            }
        };

        Runnable apiLogger = () -> {
            for (int i = 0; i < 5; i++) {
                logs.add("API Log " + i + " from " +
                        Thread.currentThread().getName());
            }
        };

        Runnable uiLogger = () -> {
            for (int i = 0; i < 5; i++) {
                logs.add("UI Log " + i + " from " +
                        Thread.currentThread().getName());
            }
        };

        service.submit(dbLogger);
        service.submit(apiLogger);
        service.submit(uiLogger);

        service.shutdown();
        service.awaitTermination(5, TimeUnit.SECONDS);

        System.out.println("Final Logs:");
        logs.forEach(System.out::println);
    }
}

```
