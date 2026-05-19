# 📘 Day 6: Java 8 – Streams, Lambdas, Functional Interfaces  
  
## 1. Why Java 8 Was a Big Deal  
Before Java 8:  
* Code was verbose  
* Loop-heavy  
* Harder to reason about transformations  
  
Java 8 introduced:  
* **Lambdas**  
* **Functional interfaces**  
* **Streams**  
* **Method reference (Employe::getDepartment)**  
* **Optional class**  
  
## 2. Functional Interfaces  
An interface with **exactly ONE abstract method**.  
**Why Java 8 introduced this?**  
To support:  
* Lambdas  
* Streams  
* Functional programming  
  
## ✅ Rule (very important)  
A **functional interface may have:**  
* ✅ **Exactly ONE *abstract* method**  
* ✅ Any number of **default methods**  
* ✅ Any number of **static methods**  
* ✅ Methods from Object (toString, equals, etc.)  
  
👉 **Only *abstract* methods are counted.**  
Default + static methods are **ignored** for the “single abstract method” rule.  
  
Example  
```
@FunctionalInterface
interface MyFunc {

    // ✅ ONE abstract method (this is what lambda implements)
    void execute();

    // ✅ default method
    default void log() {
        System.out.println("logging...");
    }

    // ✅ static method
    static void help() {
        System.out.println("help...");
    }
}

```
  
Examples:  
* Runnable  
* Callable  
* Comparator  
* Function  
* Predicate  
* Consumer  
* Supplier  
```
@FunctionalInterface
interface MyFunc {
    void execute();
}

```
  
## 3. Core Built-in Functional Interfaces  
**Predicate<T>**  
* Takes T  
* Returns boolean  
```
Predicate<Integer> isEven = n -> n % 2 == 0;

```
  
**Function<T, R>**  
* Takes T  
* Returns R  
```
Function<String, Integer> length = s -> s.length();

```
  
**Consumer<T>**  
* Takes T  
* Returns nothing  
```
Consumer<String> print = s -> System.out.println(s);

```
  
**Supplier<T>**  
* Takes nothing  
* Returns T  
```
Supplier<Double> random = () -> Math.random();

```
  
## 4. Lambda Expressions  
A **lambda expression** is a **short way to implement a functional interface** (an interface with exactly **one abstract method**).  
  
**✅ General Syntax**  
```
(parameters) -> expression
            or
(parameters) -> { statements }

```
  
Examples:  
```
() -> System.out.println("Hi")
(x) -> x * x
(a, b) -> a + b

```
  
**🧠 Key Rule (memorize)**  
Lambda works ONLY with **functional interfaces** (single abstract method).  
  
**✅ Example 1 — Runnable (no parameters)**  
  
**Without lambda:**  
```
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Running");
    }
};

```
  
**With lambda:**  
```
Runnable r = () -> System.out.println("Running");

```
  
**✅ Example 2 — Comparator (two parameters)**  
```
List<Integer> list = Arrays.asList(5, 2, 9);

list.sort((a, b) -> a - b);

System.out.println(list);

```
  
**✅ Example 3 — Custom Functional Interface**  
```
@FunctionalInterface
interface Calc {
    int add(int a, int b);
}

// Using lambda:

Calc c = (x, y) -> x + y;

System.out.println(c.add(5, 3)); // 8

```
  
**✅ Example 4 — forEach with lambda (VERY common)**  
```
List<String> names = List.of("John", "Mary", "Alex");

names.forEach(n -> System.out.println(n));

Even shorter:

names.forEach(System.out::println);
(Method reference)

```
  
**🔥 Parameter Rules**  
```
// Single parameter — parentheses optional:
x -> x * x

// Multiple parameters — parentheses required:
(a, b) -> a + b

```
  
  
## 5. Streams  
A Stream:  
* Is **not** a data structure  
* Processes data **from a source**  
* Supports **functional operations**  
```
List<Integer> nums = List.of(1, 2, 3, 4, 5);

```
  
## 6. Stream Pipeline  
Stream operations happen in **three stages**:  
1. **Source**  
2. **Intermediate operations**  
3. **Terminal operation**  
  
```
nums.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .forEach(System.out::println);

```
  
Key points:  
* Intermediate ops are **lazy**  
* Terminal op triggers execution  
  
## 7. Common Stream Operations  
**filter**  
Select elements  
```
.filter(n -> n > 10)

```
  
**map**  
Transform elements  
```
.map(String::toUpperCase)

```
  
**flatMap**  
Flatten nested structures  
```
List<List<String>> list = List.of(
    List.of("a", "b"),
    List.of("c", "d")
);

list.stream()
    .flatMap(l -> l.stream())
    .forEach(System.out::println);

```
  
**reduce**  
Combine elements  
```
int sum = nums.stream()
              .reduce(0, Integer::sum);

```
  
## 8. map vs flatMap  
* map → one-to-one transformation  
* flatMap → flattens nested streams  
  
## 9. Streams vs Loops  
Streams:  
* Declarative  
* Easier to read  
* Parallelizable  
Loops:  
* More control  
* Sometimes faster for simple logic  
Refer to JAVA streams notes  
  
## 10. Project Task – Day 6  
**Employee Analytics Module**  
* Store employees in a list  
* Use streams to:  
    * Group by department  
    * Calculate average salary per department  
    * Find top earner  
* Print results cleanly  
