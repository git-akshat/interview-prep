# 📘 Day 11: Exception Handling, Global Error Handling & Logging  
  
## 1. Why Exception Handling Matters  
Bad APIs:  
* Return 500 for everything  
* Leak stack traces  
* Confuse clients  
Good APIs:  
* Return **meaningful HTTP status codes**  
* Give **clear error messages**  
* Log errors properly  
👉 Interview line:  
Exception handling improves API reliability, debuggability, and client experience.  
  
## 2. Checked vs Unchecked Exceptions (Quick Refresh)  
**Checked Exceptions**  
* Checked at compile time  
* Must be handled or declared  
* Example: IOException, SQLException  
**Unchecked Exceptions**  
* Runtime exceptions  
* Extend RuntimeException  
* Example: NullPointerException, IllegalArgumentException  
👉 In Spring Boot:  
* **Custom exceptions are usually unchecked**  
  
## 3. Problem with Handling Exceptions in Controllers  
❌ Bad practice:  
```
@GetMapping("/{id}")
public Note get(@PathVariable Long id) {
    try {
        return service.getById(id);
    } catch (Exception e) {
        return null;
    }
}

```
Problems:  
* Duplicated try-catch everywhere  
* Wrong HTTP status  
* Poor readability  
  
## 4. Custom Exception (Best Practice)  
```
public class NoteNotFoundException extends RuntimeException {

    public NoteNotFoundException(String message) {
        super(message);
    }
}

```
Used when:  
* Resource not found  
* Business rule violated  
  
## 5. Throw Exception from Service Layer  
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
                        new NoteNotFoundException(
                            "Note not found with id " + id));
    }
}

```
👉 Interview line:  
Exceptions should be thrown from the service layer, not the controller.  
  
## 6. Global Exception Handling with @ControllerAdvice  
```
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NoteNotFoundException.class)
    public ResponseEntity<String> handleNotFound(
            NoteNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneric(
            Exception ex) {

        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Something went wrong");
    }
}

```
Benefits:  
* Centralized handling  
* Clean controllers  
* Consistent error responses  
👉 Interview line:  
@ControllerAdvice provides centralized exception handling across controllers.  
  
## 7. Proper HTTP Status Codes  
Common ones you must know:  

| Status | Meaning               |
| ------ | --------------------- |
| 200    | OK                    |
| 201    | Created               |
| 400    | Bad Request           |
| 404    | Not Found             |
| 500    | Internal Server Error |
  
##   
## 8. Logging (Must-Have in Real Projects)  
**Why logging?**  
* Debug production issues  
* Monitor system health  
* Trace failures  
  
**SLF4J + Logback (Default in Spring Boot)**  
```
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Service
public class NoteService {

    private static final Logger log =
            LoggerFactory.getLogger(NoteService.class);

    public void create(Note note) {
        log.info("Creating note: {}", note.getTitle());
    }
}

```
Log levels:  
* ERROR  
* WARN  
* INFO  
* DEBUG  
* TRACE  
👉 Interview line:  
Logging should be meaningful and placed at service boundaries.  
  
## 9. Logging Best Practices  
* ❌ Don’t log sensitive data  
* ❌ Don’t use System.out.println  
* ✅ Log exceptions with stack trace  
* ✅ Use correct log levels  
  
  
## 🔨 Project Task – Day 11  
**Make Notes API Production-Ready**  
1. Create custom exception: NoteNotFoundException  
2. Throw exception from service layer  
3. Add @RestControllerAdvice  
4. Return proper HTTP status codes  
5. Add logs in service & controller  
6. Test error scenarios:  
    * Invalid ID  
    * Missing resource  
