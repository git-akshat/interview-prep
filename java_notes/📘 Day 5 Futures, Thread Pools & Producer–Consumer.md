# 📘 Day 5: Futures, Thread Pools & Producer–Consumer  
  
## 1. Problem with Runnable  
❌ Cannot return a value  
❌ Cannot throw checked exceptions  
That’s where **Callable + Future** come in.  
  
## 2. Callable & Future  
**Callable**  
* Similar to Runnable  
* Returns a value  
* Can throw checked exceptions  
```
import java.util.concurrent.Callable;

Callable<Integer> task = () -> {
    return 10 + 20;
};

```
  
**Future**  
* Represents the **result of an async computation**  
* Returned when you submit a Callable to ExecutorService  
```
import java.util.concurrent.*;

public class Test {
    public static void main(String[] args) throws Exception {

        ExecutorService service =
                Executors.newSingleThreadExecutor();

        Future<Integer> future = service.submit(() -> {
            Thread.sleep(1000);
            return 42;
        });

        System.out.println("Doing other work...");

        Integer result = future.get(); // blocks until result available
        System.out.println("Result = " + result);

        service.shutdown();
    }
}

```
Key points:  
* get() → **blocking call**  
* isDone() → check status  
* cancel() → attempt cancellation  
👉 Interview line:  
Future represents a placeholder for a value that will be available later.  
  
## 3. Thread Pools   
**Why thread pools?**  
Creating threads is expensive:  
* Memory  
* Context switching  
* OS overhead  
Thread pools:  
* Reuse threads  
* Control concurrency  
* Prevent system overload  
  
**Common Pool Types**  
```
Executors.newFixedThreadPool(n);
Executors.newCachedThreadPool();
Executors.newSingleThreadExecutor();

```
Interview tip:  
* Avoid newCachedThreadPool() in production (can create too many threads)  
  
## 4. BlockingQueue (Core Concurrency Tool)  
Used for **Producer–Consumer** problems.  
Properties:  
* Thread-safe  
* Handles waiting internally  
* No need for manual wait() / notify()  
Common implementations:  
* ArrayBlockingQueue  
* LinkedBlockingQueue  
  
## 5. Producer–Consumer Problem  
**Without BlockingQueue (Hard & Buggy)**  
Requires:  
* synchronized  
* wait()  
* notify()  
**With BlockingQueue (Clean & Safe)**  
  
**Producer–Consumer Example**  
```

import java.util.concurrent.*;

class Producer implements Runnable {
    private BlockingQueue<Integer> queue;

    Producer(BlockingQueue<Integer> queue) {
        this.queue = queue;
    }

    public void run() {
        try {
            for (int i = 1; i <= 5; i++) {
                queue.put(i); // blocks if full
                System.out.println("Produced: " + i);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

class Consumer implements Runnable {
    private BlockingQueue<Integer> queue;

    Consumer(BlockingQueue<Integer>> queue) {
        this.queue = queue;
    }

    public void run() {
        try {
            while (true) {
                Integer value = queue.take(); // blocks if empty
                System.out.println("Consumed: " + value);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

public class Test {
    public static void main(String[] args) {

        BlockingQueue<Integer> queue =
                new ArrayBlockingQueue<>(3);

        ExecutorService service =
                Executors.newFixedThreadPool(2);

        service.submit(new Producer(queue));
        service.submit(new Consumer(queue));

        service.shutdown();
    }
}

```
Key understanding:  
* put() blocks when queue is full  
* take() blocks when queue is empty  
* No explicit synchronization needed  
  
## 6. Deadlock vs Starvation vs Livelock  
**Deadlock**  
* Threads wait forever for each other  
**Starvation**  
* Thread never gets CPU or lock  
**Livelock**  
* Threads keep reacting to each other but make no progress  
  
## 7. Project Task – Day 5  
**🔨 Multithreaded File Processor**  
**Scenario:** Process multiple files in parallel and count words.  
**Requirements:**  
* Use ExecutorService  
* Each file processed by one Callable  
* Return word count per file  
* Collect results using Future  
Skeleton:  
```
Callable<Integer> task = () -> {
    // read file
    // count words
    return count;
};

```
Extensions:  
* Use thread pool of size = number of cores  
* Handle exceptions properly  
* Print total word count  
