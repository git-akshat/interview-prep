# 📘 Day 20: Integration Testing & Testcontainers  
  
## 1. Why Integration Testing Exists  
Unit tests answer:  
* “Does this class work in isolation?”  
Integration tests answer:  
* “Does the system work when everything is wired together?”  
  
👉 **Interview line:**  
Integration tests verify interaction between multiple components.  
  
## 2. Unit Test vs Integration Test (One Last Time, Clearly)  

| Aspect       | Unit Test    | Integration Test |
| ------------ | ------------ | ---------------- |
| Scope        | Single class | Multiple layers  |
| Dependencies | Mocked       | Real             |
| Speed        | Fast         | Slower           |
| DB           | ❌ No         | ✅ Yes            |
  
👉 Interview rule: **Unit tests isolate logic; integration tests validate wiring + configuration.**  
  
## 3. @SpringBootTest (Core Annotation)  
This loads:  
* Full Spring context  
* Real beans  
* Real configuration  
```
@SpringBootTest
class NotesIntegrationTest {
}

```
By default:  
* Starts embedded server (random port or mock)  
* Loads all beans  
  
## 4. Testing REST APIs End-to-End  
**Using MockMvc**  
```
@SpringBootTest
@AutoConfigureMockMvc
class NoteIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void createAndFetchNote() throws Exception {

        mockMvc.perform(post("/notes")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                      "title": "Test",
                      "content": "Integration test"
                    }
                """))
                .andExpect(status().isOk());

        mockMvc.perform(get("/notes"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].title")
                        .value("Test"));
    }
}

```
👉 Interview line:  
Integration tests validate request → controller → service → repository → DB.  
  
## 5. Problem with Real Databases in Tests  
Using local DB:  
* Environment dependent  
* Hard to set up on CI  
* Risky (data loss)  
Solution → **Testcontainers**.  
  
## 6. What is Testcontainers?  
Testcontainers:  
* Spins up **real databases** in Docker  
* Temporary, isolated  
* Automatically destroyed  
Supported:  
* MySQL  
* PostgreSQL  
* Redis  
* Kafka  
  
👉 **Interview line:**  
Testcontainers provide production-like dependencies for tests.  
  
## 7. Basic Testcontainers Setup  
**Dependency (Maven)**  
```
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>mysql</artifactId>
  <scope>test</scope>
</dependency>

```
  
**MySQL Container Example**  
```
@Testcontainers
@SpringBootTest
class NoteIntegrationTest {

    @Container
    static MySQLContainer<?> mysql =
            new MySQLContainer<>("mysql:8.0")
                    .withDatabaseName("testdb")
                    .withUsername("test")
                    .withPassword("test");

    @DynamicPropertySource
    static void overrideProps(
            DynamicPropertyRegistry registry) {

        registry.add("spring.datasource.url",
                mysql::getJdbcUrl);
        registry.add("spring.datasource.username",
                mysql::getUsername);
        registry.add("spring.datasource.password",
                mysql::getPassword);
    }
}

```
What this does:  
* Starts MySQL in Docker  
* Injects DB config dynamically  
* Uses **real DB**, clean every run  
  
## 8. When to Use Testcontainers (Interview Favorite)  
Use when:  
* Testing DB queries  
* Testing migrations  
* Testing caching (Redis)  
* CI/CD pipelines  
Don’t use for:  
* Simple unit tests  
  
## 9. Data Cleanup Strategy  
Options:  
* @Transactional (rollback after test)  
* Clean DB before each test  
* Use container restart (slower)  
👉 Interview line:  
Tests must be isolated and repeatable.  
  
## 10. Common Interview Questions (Day 20)  
**Q1. Why not use H2 always?**  
H2 behaves differently from real DBs.  
  
**Q2. @SpringBootTest vs @WebMvcTest?**  
* SpringBootTest → full context  
* WebMvcTest → controller slice only  
  
**Q3. Why Testcontainers over local DB?**  
Consistent, isolated, CI-friendly.  
  
**Q4. Are integration tests slow?**  
Yes, but fewer in number.  
  
## 📝 Day 20 MCQs  
**Q1. Which annotation loads full Spring context?**  
a) @WebMvcTest b) @DataJpaTest c) @SpringBootTest d) @ContextTest  
✅ **Answer:** c)  
  
**Q2. Testcontainers require what?**  
a) JVM b) Docker c) Kubernetes d) Maven Central  
✅ **Answer:** b)  
  
**Q3. What does @DynamicPropertySource do?**  
a) Creates beans b) Injects test properties c) Starts containers d) Cleans DB  
✅ **Answer:** b)  
  
**Q4. Integration tests should:**  
a) Mock everything b) Test only one method c) Use real dependencies d) Avoid Spring  
✅ **Answer:** c)  
  
## 🔨 Project Task – Day 20  
**Integration Tests for Notes API**  
1. Write integration test for:  
    * Create note  
    * Fetch notes  
2. Use @SpringBootTest + MockMvc  
3. Add Testcontainers for MySQL  
4. Verify:  
    * Data persists correctly  
    * API works end-to-end  
5. Run tests locally & ensure repeatability  
