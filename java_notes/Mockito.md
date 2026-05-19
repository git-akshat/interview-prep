# Mockito  
##  ✅ @Mock  
Creates a **fake (mocked) object**.  
You use it for **dependencies** that you don’t want to execute for real (DB, REST client, other services).  
**Example**  
```


@Mock
private OrderRepository orderRepository;


```
This means:  
👉 Mockito creates a dummy OrderRepository 👉 Real methods are NOT called 👉 You control behavior using when(...).thenReturn(...)  
  
## ✅ @InjectMocks  
Creates the **real object under test** and automatically **injects all mocks into it**.  
Usually applied to your **Service class**.  
**Example**  
```


@InjectMocks
private OrderService orderService;


```
Mockito will:  
* Create OrderService  
* Find matching @Mock fields  
* Inject them via constructor / setter / field  
  
## 🧠 Together (most common pattern)  
```


@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private PaymentClient paymentClient;

    @InjectMocks
    private OrderService orderService;

    @Test
    void createOrderTest() {

        when(orderRepository.save(any()))
                .thenReturn(new Order());

        Order order = orderService.createOrder();

        assertNotNull(order);
    }
}


```
  
## What happens internally?  
**Step 1**  
Mockito creates mocks:  
```


orderRepository (fake)
paymentClient (fake)


```
  
**Step 2**  
Mockito creates real service:  
```


orderService (real)


```
  
**Step 3**  
Mockito injects mocks into service:  
```


orderService.orderRepository = mock
orderService.paymentClient = mock


```
  
## 🎯 Key Difference  

| Annotation   | Purpose                              |
| ------------ | ------------------------------------ |
| @Mock        | Creates fake dependency              |
| @InjectMocks | Creates real class and injects mocks |
  
## ❗ Important Interview Gotchas  
  
## 1️⃣ @InjectMocks does NOT create mocks  
Only creates the **real object**.  
Mocks come ONLY from @Mock.  
  
## 2️⃣ Injection order  
Mockito tries:  
1. Constructor injection  
2. Setter injection  
3. Field injection  
(in that order)  
  
## 3️⃣ Must enable Mockito  
Either:  
**JUnit 5**  
```


@ExtendWith(MockitoExtension.class)


```
**OR JUnit 4**  
```


@RunWith(MockitoJUnitRunner.class)


```
Otherwise annotations won’t work.  
  
## ⭐ Simple Mental Model  
Think:  
****@Mock → fake objects****  
****@InjectMocks → real object using those fakes****  
  
## 💬 Perfect Interview Answer  
@Mock creates mocked dependencies, while @InjectMocks creates the real class under test and injects those mocks automatically. This allows isolated unit testing without calling actual external components.  
  
If you want next (high value):  
✅ @Spy vs @Mock ✅ @MockBean vs @Mock (Spring Boot) ✅ Unit vs Integration tests ✅ Common Mockito mistakes  
Just tell me 👍  
  
✅ @Spy vs @Mock ✅ @MockBean vs @Mock (Spring Boot) ✅ Unit vs Integration tests ✅ Common Mockito mistakes  
  
Perfect — let’s cover all four, clearly and interview-ready 👇 (I’ll keep it practical, Spring + Mockito focused.)  
  
## ✅ 1. @Spy vs @Mock  
**🔹 @Mock**  
Creates a **complete fake object**.  
* Real methods are NOT called  
* Everything must be stubbed  
```


@Mock
List<String> list;

list.add("A");        // does nothing
list.size();         // returns 0 unless stubbed


```
**Use when:**  
👉 You don’t care about real behavior 👉 Pure dependency isolation (most common)  
  
**🔹 @Spy**  
Creates a **partial mock** (real object + selective stubbing).  
Real methods ARE called unless overridden.  
```


@Spy
List<String> list = new ArrayList<>();

list.add("A");        // real add()
list.size();         // returns 1


```
Override specific behavior:  
```


doReturn(10).when(list).size();


```
  
**🎯 Difference (interview table)**  

| Feature             | @Mock | @Spy   |
| ------------------- | ----- | ------ |
| Real methods        | ❌ No  | ✅ Yes  |
| Needs constructor   | ❌     | ✅      |
| Partial mocking     | ❌     | ✅      |
| Risky in unit tests | Low   | Higher |
  
**💬 Interview line**  
@Mock creates a fully fake object, while @Spy wraps a real object and allows partial mocking.  
  
## ✅ 2. @MockBean vs @Mock (Spring Boot)  
Very important distinction.  
  
## 🔹 @Mock  
Mockito annotation.  
* Works ONLY in unit tests  
* Does NOT touch Spring context  
```


@Mock
OrderRepository repo;


```
Used with:  
```


@ExtendWith(MockitoExtension.class)


```
  
## 🔹 @MockBean  
Spring Boot annotation.  
* Replaces a real Spring Bean with a mock  
* Works in integration tests  
```


@SpringBootTest
class OrderServiceTest {

    @MockBean
    OrderRepository repo;
}


```
Spring injects this mock everywhere.  
  
**🎯 Difference**  

|                      | @Mock      | @MockBean         |
| -------------------- | ---------- | ----------------- |
| Framework            | Mockito    | Spring            |
| Loads Spring context | ❌          | ✅                 |
| Replaces bean        | ❌          | ✅                 |
| Used in              | Unit tests | Integration tests |
  
**💬 Interview line**  
@Mock is Mockito-only and used in unit tests, while @MockBean replaces a real Spring bean inside application context for integration testing.  
  
## ✅ 3. Unit Tests vs Integration Tests  
  
## 🔹 Unit Test  
Tests ONE class in isolation.  
* Uses @Mock  
* No Spring context  
* Very fast  
```


@ExtendWith(MockitoExtension.class)
class OrderServiceTest


```
**Example:**  
Testing only OrderService.  
  
## 🔹 Integration Test  
Tests multiple components together.  
* Uses @SpringBootTest  
* Real beans loaded  
* Can use @MockBean  
```


@SpringBootTest
class OrderControllerIT


```
  
**🎯 Comparison**  

|                | Unit Test    | Integration Test    |
| -------------- | ------------ | ------------------- |
| Scope          | Single class | Multiple components |
| Spring context | ❌            | ✅                   |
| Speed          | Fast         | Slow                |
| Uses           | @Mock        | @MockBean           |
| DB             | Fake         | Often real/test     |
  
**💬 Interview line**  
Unit tests validate isolated logic using mocks, while integration tests verify collaboration between components using Spring context.  
  
## ✅ 4. Common Mockito Mistakes (INTERVIEW GOLD)  
  
**❌ Mistake 1 — Forgetting Mockito extension**  
```


@ExtendWith(MockitoExtension.class)


```
Without this → mocks are null.  
  
**❌ Mistake 2 — Mixing @Mock with @SpringBootTest**  
Bad:  
```


@SpringBootTest
@Mock
Repo repo;


```
Spring won’t inject it.  
Use @MockBean.  
  
**❌ Mistake 3 — Using when() on spies**  
Wrong:  
```


when(spy.method()).thenReturn(x);


```
Real method executes first 😱  
Correct:  
```


doReturn(x).when(spy).method();


```
  
**❌ Mistake 4 — Over-mocking**  
Mocking everything = meaningless test.  
Mock ONLY external dependencies.  
  
**❌ Mistake 5 — Not verifying behavior**  
People forget:  
```


verify(repo).save(any());


```
  
**❌ Mistake 6 — Testing implementation instead of behavior**  
Avoid testing private methods.  
Test outcomes.  
  
## 🏆 Final Interview Summary (memorize)  
@Mock creates fake dependencies for unit testing, while @InjectMocks injects them into the real class. @Spy allows partial mocking. @MockBean replaces Spring beans in integration tests. Unit tests validate isolated logic, integration tests validate component collaboration. Common Mockito mistakes include missing extensions, mixing @Mock with SpringBootTest, improper spy stubbing, and over-mocking.  
  
If you’d like next (very useful):  
✅ Mockito verify examples ✅ ArgumentCaptor ✅ Testing Kafka consumers ✅ Controller tests with MockMvc  
Just tell me 👍  
  
  
  
**🧩 Simple Example**  
  
```
@Spy
List<String> list = new ArrayList<>();

list.add("A");              // REAL method
list.add("B");              // REAL method

doReturn(100).when(list).size();

System.out.println(list.size());   // MOCKED → 100
System.out.println(list.get(0));  // REAL → "A"

```
  
  
  
  
  
  
  
  
  
  
