# 📘 Day 4: Threads Deep Dive – Locks, Volatile, Atomic Variables  
  
## 1. Problem with Basic Synchronization  
You already saw synchronized works, but:  
* It **blocks** other threads completely  
* Can reduce performance  
* Can lead to **deadlocks** if misused  
So Java provides **better concurrency tools**.  
  
## 2. synchronized vs Lock  
**synchronized**  
* Implicit lock (monitor)  
* Automatically acquired and released  
* Simpler, but less flexible  
```
public synchronized void increment() {
    count++;
}

```
  
**Lock (java.util.concurrent.locks.Lock)**  
```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Counter {
    private int count = 0;
    private Lock lock = new ReentrantLock();

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}

```
Advantages of Lock:  
* Can try to acquire lock (tryLock())  
* Can interrupt while waiting  
* More control than synchronized  
👉 Interview line:  
Lock provides more flexibility than synchronized, but must be released manually.  
  
## 3. Deadlock (Very Important)  
When two or more threads wait forever for each other’s resources.  
  
**Deadlock Example**  
```
class DeadlockExample {
    static final Object lock1 = new Object();
    static final Object lock2 = new Object();

    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            synchronized (lock1) {
                synchronized (lock2) {
                    System.out.println("Thread 1");
                }
            }
        });

        Thread t2 = new Thread(() -> {
            synchronized (lock2) {
                synchronized (lock1) {
                    System.out.println("Thread 2");
                }
            }
        });

        t1.start();
        t2.start();
    }
}

```
Both threads:  
* Hold one lock  
* Wait for the other → **deadlock**  
  
**How to Prevent Deadlock **  
* Acquire locks in **same order**  
* Use tryLock()  
* Avoid nested locks  
* Use timeouts  
  
## 4. Volatile Keyword (Frequently Asked)  
**What problem does volatile solve?**  
* **Visibility**, not atomicity  
* Ensures changes made by one thread are visible to others  
  
**Example Without volatile**  
```
class Flag {
    boolean running = true;
}

```
One thread may never see updates to running.  
  
**With volatile**  
```
class Flag {
    volatile boolean running = true;
}

```
Now:  
* Reads always from **main memory**  
* Writes flushed immediately  
👉 Important:  
* volatile does **NOT** make operations atomic  
* count++ is still unsafe  
  
**Interview Line**  
volatile guarantees visibility, not mutual exclusion.  
  
## 5. Atomic Variables  
Java provides atomic classes for lock-free thread safety.  
Common ones:  
* AtomicInteger  
* AtomicLong  
* AtomicBoolean  
  
**Example**  
```
import java.util.concurrent.atomic.AtomicInteger;

class Counter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }

    public int getCount() {
        return count.get();
    }
}

```
Advantages:  
* Lock-free  
* Faster than synchronized  
* Uses **CAS (Compare-And-Swap)**  
  
## 6. synchronized vs volatile vs Atomic (Compare)  

| Feature     | synchronized | volatile | Atomic |
| ----------- | ------------ | -------- | ------ |
| Visibility  | ✅            | ✅        | ✅      |
| Atomicity   | ✅            | ❌        | ✅      |
| Blocking    | Yes          | No       | No     |
| Performance | Slower       | Fast     | Fast   |
  
👉 Interview rule:  
* Visibility only → volatile  
* Simple atomic ops → Atomic  
* Complex critical section → synchronized / Lock  
  
  
## 7 . Project Task – Day 4  
**🔨 Thread-Safe Counter Comparison**  
Implement 3 counters:  
1. Using synchronized  
2. Using AtomicInteger  
3. Using volatile  
Run with multiple threads:  
* Measure final count  
* Observe correctness and performance  
👉 Goal:  
* See visibility vs atomicity difference **in practice**  
