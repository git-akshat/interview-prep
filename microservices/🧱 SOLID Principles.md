# 🧱 SOLID Principles  
  
# ✅ S — Single Responsibility Principle (SRP)  
A class should have **only ONE reason to change**.  
**❌ Bad**  
```

class UserService {
    void saveUser() {}
    void sendEmail() {}
}

```
Two responsibilities: business logic + email.  
**✅ Good**  
```

class UserService {
    void saveUser() {}
}

class EmailService {
    void sendEmail() {}
}

```
🎯 Each class has **one job**.  
  
# ✅ O — Open / Closed Principle (OCP)  
Open for extension, closed for modification.  
Add new behavior **without changing existing code**.  
**Example**  
```

interface Shape {
    double area();
}

class Circle implements Shape {
    public double area() { return 10; }
}

class Rectangle implements Shape {
    public double area() { return 20; }
}

```
You can add new shapes without touching old ones.  
  
# ✅ L — Liskov Substitution Principle (LSP)  
Subclasses must be usable in place of parent class without breaking behavior.  
**Rule:**  
If B extends A, then A can be replaced by B safely.  
Bad example: overriding methods to throw exceptions or change meaning.  
  
# ✅ I — Interface Segregation Principle (ISP)  
Don’t force classes to implement methods they don’t need.  
**❌ Bad**  
```

interface Worker {
    void work();
    void eat();
}

```
Robot doesn’t eat.  
**✅ Good**  
```

interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

```
Small focused interfaces.  
  
# ✅ D — Dependency Inversion Principle (DIP)  
Depend on abstractions, not concrete classes.  
**❌ Bad**  
```

class Car {
    Engine engine = new Engine();
}

```
Tightly coupled.  
**✅ Good**  
```

interface Engine {}

class PetrolEngine implements Engine {}

class Car {
    Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}

```
Used heavily in Spring via **Dependency Injection**.  
