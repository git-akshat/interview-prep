# 📘 Day 3: Collections Internals (HashMap, ConcurrentHashMap)  
  
## 1. Why Java Collections Exist  
Java Collections provide reusable, optimized data structures for storing and manipulating data.  
  
## 2. Core Collection Interfaces (Hierarchy)  
At a high level:  
* **Collection**  
    * List  
    * Set  
    * Queue  
* **Map** (does NOT extend Collection)  
  
Important ones you must know:  
* ArrayList  
* LinkedList  
* HashSet  
* HashMap  
* ConcurrentHashMap  
  
## 3. ArrayList vs LinkedList  
**ArrayList**  
* Backed by **dynamic array**  
* Fast random access → O(1)  
* Slow insert/delete in middle → shifting required  
**LinkedList**  
* Doubly linked list  
* Slow random access → O(n)  
* Faster insert/delete (if position known)  
  
👉 Interview rule:  
* Mostly read → **ArrayList**  
* Frequent insert/delete in middle → **LinkedList**  
  
## 4. HashMap – Core Concept  
A **HashMap** stores data as **key–value pairs**.  
Internally:  
* Uses an **array of buckets**  
* Each bucket can hold:  
    * Linked list (Java 7)  
    * Tree (Red-Black Tree, Java 8+ when collisions are high)  
  
## 5. How HashMap Works⭐️⭐️⭐️  
When you do:  
```
map.put(key, value);

```
Steps:  
1. key.hashCode() is called  
2. Hashcode is converted to bucket index  
3. If bucket is empty → store entry  
4. If bucket has entries:  
    * equals() is used to check key match  
    * If match → replace value  
    * Else → collision → add to bucket  
👉 Interview line:  
HashMap uses hashCode() to find bucket and equals() to identify the correct key.  
  
## 6. What Happens During Collision?  
```
"FB".hashCode() == "Ea".hashCode()

```
* Both go to same bucket  
* Stored as separate entries  
* Java 8+ converts bucket to tree if:  
    * Bucket size > 8  
    * Map size > 64  
This improves lookup from O(n) → O(log n).  
  
## 7. Why equals() and hashCode() Must Match  
Rule:  
* If a.equals(b) → a.hashCode() == b.hashCode()  
If violated:  
* HashMap cannot find keys  
* Data loss / duplicate keys  
  
## 8. Fail-Fast vs Fail-Safe Iterators  
**Fail-Fast**  
* ArrayList, HashMap  
* Throws ConcurrentModificationException  
* Detects structural modification during iteration  
```
list.forEach(x -> list.add(10)); // Exception

```
  
**Fail-Safe**  
* ConcurrentHashMap, CopyOnWriteArrayList  
* Works on copy  
* No exception, but may not reflect latest data  
  
## 9. HashMap vs ConcurrentHashMap  
**HashMap**  
* Not thread-safe  
* Faster in single-threaded use  
* Can cause infinite loop in multithreaded access (Java 7)  
  
**ConcurrentHashMap**  
* Thread-safe  
* Uses bucket-level locking (Java 8+)  
* No synchronized on entire map  
👉 Interview line:  
ConcurrentHashMap allows concurrent reads and controlled writes without locking the entire map.  
  
## 10. Implement Your Own HashMap (Core Coding)  
**Simple Custom HashMap (Conceptual)**  
```
class MyHashMap<K, V> {

    static class Entry<K, V> {
        K key;
        V value;
        Entry<K, V> next;

        Entry(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }

    private int capacity = 16;
    private Entry<K, V>[] table;

    public MyHashMap() {
        table = new Entry[capacity];
    }

    private int getIndex(K key) {
        return key.hashCode() % capacity;
    }

    public void put(K key, V value) {
        int index = getIndex(key);
        Entry<K, V> head = table[index];

        while (head != null) {
            if (head.key.equals(key)) {
                head.value = value;
                return;
            }
            head = head.next;
        }

        Entry<K, V> entry = new Entry<>(key, value);
        entry.next = table[index];
        table[index] = entry;
    }

    public V get(K key) {
        int index = getIndex(key);
        Entry<K, V> head = table[index];

        while (head != null) {
            if (head.key.equals(key))
                return head.value;
            head = head.next;
        }
        return null;
    }
}

```
👉 Purpose:  
* Understand collisions  
* Understand chaining  
* NOT for production  
  
##   
