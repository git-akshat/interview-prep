# ⭐️ Design Patterns  
  
**⭐ Interview One-Liners (VERY IMPORTANT)**  
  
- **Creational Patterns**  
    * **Singleton** → only one object  
    * **Factory** → create objects  
    * **Builder** → build complex objects  
  
- **Structural Pattern**  
    * **Adapter** → make incompatible things work  
    * **Decorator** → add features dynamically  
    * **Facade** → simplify complex system  
  
- **Behavioural Pattern**  
    * **Observer** → notify subscribers  
    * **Strategy** → switch algorithms  
  
## ✅ 1. Creational Patterns  
👉 Focus on *object creation*  
**Purpose**  
Create objects in a flexible, controlled way instead of using new everywhere.  
  
🔹**** Singleton****  
Only **one instance** exists.  
```

class Singleton {
    private static Singleton instance = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return instance;
    }
}

```
Use when: logging, config, DB connection.  
  
  
🔹** Factory**  
Create objects **without exposing creation logic**.  
```

interface Shape {
    void draw();
}

class Circle implements Shape {
    public void draw() { System.out.println("Circle"); }
}

class ShapeFactory {
    public static Shape getShape() {
        return new Circle();
    }
}

```
Use when: object type depends on input.  
  
🔹**** Builder****  
Build complex objects step-by-step.  
```
class User {
    String name;

```
```
    int age;

    static class Builder {
        private User u = new User();

        Builder name(String n) { u.name = n; return this; }
        Builder age(int a) { u.age = a; return this; }

        User build() { return u; }
    }
}

// usage
User user = new User.Builder().name("Akshat").age(28).build();

```
Use when: many optional fields.  
  
  
  
  
## ✅ 2. Structural Patterns  
👉 Focus on *class/object composition*  
**Purpose**  
Combine classes cleanly.  
  
🔹**** Adapter****  
Make incompatible interfaces work together.  
  
Your app expects a ****Charger**** interface:  
```
interface Charger {
    void charge();
}

```
But you already have an **old library / class** that looks like this:  
```
class OldCharger {
    void oldCharge() {
        System.out.println("Charging with old charger");
    }
}

```
👉 Notice:  
* Your system wants charge()  
* Old class provides oldCharge()  
They **don’t match**.  
You **cannot change OldCharger** (third-party / legacy).  
  
## ❌ Without Adapter (won’t compile)  
```

Charger charger = new OldCharger(); // ERROR

```
Different interfaces.  
  
## ✅ Solution: Adapter  
The Adapter sits in between.  
  
## Step 1 — Target Interface (what client wants)  
```
interface Charger {
    void charge();
}

```
  
## Step 2 — Existing / Legacy Class  
```

class OldCharger {
    void oldCharge() {
        System.out.println("Charging with old charger");
    }
}

```
  
## Step 3 — Adapter  
```

class ChargerAdapter implements Charger {

    private OldCharger oldCharger;

    ChargerAdapter(OldCharger oldCharger) {
        this.oldCharger = oldCharger;
    }

    @Override
    public void charge() {
        oldCharger.oldCharge();   // adapting call
    }
}

```
  
## Step 4 — Client Code  
```

public class Main {
    public static void main(String[] args) {

        OldCharger old = new OldCharger();

        Charger charger = new ChargerAdapter(old);

        charger.charge();
    }
}

```
  
## ⭐ Output  
```

Charging with old charger

```
  
Real world: USB adapters.  
  
  
🔹**** Decorator****  
Add behavior **without changing class**.  
```

interface Coffee {
    int cost();
}

class SimpleCoffee implements Coffee {
    public int cost() { return 10; }
}

class MilkDecorator implements Coffee {
    Coffee coffee;

    MilkDecorator(Coffee c) { coffee = c; }

    public int cost() {
        return coffee.cost() + 5;
    }
}

```
  
🔹**** Facade****  
Simple interface over complex system.  
  
❌ Without Facade (client handles everything)  
```
class AccountService {
    void checkBalance() { System.out.println("Checking balance"); }
}

class SecurityService {
    void verifyUser() { System.out.println("Verifying user"); }
}

class PaymentService {
    void makePayment() { System.out.println("Making payment"); }
}

public class Client {
    public static void main(String[] args) {
        SecurityService security = new SecurityService();
        AccountService account = new AccountService();
        PaymentService payment = new PaymentService();

        security.verifyUser();
        account.checkBalance();
        payment.makePayment();
    }
}

```
##   
  
**✅ With Facade**  
We hide all that complexity behind **one class**.  
  
**Step 1 — Subsystems (unchanged)**  
```
class AccountService {
    void checkBalance() {
        System.out.println("Checking balance");
    }
}

class SecurityService {
    void verifyUser() {
        System.out.println("Verifying user");
    }
}

class PaymentService {
    void makePayment() {
        System.out.println("Making payment");
    }
}

```
  
**Step 2 — Facade**  
```
class BankFacade {

    private SecurityService security = new SecurityService();
    private AccountService account = new AccountService();
    private PaymentService payment = new PaymentService();

    void payBill() {
        security.verifyUser();
        account.checkBalance();
        payment.makePayment();
        System.out.println("Bill paid successfully");
    }
}

```
  
**Step 3 — Client (VERY clean)**  
```
public class Client {
    public static void main(String[] args) {

        BankFacade bank = new BankFacade();
        bank.payBill();

    }
}

```
  
  
## ✅ 3. Behavioral Patterns  
👉 Focus on *object interaction*  
  
**🔹 Observer**  
Notify many objects when state changes.  
```

interface Observer {
    void update();
}

class Subject {
    List<Observer> list = new ArrayList<>();

    void add(Observer o) { list.add(o); }

    void notifyAllObservers() {
        for(Observer o : list) o.update();
    }
}

```
Used in: event systems.  
  
🔹**** Strategy****  
Change algorithm at runtime.  
```

interface Payment {
    void pay();
}

class CardPayment implements Payment {
    public void pay() { System.out.println("Card"); }
}

```
  
  
🔹**** Command****  
**Think Remote Control 🎮**  
Buttons don’t know HOW light works — they just execute commands.  
  
**Example: Light System**  
  
**Step 1: Command Interface**  
  
```
interface Command {
    void execute();
}

```
  
**Step 2: Receiver (Actual worker)**  
  
```
class Light {
    void on() {
        System.out.println("Light ON");
    }

    void off() {
        System.out.println("Light OFF");
    }
}

```
  
**Step 3: Concrete Commands**  
  
```
class LightOnCommand implements Command {

    private Light light;

    LightOnCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.on();
    }
}

class LightOffCommand implements Command {

    private Light light;

    LightOffCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.off();
    }
}

```
  
**Step 4: Invoker (Remote Control)**  
  
```
class RemoteControl {

    private Command command;

    void setCommand(Command command) {
        this.command = command;
    }

    void pressButton() {
        command.execute();
    }
}

```
  
**Step 5: Client**  
  
```
Light light = new Light();

RemoteControl remote = new RemoteControl();

remote.setCommand(new LightOnCommand(light));
remote.pressButton();

remote.setCommand(new LightOffCommand(light));
remote.pressButton();

```
  
**⭐ Why is this powerful?**  
Invoker DOES NOT KNOW Light.  
It only knows Command.  
Loose coupling achieved.  
  
**Undo Example (real interview favorite)**  
```
Stack<Command> history = new Stack<>();

command.execute();
history.push(command);

// undo
history.pop().execute();

```
  
  
  
  
  
  
  
  
  
  
  
