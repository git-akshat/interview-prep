## 🏭 Factory Pattern vs 🧱 Builder Pattern (Java)  
Both are **creational design patterns**, but they solve *different* problems.  
  
## ✅ Factory Pattern  
**👉 Used when you want to hide object creation logic**  
**Core idea**  
Factory decides **which object to create**.  
You ask for something — factory gives you the right implementation.  
  
## Simple Example (Factory)  
**Step 1: Common interface**  
```

interface Notification {
    void notifyUser();
}

```
  
**Step 2: Implementations**  
```

class EmailNotification implements Notification {
    public void notifyUser() {
        System.out.println("Email sent");
    }
}

class SmsNotification implements Notification {
    public void notifyUser() {
        System.out.println("SMS sent");
    }
}

```
  
**Step 3: Factory**  
```

class NotificationFactory {

    public static Notification getNotification(String type) {
        if (type.equals("EMAIL"))
            return new EmailNotification();
        else if (type.equals("SMS"))
            return new SmsNotification();

        return null;
    }
}

```
  
**Usage**  
```
Notification n = NotificationFactory.getNotification("EMAIL");
n.notifyUser();

```
  
**🎯 Factory Use Case**  
* Multiple implementations  
* Runtime decision  
* Decoupling creation logic  
  
**🧠 Interview One-liner (Factory)**  
Factory pattern provides an interface for creating objects while letting subclasses decide which class to instantiate.  
  
  
## ✅ Builder Pattern  
**👉 Used when object creation is complex or has many parameters**  
**Core idea**  
Build object **step-by-step**.  
Avoid huge constructors.  
  
## Problem Without Builder  
```
User user = new User("Akshat", 25, "blr", "India", true);

```
Ugly + confusing.  
  
## Builder Example  
**Product Class**  
```

class User {
    private String name;
    private int age;
    private String city;
    private boolean active;

    private User(UserBuilder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.city = builder.city;
        this.active = builder.active;
    }

    public static class UserBuilder {
        private String name;
        private int age;
        private String city;
        private boolean active;

        public UserBuilder setName(String name) {
            this.name = name;
            return this;
        }

        public UserBuilder setAge(int age) {
            this.age = age;
            return this;
        }

        public UserBuilder setCity(String city) {
            this.city = city;
            return this;
        }

        public UserBuilder setActive(boolean active) {
            this.active = active;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}

```
  
**Usage**  
```

User user = new User.UserBuilder()
        .setName("Akshat")
        .setAge(25)
        .setCity("Bangalore")
        .setActive(true)
        .build();

```
Clean + readable.  
  
**🎯 Builder Use Case**  
* Many optional fields  
* Immutable objects  
* Clean object creation  
  
**🧠 Interview One-liner (Builder)**  
Builder pattern separates object construction from representation and is used for complex objects.  
