# 📕 Day 31 - Java  
  
# 🔹 1. What is a final class in Java?  
A **final class** is a class that **cannot be inherited**.  
```
final class Utility {
    void log() {
        System.out.println("Logging");
    }
}

// class Test extends Utility {}  ❌ Compile-time error

```
  
**Why do we use final classes?**  
1. **Security**  
    * Prevents malicious subclassing  
    * Example: String, Integer are final  
2. **Immutability**  
    * No subclass can change behavior  
3. **Design control**  
    * Ensures behavior remains consistent  
  
**Common interview follow-ups**  
❓ Can a final class have methods overridden? 👉 No, because it cannot be inherited.  
  
❓ Can a final class have variables and methods? 👉 Yes, completely normal class — just no inheritance.  
  
# 🔹 2. Java 8 Functional Interfaces & Functions  
# What is a Functional Interface?  
An interface with **exactly ONE abstract method**.  
```

@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}

```
**Why Java 8 introduced this?**  
To support:  
* Lambdas  
* Streams  
* Functional programming  
  
## Interview trap  
❓ Can functional interface have default methods? 👉 Yes. Only **abstract methods count**.  
  
  
# 🔹 3. Interface vs Abstract Class (DEEP)  
## Interface  
* Represents **capability / contract**  
* Supports **multiple inheritance**  
* No state  
```

interface Flyable {
    void fly(); // public abstract by default
}

```
  
## Abstract Class  
* Represents **base identity**  
* Can have **state + constructor**  
* Only single inheritance  
```

abstract class Vehicle {
    int speed;

    abstract void move();

    void stop() {
        System.out.println("Stopped");
    }
}

```
  
## Key Differences (Interview Table)  

| Feature              | Interface  | Abstract Class |
| -------------------- | ---------- | -------------- |
| Multiple inheritance | ✅ Yes      | ❌ No           |
| Constructor          | ❌ No       | ✅ Yes          |
| Instance variables   | ❌ No       | ✅ Yes          |
| Use case             | Capability | Base behavior  |
| Spring preference    | ✅ More     | Less           |
  
## Interview one-liner  
👉 Use **interface** when behavior is shared across unrelated classes. 👉 Use **abstract class** when classes are closely related and share state.  
  
# 🔹 4. Marker Interface (VERY COMMON TRAP)  
## What is a Marker Interface?  
An interface with **NO methods**.  
```

interface Serializable {
}

```
**Why does it exist?**  
It **marks** a class and JVM / framework checks it using instanceof.  
  
## Example  
```

class User implements Serializable {
}

```
JVM behavior:  
* If object is Serializable → allow serialization  
* Else → throw exception  
  
## Famous Marker Interfaces  
* Serializable  
* Cloneable  
* RandomAccess  
  
## Interview follow-up  
❓ How does JVM detect marker interface? 👉 Using instanceof check at runtime.  
  
# 🔹 5. How to Make an Object Immutable (VERY IMPORTANT)  
**Rules (MUST MEMORIZE)**  
1. Class must be final  
2. Fields must be private final  
3. No setters  
4. Defensive copy for mutable fields  
  
# Correct Immutable Class Example  
```
final class Employee {

    private final int id;
    private final String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() { return id; }
    public String getName() { return name; }
}

```
  
**With mutable field**  
```

final class Dept {

    private final List<String> teams;

    Dept(List<String> teams) {
        this.teams = new ArrayList<>(teams); // defensive copy
    }

    public List<String> getTeams() {
        return new ArrayList<>(teams); // defensive copy
    }
}

```
  
# 🔹 7. MVC Architecture (VERY IMPORTANT)  
## What is MVC?  
**Model – View – Controller**  
  
**Controller**  
Handles HTTP request  
```

@RestController
class UserController {
    @GetMapping("/users")
    List<User> getUsers() {}
}

```
  
**Model**  
Represents data  
```

class User {
    int id;
    String name;
}

```
  
**View**  
UI (Thymeleaf / JSON / JSP)  
  
## Flow  
```

Client → Controller → Service → Repository → DB

```
  
## Interview trap  
❓ Is Service part of MVC? 👉 No. Service is **business layer**, not MVC.  
  
# 🔹 8. finalize() vs finally vs final  
## finally  
Used in exception handling.  
```

try {
   int x = 10 / 2;
} finally {
   System.out.println("Always runs");
}

```
  
## finalize()  
* Called by Garbage Collector  
* Deprecated  
* Not guaranteed  
```

protected void finalize() {
   System.out.println("Cleanup");
}

```
  
## final  
Used for:  
* Variables → constant  
* Methods → cannot override  
* Class → cannot inherit  
  
## Interview table  

| Keyword  | Purpose                 |
| -------- | ----------------------- |
| final    | Restriction             |
| finally  | Exception cleanup       |
| finalize | GC cleanup (deprecated) |
  
# 🔹 9. What is a default class?  
This is often **mis-asked**.  
**They mean:**  
👉 **Class without access modifier**  
```

class Test {
}

```
**Scope**  
Accessible **only within same package**.  
  
## Interview follow-up  
❓ Can top-level class be private? 👉 ❌ No.  
  
  
# 🧠 JVM MEMORY MODEL (MOST IMPORTANT CORE JAVA TOPIC)  
Interviewers ask JVM questions to check:  
* Do you understand **how Java actually runs**  
* Do you know **where objects live**  
* Do you understand **performance & GC**  
  
## 🔹 What is JVM?  
**JVM (Java Virtual Machine)** is the engine that:  
* Executes Java bytecode  
* Manages memory  
* Handles garbage collection  
* Ensures platform independence  
Java flow:  
```

.java → javac → .class (bytecode) → JVM → OS

```
  
# 🔹 JVM MEMORY AREAS (THIS IS CRITICAL)  
JVM memory is divided into **runtime areas**.  
```

JVM Memory
├── Method Area (Metaspace)
├── Heap
│   ├── Young Generation
│   │   ├── Eden
│   │   ├── Survivor S0
│   │   └── Survivor S1
│   └── Old Generation
├── Stack
├── PC Register
└── Native Method Stack

```
  
## 1️⃣ STACK MEMORY  
**What lives in Stack?**  
* Method calls  
* Local variables  
* Reference variables  
**Characteristics**  
* Thread-specific  
* LIFO  
* Very fast  
* Automatically freed  
**Example**  
```

void test() {
    int x = 10;        // stack
    User u = new User(); // reference in stack, object in heap
}

```
**Interview trap**  
❓ Is Stack shared between threads? 👉 ❌ No. Each thread has its own stack.  
  
## 2️⃣ HEAP MEMORY  
**What lives in Heap?**  
* Objects  
* Instance variables  
**Characteristics**  
* Shared across threads  
* Managed by GC  
* Slower than stack  
**Example**  
```

User u = new User();

```
Object → Heap Reference u → Stack  
  
## Heap Structure (VERY IMPORTANT)  
**🔹 Young Generation**  
* New objects  
* Short-lived  
**Eden**  
* All objects created here first  
**Survivor (S0, S1)**  
* Objects that survive GC move here  
  
**🔹 Old Generation**  
* Long-lived objects  
* Objects promoted from Young Gen  
  
**Interview question**  
❓ Where are String literals stored? 👉 **String Pool (inside Heap)**  
  
## 3️⃣ METHOD AREA (METASPACE)  
**Stores:**  
* Class metadata  
* Method bytecode  
* Static variables  
* Constant pool  
Since Java 8: 👉 PermGen removed 👉 Metaspace introduced (uses native memory)  
  
## 4️⃣ PC REGISTER  
* Holds address of current executing instruction  
* Thread-specific  
  
## 5️⃣ NATIVE METHOD STACK  
* Used for native (C/C++) calls  
  
# 🔥 OBJECT CREATION FLOW (VERY IMPORTANT)  
```

User u = new User();

```
Steps:  
1. Class loaded into Method Area  
2. Memory allocated in Heap  
3. Constructor executed  
4. Reference stored in Stack  
  
# 🔹 GARBAGE COLLECTION (GC)  
## What is Garbage Collection?  
👉 Automatic removal of **unused objects** from Heap.  
Object becomes eligible for GC when:  
* No live references point to it  
```

User u = new User();
u = null;   // eligible for GC

```
  
## Types of GC  
**1️⃣ Minor GC**  
* Cleans Young Generation  
* Fast  
* Happens frequently  
  
**2️⃣ Major GC (Full GC)**  
* Cleans Old Generation  
* Expensive  
* Causes application pause  
  
## GC Algorithms (INTERVIEW LEVEL)  
* Serial GC  
* Parallel GC  
* G1 GC (default in modern Java)  
* ZGC / Shenandoah (low latency)  
You **don’t need deep internals**, just awareness.  
  
## Interview trap  
❓ Is GC guaranteed to run immediately? 👉 ❌ No. JVM decides.  
  
# 🔹 finalize() (CONNECTED TO GC)  
```
protected void finalize() {
    System.out.println("cleanup");
}

```
**Truth:**  
* Called before GC  
* Not guaranteed  
* Deprecated  
* DO NOT use in real apps  
Interview answer: 👉 finalize is unreliable and deprecated.  
  
# 🔹 MEMORY LEAK IN JAVA (VERY IMPORTANT)  
Java has GC — **still memory leaks happen**.  
Example:  
```

static List<User> users = new ArrayList<>();

void add() {
    users.add(new User());
}

```
Objects are still referenced → GC cannot clean.  
  
## Interview question  
❓ Can Java have memory leak? 👉 ✅ Yes, due to lingering references.  
  
# 🔹 OutOfMemoryError vs StackOverflowError  
**StackOverflowError**  
* Too deep recursion  
* Stack exhausted  
```

void recurse() {
    recurse();
}

```
  
**OutOfMemoryError**  
* Heap full  
* Too many objects  
  
# 🧠 ONE-LINE INTERVIEW ANSWERS (MEMORIZE)  
* Stack → method calls & local variables  
* Heap → objects & instance variables  
* Metaspace → class metadata  
* GC → automatic heap cleanup  
* finalize → GC hook, deprecated  
* Memory leak → object still referenced  
