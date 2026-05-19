# 📕 Singleton  
  
## ✅ 1️⃣ Eager Initialization  
Instance created at class load time.  
```

public class Singleton {

    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return INSTANCE;
    }
}

```
**Pros**  
✅ Simple ✅ Thread-safe  
**Cons**  
❌ Object created even if never used ❌ No lazy loading  
  
## ✅ 2️⃣ Lazy Initialization (NOT Thread Safe)  
Created only when requested.  
```

public class Singleton {

    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

```
**Problem**  
❌ Multiple threads can create multiple instances.  
  
## ✅ 3️⃣ Synchronized Method (Thread Safe but Slow)  
```

public class Singleton {

    private static Singleton instance;

    private Singleton() {}

    public synchronized static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

```
**Pros**  
✅ Thread safe  
**Cons**  
❌ Synchronization every call → performance hit  
  
## ✅ 4️⃣ Double Checked Locking (MOST COMMON INTERVIEW ANSWER)  
```

public class Singleton {

    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

```
**Why volatile?**  
Prevents instruction reordering.  
  
**Pros**  
✅ Lazy ✅ Thread safe ✅ High performance  
  
## ⭐ 5️⃣ Bill Pugh Singleton (BEST CLASSIC APPROACH)  
Uses JVM class loading.  
```

public class Singleton {

    private Singleton() {}

    private static class Helper {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Helper.INSTANCE;
    }
}

```
**Why this is great:**  
✅ Lazy loading ✅ Thread safe ✅ No synchronization ✅ Clean  
  
## 🏆 Recommended in interviews.  
  
## ✅ 6️⃣ Enum Singleton (BEST OVERALL – Effective Java)  
```

public enum Singleton {
    INSTANCE;
}

```
Usage:  
```

Singleton.INSTANCE.doSomething();

```
  
**Why enum is king:**  
✅ Thread safe ✅ Serialization safe ✅ Reflection safe ✅ Very short  
Joshua Bloch recommends this.  
  
## ⚠️ Important Interview Trap  
**How to break Singleton?**  
* Reflection  
* Serialization  
* Cloning  
Enum prevents ALL three.  
