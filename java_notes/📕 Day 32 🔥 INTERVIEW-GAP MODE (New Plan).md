# 📕 Day 32: 🔥 INTERVIEW-GAP MODE (New Plan)  
  
Let me translate them into **depth areas** 👇  
**1️⃣ final — NOT “cannot inherit”**  
Interviewers go further:  
* Why String is final  
* final vs immutability  
* final method vs polymorphism  
* final with collections  
👉 This was NOT deeply covered in 30-day plans.  
  
**2️⃣ Interfaces — NOT syntax**  
They expect:  
* Why Spring prefers interfaces  
* default methods → diamond problem  
* functional vs marker interface difference  
* interface vs abstract class **design decision**  
👉 Most prep stops at tables. Interviews don’t.  
  
**3️⃣ Abstract class vs Interface — at DESIGN level**  
They ask:  
* When abstract class is a bad choice  
* Why interfaces scale better in microservices  
* Why Spring AOP needs interfaces / proxies  
  
**4️⃣ Marker Interface — JVM behavior**  
They don’t want definition. They want:  
* How JVM checks it  
* Why annotation replaced many marker interfaces  
* Serializable vs Externalizable  
  
**5️⃣ Immutability — REAL depth**  
They ask:  
* Why String is immutable  
* How immutability helps HashMap  
* Defensive copy mistakes  
* Why records exist  
  
**6️⃣ Spring singleton — TRICK question**  
They expect:  
* JVM singleton vs Spring singleton  
* Thread safety confusion  
* Prototype inside singleton problem  
  
**7️⃣ MVC — ARCHITECTURE clarity**  
They ask:  
* Why Service is NOT part of MVC  
* Why Controller should be thin  
* MVC vs layered architecture  
  
**8️⃣ finalize / default / final — TRAP WORDS**  
They intentionally mix:  
* final  
* finally  
* finalize  
* default method  
* default access modifier  
  
  
# 🔥 final KEYWORD — INTERVIEW-LEVEL DEPTH  
Interviewers ask “what is final?” But they **evaluate you** on how well you understand **behavior, guarantees, and limitations**.  
We’ll break it into **4 dimensions**:  
1. final class  
2. final method  
3. final variable (primitive vs reference)  
4. final + immutability + thread safety (BIG TRAP)  
  
## 1️⃣ final CLASS — NOT JUST “CANNOT BE INHERITED”  
**Correct definition (speakable):**  
A final class **prevents inheritance to guarantee behavior cannot be altered by subclassing**.  
```

final class PaymentValidator {
    boolean isValid(int amount) {
        return amount > 0;
    }
}

```
**Why interviewers care**  
They want to know **WHY Java core classes are final**.  
  
**🔥 Why String is final (VERY COMMON)**  
Interviewers expect **at least 3 reasons**:  
1. **Security**  
    * Prevents overriding equals(), hashCode()  
    * Important for HashMap keys, class loading, file paths  
2. **Immutability guarantee**  
    * Subclass could add setters → break immutability  
3. **Caching & performance**  
    * String pool relies on immutability  
👉 Saying only “String is immutable” is **NOT enough**.  
  
**Follow-up trap**  
❓ Can a final class implement interfaces? 👉 ✅ YES (inheritance ≠ implementation)  
  
## 2️⃣ final METHOD — POLYMORPHISM CONTROL  
**Definition:**  
A final method **cannot be overridden**, but **can be inherited**.  
```

class Parent {
    final void log() {
        System.out.println("Logging");
    }
}

class Child extends Parent {
    // void log() {} ❌ compile error
}

```
**Why this exists**  
To **lock critical behavior** while still allowing inheritance.  
**Real-world usage**  
* Security checks  
* Template methods  
* Framework internal logic  
  
**Interview trap**  
❓ Can a final method be overloaded? 👉 ✅ YES (overloading ≠ overriding)  
  
## 3️⃣ final VARIABLE — THIS IS WHERE MOST PEOPLE FAIL  
**🔹 Case 1: final primitive**  
```

final int x = 10;
// x = 20; ❌

```
👉 Value cannot change.  
  
**🔹 Case 2: final reference (VERY IMPORTANT)**  
```

final List<String> list = new ArrayList<>();
list.add("Java");      // ✅ allowed
list = new ArrayList<>(); // ❌ NOT allowed

```
**Key rule (memorize this sentence):**  
final prevents reassignment of the reference, **not mutation of the object**.  
This single line clears **50% of interview confusion**.  
  
**Interview trap**  
❓ Is final List immutable? 👉 ❌ NO  
You must explicitly say:  
final does NOT mean immutable.  
  
## 4️⃣ final ≠ IMMUTABILITY (CRITICAL DISTINCTION)  
Interviewers love this trap.  
**❌ WRONG thinking:**  
“final class means immutable”  
**✅ Correct thinking:**  
Immutability requires:  
1. final class  
2. private final fields  
3. no setters  
4. defensive copies  
  
**Example: NOT immutable**  
```

final class User {
    private final List<String> roles;

    User(List<String> roles) {
        this.roles = roles;
    }
}

```
Caller can still modify roles.  
  
**Correct immutable version**  
```

final class User {
    private final List<String> roles;

    User(List<String> roles) {
        this.roles = new ArrayList<>(roles);
    }

    public List<String> getRoles() {
        return new ArrayList<>(roles);
    }
}

```
  
## 5️⃣ final & THREAD SAFETY — BIG INTERVIEW TRAP  
Interviewers may ask:  
“Is final thread-safe?”  
**Correct answer:**  
👉 final gives **safe publication**, NOT full thread safety.  
**What that means:**  
* Once constructor finishes  
* Other threads see correctly initialized final fields  
But…  
```

final List<String> list = new ArrayList<>();

```
❌ Still NOT thread-safe (ArrayList is mutable).  
  
**Interview one-liner (gold):**  
final guarantees visibility after construction, but does not guarantee thread safety for mutable objects.  
This answer **impresses senior interviewers**.  
  
## 6️⃣ final vs static final  
**static final = constant**  
```

public static final int MAX = 100;

```
Stored in:  
* Method Area (Metaspace)  
* Inlined by compiler  
  
**Interview trap**  
❓ Why static final constants are fast? 👉 Because compiler inlines them.  
  
## 7️⃣ When SHOULD you use final (Design perspective)  
Use final when:  
* Behavior must not change  
* Class is utility-like  
* You want immutability  
* You want safe publication  
Avoid final when:  
* Frameworks need proxying (Spring AOP)  
* Behavior extension is expected  
  
# 8️⃣ Spring-Specific Trap (VERY IMPORTANT)  
**❓ Why Spring beans are usually NOT final?**  
Because:  
* Spring uses proxies (CGLIB / JDK)  
* final classes & methods cannot be proxied  
👉 This is **why many Spring services avoid final methods**.  
  
# 🧠 PERFECT INTERVIEW ANSWERS (MEMORIZE)  
* final class → cannot be inherited  
* final method → cannot be overridden  
* final variable → reference cannot change  
* final ≠ immutable  
* final helps safe publication, not thread safety  
* String is final for security + immutability + performance  
  
  
# ✅ What is a *proxy* (in Spring)?  
A **proxy** is a **wrapper object** that Spring creates around your real bean.  
Instead of calling your bean directly, your code actually talks to this *proxy* — and the proxy can run **extra logic before and after** your method.  
Think of it like a *security gate*:  
```

Your Code → Proxy → Real Bean

```
The proxy intercepts the call.  
  
  
# Why does Spring need proxies?  
Frameworks like Spring Framework use proxies to implement features such as:  
* @Transactional  
* @Cacheable  
* @Async  
* Security checks  
* Logging / metrics  
Without proxies, Spring would have **no place to inject this behavior**.  
  
  
## 1️⃣ Why Java 8 Added default Methods (REAL REASON)  
**The real problem Java had**  
Before Java 8:  
* Interfaces could have **only abstract methods**  
* Adding a new method = **breaking all implementations**  
Example pre–Java 8 disaster:  
```

interface List {
    int size();
}

```
If Java adds:  
```

void sort();

```
👉 **Every existing List implementation breaks**  
  
**Java 8 solution: default methods**  
```

interface List {
    int size();

    default void sort() {
        System.out.println("Default sort");
    }
}

```
Now:  
* Old implementations still compile  
* New behavior is optional  
  
**Interview-quality sentence:**  
Default methods were introduced to allow interface evolution without breaking existing implementations.  
This line matters.  
  
## 2️⃣ Default Methods — RULES INTERVIEWERS EXPECT  
**Rule 1: Default methods CAN be overridden**  
```

class MyList implements List {
    @Override
    public void sort() {
        System.out.println("Custom sort");
    }
}

```
  
**Rule 2: Default methods are NOT inherited blindly**  
If **multiple interfaces** provide same default method → conflict.  
This leads us to…  
  
## 3️⃣ DIAMOND PROBLEM — THIS IS A CORE TEST  
**Situation**  
```

interface A {
    default void show() {
        System.out.println("A");
    }
}

interface B {
    default void show() {
        System.out.println("B");
    }
}

class C implements A, B {
}

```
❌ Compile-time error:  
Class C inherits unrelated defaults for show()  
  
**Why Java does this**  
Java **refuses to guess**.  
Unlike C++, Java forces you to resolve ambiguity explicitly.  
  
**Correct resolution**  
```
class C implements A, B {
    @Override
    public void show() {
        A.super.show(); // OR B.super.show()
    }
}

```
  
**Interview one-liner (important):**  
Java resolves default method conflicts at compile time and forces explicit override.  
  
**Follow-up trap**  
❓ What if one interface has default and the other has abstract?  
```
interface A {
    default void show() {}
}

interface B {
    void show();
}

```
👉 Class **MUST implement show()** (Abstract wins over default)  
  
## 4️⃣ STATIC METHODS IN INTERFACES — WHY THEY EXIST  
```

interface Validator {
    static boolean isValid(int x) {
        return x > 0;
    }
}

```
**Key rules:**  
* Static methods **belong to interface**  
* NOT inherited  
* Cannot be overridden  
```

Validator.isValid(10); // ✅

```
  
**Why not put them in utility class?**  
Because:  
* Logic is **conceptually tied to contract**  
* Keeps API cohesive  
  
**Interview sentence:**  
Static methods in interfaces provide utility behavior closely related to the contract.  
