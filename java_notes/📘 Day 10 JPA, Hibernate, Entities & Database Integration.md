# 📘 Day 10: JPA, Hibernate, Entities & Database Integration  
  
## 1. Why Do We Need JPA?  
Before JPA:  
* Writing raw JDBC  
* Boilerplate code  
* Manual ResultSet mapping  
* Hard to maintain  
JPA solves this by:  
* Mapping Java objects to DB tables  
* Handling SQL behind the scenes  
* Reducing boilerplate  
👉 Interview line:  
JPA is a specification for ORM, not an implementation.  
  
## 2. JPA vs Hibernate vs JDBC (Very Important)  
**JDBC**  
* Low-level API  
* Manual SQL  
* More control, more code  
**JPA**  
* Specification (rules/interfaces)  
* Defines how ORM should work  
**Hibernate**  
* JPA implementation  
* Most commonly used with Spring Boot  
👉 Interview-ready answer:  
JPA is a spec, Hibernate is an implementation, JDBC is a low-level API.  
  
## 3. ORM (Object Relational Mapping)  
ORM maps:  
* Java class → DB table  
* Field → column  
* Object → row  
So instead of SQL:  
```
INSERT INTO notes VALUES (...)

```
You write:  
```
noteRepository.save(note);

```
  
## 4. Entity Basics  
**Create Your First Entity**  
```
import jakarta.persistence.*;

@Entity
@Table(name = "notes")
public class Note {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;

    // getters & setters
}

```
Annotations explained:  
* @Entity → marks class as DB entity  
* @Table → maps to table  
* @Id → primary key  
* @GeneratedValue → auto-increment  
  
## 5. Repository with Spring Data JPA  
Instead of writing DAO code:  
```
@Repository
public interface NoteRepository extends JpaRepository<Note, Long> {
}

```
What you get for free:  
* save()  
* findById()  
* findAll()  
* deleteById()  
👉 Interview line:  
Spring Data JPA generates implementations at runtime.  
  
## 6. Service Layer  
```
@Service
public class NoteService {

    private final NoteRepository repository;

    public NoteService(NoteRepository repository) {
        this.repository = repository;
    }

    public Note save(Note note) {
        return repository.save(note);
    }

    public List<Note> getAll() {
        return repository.findAll();
    }

    public void delete(Long id) {
        repository.deleteById(id);
    }
}

```
  
## 7. Controller Layer  
```
@RestController
@RequestMapping("/notes")
public class NoteController {

    private final NoteService service;

    public NoteController(NoteService service) {
        this.service = service;
    }

    @PostMapping
    public Note create(@RequestBody Note note) {
        return service.save(note);
    }

    @GetMapping
    public List<Note> getAll() {
        return service.getAll();
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        service.delete(id);
    }
}

```
  
## 8. application.properties (Critical)  
```
spring.datasource.url=jdbc:mysql://localhost:3306/notes_db
spring.datasource.username=root
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

```
  
Key property:  
* ddl-auto=update  
    * create  
    * update  
    * validate  
    * none  
👉 Interview warning:  
Never use create or update blindly in production.  
  
## 9. Lazy vs Eager Loading  
**LAZY**  
* Loads data only when accessed  
* Better performance  
* Default for collections  
  
**EAGER**  
* Loads immediately  
* Can cause performance issues  
```
@OneToMany(fetch = FetchType.LAZY)

```
👉 Interview line:  
Prefer LAZY loading to avoid unnecessary DB calls.  
  
  
## 🔨 Project Task – Day 10  
**Notes API with MySQL**  
1. Create MySQL DB: notes_db  
2. Create Note entity  
3. Replace in-memory repo with JPA  
4. Test CRUD APIs  
5. Verify table auto-creation  
6. Push code to GitHub  
