# Sample API  
  
## 🏗 Final Architecture  
```

demo
├── controller
├── service
├── repository
├── entity
├── dto
├── exception
├── config
└── DemoApplication

```
  
## 1️⃣ Maven Dependencies  
```
pom.xml

<dependencies>

    <!-- Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- PostgreSQL -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
    </dependency>

    <!-- Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- Swagger -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.5.0</version>
    </dependency>

</dependencies>

```
  
## 2️⃣ Entity (Database Model)  
```

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

    // getters/setters
}

```
  
## 3️⃣ DTO + Validation  
```

public class UserDto {

    @NotBlank
    private String name;

    @Email
    @NotBlank
    private String email;

    // getters/setters
}

```
  
## 4️⃣ Repository  
```

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    boolean existsByEmail(String email);
}

```
  
## 5️⃣ Service  
```

@Service
public class UserService {

    private final UserRepository repo;

    public UserService(UserRepository repo) {
        this.repo = repo;
    }

    public User create(UserDto dto) {

        if(repo.existsByEmail(dto.getEmail())) {
            throw new RuntimeException("Email already exists");
        }

        User user = new User();
        user.setName(dto.getName());
        user.setEmail(dto.getEmail());

        return repo.save(user);
    }

    public User get(Long id) {
        return repo.findById(id)
                .orElseThrow(() -> new RuntimeException("User not found"));
    }

    public List<User> getAll() {
        return repo.findAll();
    }

    public void delete(Long id) {
        repo.deleteById(id);
    }
}

```
  
## 6️⃣ Controller  
```

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @PostMapping
    public User create(@Valid @RequestBody UserDto dto) {
        return service.create(dto);
    }

    @GetMapping("/{id}")
    public User get(@PathVariable Long id) {
        return service.get(id);
    }

    @GetMapping
    public List<User> getAll() {
        return service.getAll();
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        service.delete(id);
    }
}

```
  
## 7️⃣ Global Exception Handling  
```

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handle(RuntimeException ex) {
        return ResponseEntity.badRequest().body(ex.getMessage());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String,String>> validation(MethodArgumentNotValidException ex) {

        Map<String,String> errors = new HashMap<>();

        ex.getBindingResult().getFieldErrors()
            .forEach(e -> errors.put(e.getField(), e.getDefaultMessage()));

        return ResponseEntity.badRequest().body(errors);
    }
}

```
  
## 8️⃣ application.yml  
```

spring:
  datasource:
    url: jdbc:postgresql://postgres:5432/demo
    username: postgres
    password: postgres

  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

springdoc:
  swagger-ui:
    path: /swagger-ui.html

```
  
## ✅ Swagger  
After running:  
```

http://localhost:8080/swagger-ui.html

```
  
## 9️⃣ Dockerfile  
```

FROM openjdk:17-jdk-slim
COPY target/demo.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]

```
  
## 🔟 docker-compose.yml  
```

version: '3.8'

services:

  postgres:
    image: postgres
    environment:
      POSTGRES_DB: demo
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres

  app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - postgres

```
  
## 🚀 Run Everything  
```
mvn clean package
docker compose up --build

```
  
## 🧠 Interview GOLD Architecture Summary  
```
Controller → Service → Repository → PostgreSQL
DTO → Validation
@ControllerAdvice → Global errors
Swagger → API docs
Docker → deployment

```
  
##   
**🔐 1. JWT Security (Spring Boot 3)**  
**Add dependency**  
  
```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt-api</artifactId>
  <version>0.11.5</version>
</dependency>

```
  
  
**JWT Util**  
  
```
@Component
public class JwtUtil {

    private final String SECRET = "secret";

    public String generate(String username) {
        return Jwts.builder()
            .setSubject(username)
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis()+3600000))
            .signWith(Keys.hmacShaKeyFor(SECRET.getBytes()))
            .compact();
    }

    public String extract(String token) {
        return Jwts.parserBuilder()
            .setSigningKey(SECRET.getBytes())
            .build()
            .parseClaimsJws(token)
            .getBody()
            .getSubject();
    }
}

```
  
**Security Config**  
  
```
@Bean
SecurityFilterChain filter(HttpSecurity http) throws Exception {

    http.csrf(csrf->csrf.disable())
        .authorizeHttpRequests(auth->auth
            .requestMatchers("/auth/**","/swagger-ui/**","/v3/api-docs/**").permitAll()
            .anyRequest().authenticated()
        );

    return http.build();
}

```
  
**📄 2. Pagination (Spring Data JPA)**  
Controller:  
  
```
@GetMapping
public Page<User> getUsers(
    @RequestParam(defaultValue="0") int page,
    @RequestParam(defaultValue="10") int size){

    return repo.findAll(PageRequest.of(page,size));
}

```
  
URL:  
```
/api/users?page=0&size=5

```
  
**🧵 3. Kafka Integration**  
**Dependency**  
  
```
<dependency>
 <groupId>org.springframework.kafka</groupId>
 <artifactId>spring-kafka</artifactId>
</dependency>

```
  
**Producer**  
  
```
@Service
public class Producer {

 @Autowired KafkaTemplate<String,String> kafka;

 public void send(String msg){
     kafka.send("topic",msg);
 }
}

```
  
**Consumer**  
  
```
@KafkaListener(topics="topic")
public void listen(String msg){
   System.out.println(msg);
}

```
  
**⚡ 4. Async APIs**  
Enable async:  
  
```
@EnableAsync

Service:

@Async
public CompletableFuture<String> process(){
   Thread.sleep(3000);
   return CompletableFuture.completedFuture("done");
}

Controller:

@GetMapping("/async")
public CompletableFuture<String> async(){
   return service.process();
}

```
  
  
  
  
  
  
  
  
  
  
