# 📘 Day 13: Spring Data JPA Queries, Pagination & Sorting  
  
## 1. Why Spring Data JPA is Powerful  
  
Without Spring Data JPA:  
* Write SQL / JPQL(Java Persistence Query Language) manually  
* Boilerplate DAO code  
* Hard to maintain  
  
With Spring Data JPA:  
* Query methods from method names  
* Pagination & sorting built-in  
* Less code, more clarity  
  
👉 Interview line:  
Spring Data JPA reduces boilerplate by generating queries at runtime.  
  
## 2. Derived Query Methods (Very Important)  
Spring generates queries based on **method names**.  
```
public interface NoteRepository
        extends JpaRepository<Note, Long> {

    List<Note> findByTitle(String title);

    List<Note> findByTitleContaining(String keyword);

    List<Note> findByCreatedAtAfter(LocalDate date);

    List<Note> findByTitleAndContent(String title, String content);
}

```
  
Naming rules:  
* findBy  
* Property names must match entity fields  
* Keywords like:  
    * And, Or  
    * Containing  
    * Between  
    * After, Before  
  
👉 Interview tip:  
Method names are parsed to generate JPQL queries.  
  
## 3. @Query Annotation (Custom Queries)  
When method names get too complex, use @Query.  
  
**JPQL Example**  
```
@Query("SELECT n FROM Note n WHERE n.title LIKE %:keyword%")
List<Note> search(@Param("keyword") String keyword);

```
  
JPQL:  
* Uses **entity names**, not table names  
* DB-independent  
  
**Native Query**  
```
@Query(value =
  "SELECT * FROM notes WHERE title LIKE %?1%",
  nativeQuery = true)
List<Note> nativeSearch(String keyword);

```
  
👉 Interview line:  
Prefer JPQL; use native queries only when required.  
  
## 4. Pagination (Extremely Important)  
Never return **huge lists** in real APIs.  
Spring provides Pageable.  
  
**Repository Method**  
```
Page<Note> findAll(Pageable pageable);

```
  
**Controller Usage**  
```
@GetMapping
public Page<Note> getNotes(
        @RequestParam int page,
        @RequestParam int size) {

    Pageable pageable =
            PageRequest.of(page, size);

    return repository.findAll(pageable);
}

```
  
Page contains:  
* Content  
* Total elements  
* Total pages  
* Page number  
  
## 5. Sorting  
**Single Field Sorting**  
```
Sort sort = Sort.by("createdAt").descending();
List<Note> notes = repository.findAll(sort);

```
  
**Pagination + Sorting**  
```
Pageable pageable =
    PageRequest.of(page, size,
        Sort.by("title").ascending());

```
##   
## 6. Page vs Slice vs List  
****⭐️Page****  
* Total count query  
* Slower  
* More metadata  
```
Pageable pageable = PageRequest.of(10, 0); // page, size
Page<Employee> page = employeeRepository.findByDepartment("IT", pageable);

List<Employee> employees = page.getContent();
long totalRecords = page.getTotalElements();
int totalPages = page.getTotalPages();

SQL behavior (IMPORTANT)
-- data query
SELECT * FROM employee WHERE department = 'IT' LIMIT 10 OFFSET 0;

-- count query
SELECT COUNT(*) FROM employee WHERE department = 'IT';


```
  
****⭐️Slice****  
* No total count  
* Faster  
* Good for infinite scroll  
```
Repository
----------
Slice<Employee> findByDepartment(String department, Pageable pageable);

Service usage
-------------
Pageable pageable = PageRequest.of(0, 10);
Slice<Employee> slice = employeeRepository.findByDepartment("IT", pageable);

List<Employee> employees = slice.getContent();
boolean hasNext = slice.hasNext();

SQL behavior
------------
SELECT * FROM employee WHERE department = 'IT' LIMIT 11 OFFSET 0;


```
  
****⭐️List****  
* No pagination  
* Risky for large data  
```
Repository
-------------
List<Employee> findByDepartment(String department);

Service usage
-------------
List<Employee> employees = employeeRepository.findByDepartment("IT");

SQL behavior
-------------
SELECT * FROM employee WHERE department = 'IT';

```
  
👉 Interview line:  
Use Slice when total count is not required.  
  
  

| Feature         | List | Page | Slice |
| --------------- | ---- | ---- | ----- |
| Pagination      | ❌    | ✅    | ✅     |
| Total count     | ❌    | ✅    | ❌     |
| COUNT query     | ❌    | ✅    | ❌     |
| Performance     | POOR | SLOW | FAST  |
| Infinite scroll | ❌    | ❌    | ✅     |
  
##   
  
## 🔨 Project Task – Day 13  
**Enhance Notes API**  
1. Add search by title (keyword)  
2. Add pagination (page, size)  
3. Add sorting (by createdAt)  
4. Use:  
    * Derived query  
    * One @Query  
5. Test with large dataset  
