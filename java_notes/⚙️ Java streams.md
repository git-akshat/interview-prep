# ⚙️ Java streams  
  
  
# Set A: Warm-up (basics)  
  
```
List<Integer> numbers =
    List.of(12, -3, 7, 9, 10, 15, 8, 7);

List<String> words =
    List.of("apple", "banana", "kiwi", "apple", "orange", "pear", "banana");

```
  
  
1. From a list of integers, return a list of **odd numbers sorted in descending order**.  
```
	List<Integer> oddNums =
    numbers.stream()
           .filter(n -> n % 2 != 0)
           .sorted(Comparator.reverseOrder())
           .toList();

```
  
2. Count how many strings in a list have **length greater than 5**.  
```
	long count =
    words.stream()
         .filter(word -> word.length() > 5)
         .count();

```
  
3. Check if **all numbers** in a list are positive.  
```
	boolean isAllPositive = 
    numbers.stream()
           .allMatch(n -> n>0);

```
  
4. Find the **sum of squares** of even numbers in a list.  
```
	int sum =
    numbers.stream()
           .filter(n -> n % 2 == 0)
           .mapToInt(n -> n * n)
           .sum();

```
  
5. From a list of strings, return a list of **non-repeating strings in uppercase**.  
```
	List<String> result =
    words.stream()
         .collect(Collectors.groupingBy(w -> w, Collectors.counting()))
         .entrySet()
         .stream()
         .filter(e -> e.getValue() == 1)
         .map(Map.Entry::getKey)
         .map(String::toUpperCase)
         .toList();

```
  
  
  
  
# Set B: Objects (very interview-relevant)  
Assume this class:  
```
class Employee {
    String name;
    String dept;
    int salary;

    // getters
}

List<Employee> employees = List.of(
    new Employee("Amit",   "IT",  90000),
    new Employee("Neha",   "HR",  45000),
    new Employee("Rahul",  "IT",  60000),
    new Employee("Priya",  "HR", 110000),
    new Employee("Suresh", "Sales", 50000),
    new Employee("Anita",  "Sales", 75000),
    new Employee("Vikas",  "IT",  30000),
    new Employee("Neha",   "HR",  70000)   // duplicate name on purpose
);

```
  
6. Get a list of employee names whose salary is **between 40k and 80k**.  
```
	List<Employee> employeeList = 
        employees.stream()
                 .filter(e -> e.getSalary() > 40000 && e.getSalary() < 80000)
                 .map(Employee::getName)
                 .toList();

```
  
7. Count employees in each department.  
```
	Map<String, Long) empCount =
        employees.stream()
                 .collect(
                     Collectors.groupingBy(
                      Employee::getDepartment(), 
                      Collectors.counting()
                  ));

```
  
8. Find the **average salary** of all employees.  
```
	double avg = 
        employees.stream()
                 .mapToInt(Employee::getSalary)
                 .average()
                 .orElse(0.0);

```
  
9. Find the **highest-paid employee** in each department.  
```
	Map<String, Optional<Employee>> highPayEmployees =
        employees.stream()
                 .collect(Collectors.groupingBy(
                              Employee::getDepartment(),
                              Collectors.maxBy(
                                  Comparator.comparingInt(Employee::getSalary)
                              )
                         )
                  );


    // without optional
    Map<String, Employee> highestPaidByDept =
    employees.stream()
             .collect(
                 Collectors.groupingBy(
                     Employee::getDept,
                     Collectors.collectingAndThen(
                         Collectors.maxBy(
                             Comparator.comparingInt(Employee::getSalary)
                         ),
                         Optional::get
                     )
                 )
             );


```
  
10. Check if **any employee** in the “HR” department earns more than 1 lakh.  
```
	boolean check = 
        employees.stream()
                .filter(e -> "HR".equals(e.getDepartment()))
                .anyMatch(e -> e.getSalary()>100000);

```
  
  
# Set C: Grouping & Mapping (expert territory)  
11. Group employees by department and return Map<String, List<String>> where value is **employee names**.  
```
	Map<String, List<String>> empDept = 
        employees.stream()
                 .collect(Collectors.groupingBy(
                          Employee::getDepartment,
                          Collectors.mapping(
                                employee -> employee.getName(), 
                                Collectors.toList())
                         )
                 );

```
  
  
12. Create a map of department → total salary.  
```
	Map<String, Integer> totalDeptSalary =
        employees.stream()
                 .collect(Collectors.groupingBy(
                          Employee::getDepartment,
                          Collectors.summingInt(Employee::getSalary)
                         )
                  );

```
  
  
13. Partition employees into **salary >= 60k** and **salary < 60k**.  
```
	Map<Boolean, List<Employee>> splitEmp =
        employees.stream()
                 .collect(Collectors.partitioningBy(
                          e -> e.getSalary >= 60000
                         )
                 );

```
  
  
14. Create a map of name → salary, handle duplicate names safely.  
```
// If the latest value should win:
Map<String, Integer> salaryMap =
    employees.stream()
             .collect(
                 Collectors.toMap(
                     Employee::getName,
                     Employee::getSalary,
                     (oldVal, newVal) -> newVal
                 )
             );


// If the highest salary should win (often better):
Map<String, Integer> salaryMap =
    employees.stream()
             .collect(
                 Collectors.toMap(
                     Employee::getName,
                     Employee::getSalary,
                     Integer::max
                 )
             );

// The second version is especially good in interviews.

```
#   
  
# Set D: Strings & characters (streams under pressure)  
  
15. Given a string, count frequency of each **word**.  
```
	String sentence =
    "java streams are powerful and java streams are tricky";

    Map<String, Integer> frequency =

```
```
        Arrays.stream(sentence.split(" "))
                .collect(Collectors.groupingBy(
                            Function.identity(),
                            Collectors.counting()
                        )
                );

```
  
16. Find the **first non-repeated character** in a string.  
```
	String text = "swiss";

    text.chars()

```
```
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
                Function.identity(),
                LinkedHashMap::new,
                Collectors.counting()
        ))
        .entrySet()
        .stream()
        .filter(ch -> ch.getValue() == 1)
        .map(Map.Entry::getKey)
        .findFirst()
        .ifPresent(System.out::println);

```
```

    

```
  
17. Find all characters that appear **more than once**.  
```
	String text = "swiss";

    List<Character> list =

```
```
    text.chars()
        .mapToObj(c -> (char)c)
        .collect(Collectors.groupingBy(
                        Function.identity(),
                        Collectors.counting()
                )
        )
        .entrySet()
        .stream()
        .filter(e -> e.getValue()>1)
        .map(Map.Entry::getKey)
        .toList();

```
  
18. Check if a string is a **palindrome** using streams.  
```
	String input = "madam";
    
    boolean isPalindrome =

```
```
        input.contentEquals(
                new StringBuilder(
                        input.chars()
                        .mapToObj(c -> String.valueOf((char) c))
                        .collect(Collectors.joining())
                ).reverse()
        );

```
```
    

    // alternate way
    // collect(supplier, accumulator, combiner) → low-level 
    // supplier -> new object initialization
    // accumulator -> what to do with new object
    // combiner -> If two StringBuilders were built separately, how do I merge them?
    boolean isPalindrome =

```
```
        input.equals(
                input.chars()
                        .mapToObj(c -> (char) c)
                        .collect(
                                StringBuilder::new,
                                StringBuilder::append,
                                StringBuilder::append
                        )
                        .reverse()
                        .toString()
        );

```
  
  
# Set E: Advanced thinking  
  
19. From a list of integers, find the **third highest distinct number**.  
```
    List<Integer> advancedNums =
    List.of(10, 20, 30, 40, 20, 50, 30, 60);


    // First way (with optional)
    Optional<Integer> thirdHighest =
    advancedNums.stream()
                .distinct()
                .sorted(Comparator.reverseOrder())
                .skip(2)
                .findFirst();

    // Second way (get integer value)	
    int thirdHighest = 
        advancedNums.stream()
                    .distinct()
                    .sorted(Comparator.reverseOrder())
                    .skip(2)
                    .findFirst()
                    .orElseThrow(() -> new RuntimeException("Not enough numbers"));

    // Third way (return default value)
    int thirdHighest =
    advancedNums.stream()
                .distinct()
                .sorted(Comparator.reverseOrder())
                .skip(2)
                .findFirst()
                .orElse(-1);


```
  
20. Flatten a List<List<String>>, remove duplicates, sort alphabetically, return as a list.  
```
	List<List<String>> nested =
    List.of(
        List.of("banana", "apple"),
        List.of("orange", "banana"),
        List.of("kiwi", "apple")
    );

    List<String> flatlist =

```
```
        nested.stream()
                .flatMap(List::stream)
                .distinct()
                .sorted()
                .toList();

```
  
21. Given employees, find the department with the **lowest average salary**.  
```
	employees.stream()

```
```
        .collect(Collectors.groupingBy(
                    Employee::getDept,
                    Collectors.averagingInt(Employee::getSalary)
        ))
        .entrySet()
        .stream()
        .min(Comparator.comparingDouble(Map.Entry::getValue))
        .map(Map.Entry::getKey)
        .ifPresent(System.out::println);

```
```

// averagingInt returns double value

```
  
22. Given a list of numbers, find **all pairs** whose sum is 10 (stream solution only, no nested loops).  
```
	List<Integer> pairNums =
    List.of(1, 2, 3, 7, 8, 9, 5, 5, 4, 6);

    Set<Integer> lookup = new HashSet<>(pairNums);

```
```

    List<List<Integer>> pair =
        pairNums.stream()
                .filter(x -> lookup.contains(10-x))
                .filter(x -> x <= 10 - x)
                .map(x -> List.of(x, 10-x))
                .toList();       

```
  
# ✅ 1. Merge + Sort + Remove Duplicates (int[])  
```
int[] a = {5, 2, 9, 2};
int[] b = {1, 7, 3, 5};

int[] result =
        IntStream.concat(Arrays.stream(a), Arrays.stream(b))
                 .distinct()
                 .sorted()
                 .toArray();

System.out.println(Arrays.toString(result));


```
**Output**  
```
[1, 2, 3, 5, 7, 9]

```
  
# ✅ 2. Merge + Sort + Distinct (Integer[])  
```
Integer[] a = {5, 2, 9, 2};
Integer[] b = {1, 7, 3, 5};

Integer[] result =
        Stream.concat(Arrays.stream(a), Arrays.stream(b))
              .distinct()
              .sorted()
              .toArray(Integer[]::new);


```
  
