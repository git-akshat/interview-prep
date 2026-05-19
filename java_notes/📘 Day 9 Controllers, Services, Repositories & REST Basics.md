# 📘 Day 9: Controllers, Services, Repositories & REST Basics  
  
## 1. Why Layered Architecture?  
Bad design (what beginners do):  
* Controller talks directly to DB  
* Business logic everywhere  
* Hard to test, hard to change  
  
Spring encourages **layered architecture**:  
```
Controller → Service → Repository → DB

```
  
👉 Interview line:  
Layered architecture improves maintainability, testability, and separation of concerns.  
  
## 2. Controller Layer  
**Purpose**  
* Handle HTTP requests  
* Validate input  
* Return HTTP responses  
* **No business logic**  
  
**Example Controller**  
```
@RestController
@RequestMapping("/notes")
public class NoteController {

    private final NoteService noteService;

    public NoteController(NoteService noteService) {
        this.noteService = noteService;
    }

    @GetMapping
    public List<String> getNotes() {
        return noteService.getAllNotes();
    }
}

```
Annotations:  
* @RestController → REST + JSON  
* @RequestMapping → base path  
* @GetMapping → HTTP GET  
  
## 3. Service Layer  
**Purpose**  
* Business logic  
* Transaction boundaries  
* Reusable logic  
  
**Example Service**  
```
@Service
public class NoteService {

    public List<String> getAllNotes() {
        return List.of("Learn Java", "Learn Spring");
    }
}

```
👉 Interview line:  
Service layer contains business logic and coordinates repositories.  
  
## 4. Repository Layer  
**Purpose**  
* Data access  
* DB interaction  
* No business logic  
For now, we’ll **simulate DB using memory**. (Real DB comes on Day 10 with JPA.)  
  
```
@Repository
public class NoteRepository {

    private final List<String> notes = new ArrayList<>();

    public List<String> findAll() {
        return notes;
    }

    public void save(String note) {
        notes.add(note);
    }
}

```
  
## 5. REST Principles (Very Important)  
REST = Representational State Transfer  
Key ideas:  
* Resource-based URLs  
* HTTP methods define action  
* Stateless communication  
  
**HTTP Methods Meaning**  

| Method | Purpose                |
| ------ | ---------------------- |
| GET    | Fetch data             |
| POST   | Create new data        |
| PUT    | Update entire resource |
| DELETE | Remove data            |
  
  
**Idempotency (Interview Favorite)**  
* GET → idempotent  
* PUT → idempotent  
* DELETE → idempotent  
* POST → ❌ not idempotent  
  
👉 Idempotent = calling it multiple times gives same result.  
  
## 6. CRUD API for Notes  
**Create Note (POST)**  
```
@PostMapping
public void createNote(@RequestBody String note) {
    noteService.create(note);
}

```
  
**Get All Notes (GET)**  
```
@GetMapping
public List<String> getNotes() {
    return noteService.getAll();
}

```
  
**Delete Note (DELETE)**  
```
@DeleteMapping("/{index}")
public void deleteNote(@PathVariable int index) {
    noteService.delete(index);
}

```
  
## 7. RequestBody vs PathVariable vs RequestParam  
  
**@RequestBody**  
* JSON body  
* Used for POST / PUT  
  
**@PathVariable**  
* URL segment  
* Resource identifier  
  
**@RequestParam**  
* Query parameters  
* Optional filters  
  
  
## 🔨 Project Task – Day 9  
**Notes API (In-Memory)**  
1. Create:  
    * NoteController  
    * NoteService  
    * NoteRepository  
2. Implement:  
    * POST /notes  
    * GET /notes  
    * DELETE /notes/{id}  
3. Test APIs using Postman or curl  
4. Push code to GitHub  
