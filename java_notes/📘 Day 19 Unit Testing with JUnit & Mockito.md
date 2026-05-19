# 📘 Day 19: Unit Testing with JUnit & Mockito  
  
## 1. Why Unit Testing Matters  
Without tests:  
* Bugs reach production  
* Refactoring is risky  
* Hard to trust changes  
With tests:  
* Fast feedback  
* Safer refactoring  
* Better design  
👉 **Interview line:**  
Unit tests validate individual components in isolation.  
  
## 2. Unit Test vs Integration Test (Clear Difference)  
**Unit Test**  
* Tests **one class**  
* Dependencies are mocked  
* Fast  
* No Spring context required (ideally)  
**Integration Test**  
* Tests multiple layers together  
* Uses real DB / containers  
* Slower  
👉 Interview line:  
Unit tests isolate logic; integration tests verify wiring.  
  
## 3. JUnit 5 Basics  
JUnit is the **testing framework**.  
Common annotations:  
* @Test  
* @BeforeEach  
* @AfterEach  
* @DisplayName  
  
**Simple JUnit Test**  
```
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    @Test
    void add_shouldReturnSum() {
        int result = 2 + 3;
        assertEquals(5, result);
    }
}

```
  
## 4. Mockito Basics (Mocking Dependencies)  
Mockito allows:  
* Creating fake objects (mocks)  
* Controlling behavior  
* Verifying interactions  
👉 **Key idea:** Mock dependencies, not the class under test.  
  
**Common Mockito Annotations**  
* @Mock → creates mock  
* @InjectMocks → injects mocks into class  
* @ExtendWith(MockitoExtension.class) → enables Mockito  
  
## 5. Unit Testing a Service (Real Example)  
**Service Code**  
```
@Service
public class NoteService {

    private final NoteRepository repository;

    public NoteService(NoteRepository repository) {
        this.repository = repository;
    }

    public Note getById(Long id) {
        return repository.findById(id)
                .orElseThrow(() ->
                        new RuntimeException("Not found"));
    }
}

```
  
**Unit Test for Service**  
```
@ExtendWith(MockitoExtension.class)
class NoteServiceTest {

    @Mock
    private NoteRepository repository;

    @InjectMocks
    private NoteService service;

    @Test
    void getById_whenNoteExists_shouldReturnNote() {
        Note note = new Note();
        note.setId(1L);

        when(repository.findById(1L))
                .thenReturn(Optional.of(note));

        Note result = service.getById(1L);

        assertEquals(1L, result.getId());
    }

    @Test
    void getById_whenNoteMissing_shouldThrowException() {
        when(repository.findById(1L))
                .thenReturn(Optional.empty());

        assertThrows(RuntimeException.class,
                () -> service.getById(1L));
    }
}

```
👉 Interview line:  
Unit tests mock repositories and test only service logic.  
  
## 6. Stubbing vs Verification (Mockito Concept)  
**Stubbing**  
Define behavior:  
```
when(repo.findById(1L))
    .thenReturn(Optional.of(note));

```
**Verification**  
Verify interaction:  
```
verify(repo).findById(1L);

```
  
## 7. Testing Controllers (Unit Style)  
For controller tests:  
* Use @WebMvcTest  
* Mock service layer  
* Test HTTP behavior  
  
**Controller Test Example**  
```
@WebMvcTest(NoteController.class)
class NoteControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private NoteService service;

    @Test
    void getNotes_shouldReturn200() throws Exception {
        mockMvc.perform(get("/notes"))
                .andExpect(status().isOk());
    }
}

```
👉 Interview tip:  
Controller tests validate request/response, not business logic.  
  
## 8. What NOT to Do in Unit Tests  
❌ Use real DB ❌ Load full Spring context ❌ Test multiple classes together ❌ Write huge test methods  
  
## 🔨 Project Task – Day 19  
**Add Unit Tests to Notes API**  
1. Write unit tests for:  
    * NoteService  
    * AuthService (if present)  
2. Mock repositories using Mockito  
3. Write controller test using @WebMvcTest  
4. Ensure:  
    * Success case  
    * Exception case  
5. Run tests via IDE and Maven  
