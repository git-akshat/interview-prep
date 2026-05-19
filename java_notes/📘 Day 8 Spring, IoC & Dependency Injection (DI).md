# 📘 Day 8: Spring, IoC & Dependency Injection (DI)  
  
## 1. Why Spring Exists (Start Here)  
Problem before Spring:  
* Too much boilerplate  
* Tight coupling between classes  
* Hard to test  
* Manual object creation everywhere (new)  
Spring solves this by:  
* Managing object creation  
* Managing dependencies  
* Managing lifecycle  
👉 Interview line:  
Spring is a framework that manages object creation and dependency wiring for you.  
  
## 2. What is Spring Boot?  
Spring Boot is **NOT a replacement** for Spring.  
Spring Boot:  
* Built on top of Spring  
* Removes XML configuration  
* Provides auto-configuration  
* Embedded servers (Tomcat)  
👉* Interview line:*  
*Spring Boot simplifies Spring by providing auto-configuration and embedded servers.*  
  
## 3. Core Concept: Inversion of Control (IoC)  
**Traditional way (tight coupling)**  
```
class Engine {}

class Car {
    Engine engine = new Engine(); // tightly coupled
}

```
Problems:  
* Hard to replace Engine  
* Hard to test  
* Car controls dependency creation  
  
**IoC way (Spring style)**  
```
class Car {
    private Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}

```
Now:  
* Car does NOT create Engine  
* Dependency is **injected**  
* Control is inverted → **IoC**  
👉 Interview-ready definition:  
IoC means the framework controls object creation, not the developer.  
  
## 4. Dependency Injection (DI)  
DI is **how IoC is implemented**.  
  
⭐️Spring injects dependencies in 3 ways:  
1. Constructor injection ✅ (recommended)  
2. Setter injection  
3. Field injection ❌ (avoid in interviews)  
  
**Constructor Injection (Best Practice)**  
```
@Component
class Engine {}

@Component
class Car {

    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}

```
Why constructor injection:  
* Mandatory dependencies  
* Easier testing  
* Immutable design  
  
👉 Interview line:  
Constructor injection is preferred because it ensures required dependencies are available at object creation.  
  
## 5. Spring Container & Beans  
**What is a Bean?**  
* An object managed by Spring container  
  
**What is Spring Container?**  
* Creates beans  
* Injects dependencies  
* Manages lifecycle  
  
## 6. Common Spring Annotations  

| Annotation     | Purpose                           |
| -------------- | --------------------------------- |
| @Component     | Generic Spring bean               |
| @Service       | Business logic layer              |
| @Repository    | DAO layer + exception translation |
| @Autowired     | Inject dependency                 |
| @Configuration | Java-based config                 |
| @Bean          | Explicit bean creation            |
  
👉 Interview tip: @Service and @Repository are **specialized @Component**.  
  
## 7. First Spring Boot Application  
**Step 1: Create Project**  
* Java 17  
* Maven  
* Spring Web dependency  
  
**Step 2: Main Class**  
```
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}

```
  
****@SpringBootApplication consist of three annotations****  
```
@Configuration
@EnableAutoConfiguration
@ComponentScan

```
  
## 8. First REST API – /hello  
```
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello Spring Boot";
    }
}

```
Run app → open browser:  
```
http://localhost:8080/hello

```
  
## 9. What Happens Internally? (Interview Insight)  
When app starts:  
1. Spring Boot starts embedded Tomcat  
2. Scans components  
3. Creates beans  
4. Injects dependencies  
5. Starts listening on port 8080  
  
  
## 🔨 Project Task – Day 8  
**Notes App – Kickoff**  
1. Create Spring Boot project  
2. Add /hello API  
3. Push project to GitHub  
4. Verify app runs on port 8080  
👉 This project will evolve **every day**.  
