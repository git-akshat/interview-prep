# 📘 Day 18: Caching – Spring Cache & Redis Basics (Restarted)  
  
## 1. Why Caching is Needed  
Without caching:  
* Every request hits the database  
* Higher latency  
* Poor scalability  
With caching:  
* Frequently used data is kept in memory  
* Faster responses  
* Reduced DB load  
👉 **Interview line:**  
Caching trades memory for performance.  
  
## 2. Where Caching Fits (High-Level Flow)  
```
Client → API → Cache → DB

```
* **Cache hit** → return data immediately  
* **Cache miss** → query DB → store result in cache → return data  
👉 **Key idea:** Most systems are **read-heavy**.  
  
## 3. Spring Cache Abstraction  
Spring provides a **cache abstraction layer**:  
* Same annotations  
* Works with multiple cache providers  
* Provider can be changed without code changes  
Enable caching:  
```
@EnableCaching

```
  
## 4. @Cacheable (Most Important Annotation)  
```
@Cacheable(value = "notes", key = "#id")
public Note getNote(Long id) {
    return repository.findById(id)
            .orElseThrow(() -> new RuntimeException("Not found"));
}

```
**What is value = "notes"?**  
* "notes" is the **cache name**  
* Think of it as a **bucket / region**  
* All note-related cached data goes here  
Conceptually:  
```
Cache Name: notes
--------------------
Key → Value
1   → Note(id=1)
2   → Note(id=2)

```
  
**What is key = "#id"?**  
* #id refers to the **method parameter**  
* It uniquely identifies the cached entry  
* Uses **Spring Expression Language (SpEL)**  
Calling:  
```
getNote(5)

```
Creates:  
```
notes[5] = Note(id=5)

```
  
**What happens internally?**  
1. Spring checks cache "notes" for key id  
2. If found → **method is skipped**  
3. If not found → DB call happens  
4. Result is cached and returned  
👉 **Interview line:**  
@Cacheable skips method execution on cache hit.  
  
## 5. Default Cache Behavior  
If you don’t configure any cache provider:  
* Spring uses a **simple in-memory ConcurrentHashMap**  
* Cache is lost on app restart  
* Works only within one application instance  
Good for learning & small apps.  
  
## 6. @CachePut (Update Cache Explicitly)  
Used when:  
* You always want method to run  
* But also want cache updated  
```
@CachePut(value = "notes", key = "#note.id")
public Note update(Note note) {
    return repository.save(note);
}

```
👉 Difference from @Cacheable:  
* Method **always executes**  
* Cache is updated with new value  
  
## 7. @CacheEvict (Remove Stale Cache)  
Used when:  
* Data is deleted  
* Cache must be cleared  
```
@CacheEvict(value = "notes", key = "#id")
public void deleteNote(Long id) {
    repository.deleteById(id);
}

```
If you forget this:  
* DB is updated  
* Cache still holds old data  
* API returns **stale data**  
  
## 8. Cache Eviction Strategies (Interview Topic)  
Common strategies:  
* **TTL (Time To Live)** – expire after fixed time  
* **LRU (Least Recently Used)**  
* **LFU (Least Frequently Used)**  
👉 **Interview line:**  
Eviction prevents stale data and unbounded cache growth.  
  
## 9. Redis Introduction  
Redis is:  
* In-memory data store  
* Extremely fast  
* Shared across multiple app instances  
Used for:  
* Distributed caching  
* Session storage  
* Rate limiting  
  
## 10. In-Memory Cache vs Redis  

| Feature      | In-Memory  | Redis         |
| ------------ | ---------- | ------------- |
| Scope        | Single app | Multiple apps |
| Persistence  | No         | Optional      |
| Scalability  | Low        | High          |
| Restart-safe | ❌          | ✅             |
  
👉 **Interview rule:**  
Single instance → in-memory cache Multiple instances → Redis  
  
## 11. Cache Consistency Problems  
Common issues:  
* Stale data  
* Missing eviction  
* Race conditions  
Solutions:  
* Proper @CacheEvict  
* Short TTL  
* Cache-aside strategy  
##   
  
## 🔨 Project Task – Day 18  
**Add Caching to Notes API**  
1. Enable caching  
2. Cache getNoteById  
3. Evict cache on update/delete  
4. Verify:  
    * First call → DB hit  
    * Second call → cache hit  
5. (Optional) Plug Redis later  
