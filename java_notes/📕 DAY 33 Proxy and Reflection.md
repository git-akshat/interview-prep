# 📕 DAY 33: Proxy and Reflection  
# ✅ What is a *proxy* (in Spring)?  
A **proxy** is a **wrapper object** that Spring creates around your real bean.  
Instead of calling your bean directly, your code actually talks to this *proxy* — and the proxy can run **extra logic before and after** your method.  
Think of it like a *security gate*:  
```
Your Code → Proxy → Real Bean

```
The proxy intercepts the call.  
  
# 🧠 Simple definition (interview-ready)  
A proxy is a generated wrapper object that intercepts method calls to add cross-cutting behavior like transactions, logging, or security.  
  
# Why does Spring need proxies?  
Frameworks like Spring Framework use proxies to implement features such as:  
* @Transactional  
* @Cacheable  
* @Async  
* Security checks  
* Logging / metrics  
Without proxies, Spring would have **no place to inject this behavior**.  
  
.  
  
## 🔹 Example: @Transactional  
```

@Service
public class OrderService {

    @Transactional
    public void placeOrder() {
        // business logic
    }
}

```
What *actually* happens:  
1. You call placeOrder()  
2. Spring proxy intercepts it  
3. Proxy starts transaction  
4. Calls real placeOrder()  
5. Proxy commits / rolls back  
So internally it looks like:  
```

Controller → Proxy(OrderService) → Real OrderService

```
  
  
# Two proxy mechanisms in Spring  
**1️⃣ JDK Dynamic Proxy (DEFAULT)**  
* Works ONLY with interfaces  
* Proxy implements the interface  
```

interface PaymentService {
    void pay();
}

```
Spring creates:  
```

class PaymentServiceProxy implements PaymentService

```
  
**2️⃣ CGLIB Proxy**  
* Used when no interface exists  
* Creates subclass at runtime  
```

class OrderService {
    void placeOrder() {}
}

```
Spring creates:  
```

class OrderService$$Enhancer extends OrderService

```
  
# 🚨 INTERVIEW TRAPS (VERY IMPORTANT)  
**Trap 1: Why final classes break Spring proxies**  
```
final class OrderService {}

```
❌ Cannot subclass → CGLIB fails  
  
**Trap 2: Why final methods break AOP**  
```
final void process() {}

```
❌ Cannot override → proxy cannot intercept  
  
**Interview-quality answer:**  
Spring prefers interfaces because JDK proxies are simpler, safer, and avoid subclassing limitations.  
  
# How does Spring create proxies?  
Spring mainly uses two mechanisms:  
**✅ JDK Dynamic Proxy**  
(via interfaces)  
  
**✅ CGLIB**  
(via subclassing your class)  
  
High level:  
* If your bean implements an interface → JDK proxy  
* If not → CGLIB creates a **subclass at runtime**  
  
## 🌟 What is AOP?  
**AOP = Aspect-Oriented Programming**  
A programming approach where **cross-cutting concerns** (like logging, transactions, security) are separated from business logic.  
  
Instead of mixing this everywhere:  
```
log();
startTx();
businessLogic();
commitTx();

```
AOP lets you write:  
```

businessLogic();

```
…and the rest is handled **automatically** by the framework.  
In Spring Framework, this is done using **proxies** (as we discussed).  
  
## ✅ Simple Example  
```
@Aspect
@Component
class LogAspect {

    @Before("execution(* com.app.service.*.*(..))")
    public void before() {
        System.out.println("Method called");
    }
}

```
  
# ✅ What is *Reflection* (in Java)?  
**Reflection** lets a program **inspect and manipulate classes, methods, fields, and constructors at runtime** — even if they’re private.  
In simple words:  
Reflection = *Java looking at itself while running.*  
  
## 🧠 Interview one-liner (memorize)  
Reflection allows Java to examine and modify classes, methods, and fields at runtime.  
  
## 🔹 Why is reflection needed?  
Normally, Java code is fixed at compile time:  
```
User u = new User();
u.getName();

```
With reflection, you can do things like:  
* Load a class by name (string)  
* Create objects dynamically  
* Call methods dynamically  
* Access private fields  
  
All **without knowing the class at compile time.**  
  
## ✅ Simple example  
```
Class<?> clazz = Class.forName("com.example.User");

Object obj = clazz.getDeclaredConstructor().newInstance();

Method m = clazz.getDeclaredMethod("sayHello");
m.invoke(obj);

```
What happened here?  
* Class loaded by name  
* Object created dynamically  
* Method called dynamically  
No direct reference to User at compile time.  
That’s reflection.  
  
## 🔍 Accessing private fields (yes, even private!)  
```

Field f = clazz.getDeclaredField("name");
f.setAccessible(true);
f.set(obj, "Akshat");

```
Reflection can bypass access modifiers.  
  
# ✅ Where is reflection used in real life?  
Frameworks depend heavily on reflection, especially Spring Framework:  
* Dependency Injection (@Autowired)  
* AOP / Proxies  
* @Component scanning  
* @Transactional  
* JSON serialization (Jackson)  
* ORM (Hibernate)  
Example:  
```

@Service
class OrderService {}

```
Spring uses reflection to:  
* Find this class  
* Instantiate it  
* Inject dependencies  
* Wrap it with proxy  
All at runtime.  
  
  
## ⭐️ How to stop Reflection  
You cannot 100% “block” reflection in Java, but you *can* make your class effectively safe against reflective misuse.  
# ✅ 1. Guard inside the constructor (most common)  
If someone uses reflection to call your private constructor, you can **detect it and throw an exception.**  
```

public class Singleton {

    private static boolean instantiated = false;

    private Singleton() {
        if (instantiated) {
            throw new RuntimeException("Reflection not allowed!");
        }
        instantiated = true;
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }

    private static class Holder {
        private static final Singleton INSTANCE = new Singleton();
    }
}

```
**What this does**  
* First normal creation → allowed  
* Any reflective attempt after that → 💥 exception  
**Interview line**  
Add a constructor guard to prevent reflective instantiation.  
  
# ✅ 2. BEST solution: use enum (gold standard)  
```

public enum MySingleton {
    INSTANCE;
}

```
**Why this works**  
* JVM **forbids reflective creation of enums**  
* Serialization safe  
* Thread-safe  
* Zero boilerplate  
Reflection attempt:  
```

IllegalArgumentException: Cannot reflectively create enum objects

```
**Interview one-liner (very strong)**  
Enum is the safest way to prevent reflection and serialization attacks on singleton.  
  
# ✅ 3. Java Module System (Java 9+)  
You can **close packages** to reflection:  
```

module my.app {
    exports com.app.api;
}

```
If you DON’T opens a package:  
* Reflection access is blocked from outside modules.  
This is more advanced, but good to mention.  
  
# ❌ What does NOT really help  
**🚫 private constructor alone**  
Reflection can still do:  
```

constructor.setAccessible(true);

```
So:  
Private ≠ reflection-proof.  
  
**🚫 final class**  
Reflection can still instantiate it.  
Final only stops inheritance.  
  
# 🧠 Interview summary (memorize)  
You can’t completely block reflection, but you can protect classes using constructor guards or by using enum singletons, which JVM prevents from reflective instantiation.  
  
  
## ✅ Reflection on static fields  
r**eflection *does* work on static members.**  
You can **read / modify static fields** and **invoke static methods** using reflection — and you **don’t even need an object instance**.  
  
Example class:  
```

class Config {
    private static String ENV = "DEV";
}

```
Access / modify via reflection:  
```

Field f = Config.class.getDeclaredField("ENV");
f.setAccessible(true);

// For static fields, pass null as the object
System.out.println(f.get(null));   // DEV

f.set(null, "PROD");              // modify static field
System.out.println(f.get(null)); // PROD

```
**🔑 Key point**  
```

f.get(null)
f.set(null, value)

```
👉 null because **static belongs to the class, not an instance**.  
  
## ✅ Reflection on static methods  
```

class Utils {
    private static void hello() {
        System.out.println("Hello");
    }
}

```
Invoke it:  
```

Method m = Utils.class.getDeclaredMethod("hello");
m.setAccessible(true);

// null again — no object needed
m.invoke(null);

```
Output:  
```

Hello

```
  
## 🧠 Mental model  
* Instance method → needs an object  
* Static method → belongs to class → pass null  
  
## ⚠️ Why this is dangerous  
Reflection can:  
* Change private static fields  
* Call private static methods  
* Break immutability  
* Break singleton patterns  
* Bypass access modifiers  
That’s why it’s powerful — and risky.  
  
## 🔥 Common interview trap  
**❓ Can reflection modify static final?**  
Sometimes — but **compile-time constants are inlined**, so:  
```

public static final int X = 10;

```
Even if reflection changes X, **other classes may still see 10**, because they already copied the value at compile time.  
So behavior becomes inconsistent.  
  
## ⭐ Interview one-liner (memorize)  
Yes, reflection can access static fields and methods; since they belong to the class, we pass null as the instance when invoking or accessing them.  
  
