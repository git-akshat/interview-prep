# 📘 Day 12: Profiles, Configuration & Bean Lifecycle  
  
## 1. Why Configuration Matters  
In real systems:  
* Dev, test, prod environments differ  
* DB credentials change  
* Logging levels change  
* Feature flags differ  
Hardcoding values = ❌ bad practice.  
👉 Interview line:  
Externalized configuration makes applications environment-independent.  
  
## 2. application.properties vs application.yml  
Both do the same thing.  
**application.properties**  
```
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/notes_db

```
**application.yml**  
```
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/notes_db

```
👉 Interview tip: YAML is more readable for complex configs.  
  
## 3. Spring Profiles  
Profiles allow **environment-specific configuration**.  
Common profiles:  
* dev  
* test  
* prod  
  
**Profile-based files**  
```
application-dev.properties
application-prod.properties

```
Example:  
```
# application-dev.properties
spring.jpa.show-sql=true
logging.level.root=DEBUG

# application-prod.properties
spring.jpa.show-sql=false
logging.level.root=INFO

```
  
**Activating a Profile **  
  
**Via application.properties**  
```
spring.profiles.active=dev

```
  
**Via command line**  
```
java -jar app.jar --spring.profiles.active=prod

```
  
## 4. @Profile Annotation  
You can enable beans per profile.  
```
@Configuration
public class DataSourceConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        return new HikariDataSource();
    }

    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        return new HikariDataSource();
    }
}

```
👉 Interview line:  
@Profile controls bean creation based on active environment.  
  
## 5. @Value vs @ConfigurationProperties  
**@Value (Simple values)**  
```
@Value("${server.port}")
private int port;

```
Problems:  
* Scattered config  
* Hard to manage many properties  
  
**@ConfigurationProperties (Preferred)**  
```
@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private String name;
    private String version;

    // getters & setters
}

app.name=NotesApp
app.version=1.0

```
👉 Interview line:  
@ConfigurationProperties binds external config to POJO.  
  
## 6. Bean Lifecycle (Frequently Asked)  
A Spring bean goes through:  
1. Instantiation  
2. Dependency injection  
3. Initialization  
4. Ready for use  
5. Destruction  
  
**Lifecycle Hooks**  
```
@PostConstruct

@PostConstruct
public void init() {
    System.out.println("Bean initialized");
}
@PreDestroy

@PreDestroy
public void cleanup() {
    System.out.println("Bean destroyed");
}

```
👉 Interview line:  
**@PostConstruct **runs after dependency injection, before bean use.  
  
## 7. Bean Scopes (Know These)  

| Scope     | Meaning                  |
| --------- | ------------------------ |
| singleton | One instance (default)   |
| prototype | New instance per request |
| request   | One per HTTP request     |
| session   | One per HTTP session     |
  
  
  
## 🔨 Project Task – Day 12  
**Environment-Specific Notes App**  
1. Create:  
    * application-dev.properties  
    * application-prod.properties  
2. Enable dev profile  
3. Change:  
    * Logging levels  
    * DB config (if needed)  
4. Create @ConfigurationProperties class  
5. Add @PostConstruct log to verify lifecycle  
