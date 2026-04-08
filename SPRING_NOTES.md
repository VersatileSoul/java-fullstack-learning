## Spring Boot Handbook (Long-Term Revision Notes)

This file is designed as a one-stop reference for your Spring journey.
Goal: if you read this after months, you should still be able to rebuild and explain a Spring Boot backend confidently.

---

## Part A - Foundations

### 1) What is Spring?

Spring is a Java ecosystem used to build scalable applications.
It solves complexity in enterprise Java by giving:
- IoC container
- dependency injection
- modules for web, data, security, cloud, testing

Core ecosystem examples:
- Spring Framework (core)
- Spring Boot (rapid setup)
- Spring Data JPA (database abstraction)
- Spring Security (auth/authz)
- Spring Cloud (microservices/cloud tooling)

Snippet:
```java
// Think in modules, not one big framework:
// Web + Data JPA + Security can be combined in one app.
```

### 2) Spring vs Spring Boot

Both use same core ideas.
Difference is developer experience:
- Spring (old style): more manual configuration (XML/Java config)
- Spring Boot: auto-configuration + conventions + starters

Snippet:
```java
// Spring Boot app start
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### 3) IoC and DI (must-know)

IoC = you give object creation/control to Spring container.
DI = practical way Spring injects required dependencies.

Without DI:
```java
Laptop laptop = new Laptop();
```

With DI:
```java
public Dev(Computer computer) {
    this.computer = computer;
}
```

### 4) Bean lifecycle mental model

1. App starts  
2. Spring container starts  
3. Beans are created/scanned  
4. Dependencies are injected  
5. App serves requests  

Snippet:
```text
start -> create beans -> inject deps -> run logic
```

### 5) Injection styles

- Field injection (quick demo style)
- Setter injection (optional dependencies)
- Constructor injection (recommended in real projects)

Rule:
- One constructor -> `@Autowired` optional
- Multiple constructors -> mark target constructor with `@Autowired`

### 6) Multiple bean ambiguity: @Primary vs @Qualifier

If two classes implement same interface, Spring needs help.

Use:
- `@Primary` for default global choice
- `@Qualifier` for specific choice at injection point

Snippet:
```java
@Component
@Primary
class Laptop implements Computer {}

@Component
class Desktop implements Computer {}

@Component
class Dev {
    Dev(@Qualifier("desktop") Computer computer) {}
}
```

---

## Part B - Old Spring (XML) Essentials

### 7) Why still learn old Spring XML?

- Legacy codebases still use it.
- Helps understand what Boot automates.

### 8) XML bean basics

`<bean>` defines a managed object.
- `id` = bean name
- `class` = implementation class

Snippet:
```xml
<bean id="dev1" class="org.example.Dev"/>
<bean id="lap1" class="org.example.Laptop"/>
```

### 9) XML setter and constructor injection

Setter:
```xml
<bean id="dev1" class="org.example.Dev">
    <property name="age" value="12"/>
    <property name="laptop" ref="lap1"/>
</bean>
```

Constructor:
```xml
<bean id="dev1" class="org.example.Dev">
    <constructor-arg index="0" value="10"/>
    <constructor-arg index="1" ref="lap1"/>
</bean>
```

### 10) XML autowire

- `autowire="byName"` -> property name must match bean id
- `autowire="byType"` -> matches by type, may fail if multiple candidates

Snippet:
```xml
<bean id="comp" class="org.example.Laptop"/>
<bean id="dev1" class="org.example.Dev" autowire="byName"/>
```

---

## Part C - Spring Boot Web + MVC

### 11) Why Spring Boot MVC?

Console app concepts are not enough for real backend.
For HTTP request/response flow you need MVC-style web architecture.

### 12) Web request flow

Client -> DispatcherServlet (front controller) -> Controller -> Service -> Repository -> DB

Back to client as JSON.

### 13) @Controller vs @RestController

- `@Controller` returns view/template by default
- `@RestController` returns response body (JSON/text)

Snippet:
```java
@RestController
public class HomeController {
    @GetMapping("/")
    public String greet() {
        return "Welcome";
    }
}
```

### 14) Multiple controllers

You can have many controllers.
DispatcherServlet maps each request to correct method using mappings.

### 15) Layer responsibilities

- Controller: request/response only
- Service: business logic
- Repository: database interaction
- Model/Entity: data structure

Do not write heavy logic inside controller.

### 16) Package structure recommendation

```text
com.example.app
  |- controller
  |- service
  |- repository
  |- model (or entity)
  |- dto
  |- exception
  |- config
```

### 17) Port and app config

`application.properties`:
```properties
server.port=8090
```

---

## Part D - REST + CRUD + Postman

### 18) HTTP methods and CRUD mapping

- `GET` -> Read
- `POST` -> Create
- `PUT` -> Update
- `DELETE` -> Delete

### 19) Status codes quick map

- `200 OK` success
- `201 Created` for successful creation
- `204 No Content` for successful delete/update when no body
- `400 Bad Request` invalid request
- `404 Not Found` resource not found
- `500 Internal Server Error` server issue

### 20) Get all + get by id

Snippet:
```java
@GetMapping("/products")
public List<Product> getProducts() { return service.getProducts(); }

@GetMapping("/products/{id}")
public Product getProduct(@PathVariable int id) { return service.getProductById(id); }
```

### 21) Create via POST

Use `@RequestBody` to convert incoming JSON -> Java object.

Snippet:
```java
@PostMapping("/products")
public Product addProduct(@RequestBody Product product) {
    return service.addProduct(product);
}
```

### 22) Update via PUT

Snippet:
```java
@PutMapping("/products/{id}")
public Product updateProduct(@PathVariable int id, @RequestBody Product product) {
    return service.updateProduct(id, product);
}
```

### 23) Delete via DELETE

Snippet:
```java
@DeleteMapping("/products/{id}")
public void deleteProduct(@PathVariable int id) {
    service.deleteProduct(id);
}
```

### 24) Common Postman mistakes

- Sending text instead of JSON
- Missing `@RequestBody`
- Wrong HTTP method for endpoint
- Wrong URL path variable format

---

## Part E - Spring Data JPA + H2

### 25) Why JPA?

Without JPA: too much boilerplate (manual SQL/JDBC flow).
With Spring Data JPA: repository methods for common CRUD with minimal code.

### 26) ORM mapping idea

Object world to table world:
- Class -> Table
- Field -> Column
- Object instance -> Row

### 27) JPA vs Hibernate

- JPA = specification (standard)
- Hibernate = implementation
- Spring Data JPA = easy repository abstraction over JPA

### 28) Add dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

### 29) H2 + datasource configuration

```properties
spring.datasource.url=jdbc:h2:mem:telusko
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
spring.jpa.show-sql=true
```

### 30) Entity basics

Snippet:
```java
@Entity
public class Product {
    @Id
    private Integer productId;
    private String productName;
    private Integer price;
}
```

### 31) Repository interface

Snippet:
```java
public interface ProductRepo extends JpaRepository<Product, Integer> {
}
```

### 32) Service using repository

Snippet:
```java
@Service
public class ProductService {
    private final ProductRepo repo;

    public ProductService(ProductRepo repo) {
        this.repo = repo;
    }

    public List<Product> getProducts() {
        return repo.findAll();
    }

    public Product getProductById(int id) {
        return repo.findById(id).orElse(null);
    }

    public Product addProduct(Product product) {
        return repo.save(product);
    }

    public Product updateProduct(int id, Product product) {
        product.setProductId(id);
        return repo.save(product);
    }

    public void deleteProduct(int id) {
        repo.deleteById(id);
    }
}
```

---

## Part F - Must-Have Topics You Were Missing (Added)

### 33) Validation (important)

Use Bean Validation for request correctness.

Snippet:
```java
public class ProductRequest {
    @NotBlank
    private String productName;

    @Min(1)
    private Integer price;
}
```

Controller:
```java
@PostMapping("/products")
public ResponseEntity<Product> addProduct(@Valid @RequestBody ProductRequest request) {
    // map DTO -> entity and save
    return ResponseEntity.status(HttpStatus.CREATED).build();
}
```

### 34) Global exception handling

Use `@ControllerAdvice` for clean API errors.

Snippet:
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(NoSuchElementException.class)
    public ResponseEntity<String> handleNotFound(NoSuchElementException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

### 35) ResponseEntity and proper status codes

Do not always return raw objects.
Use `ResponseEntity` for clear HTTP behavior.

Snippet:
```java
return ResponseEntity.status(HttpStatus.CREATED).body(savedProduct);
```

### 36) DTO pattern (very important)

Do not expose entity directly in public API.
Use:
- Request DTO
- Response DTO

Benefits:
- Security
- API stability
- cleaner separation of concerns

### 37) JPA relationships

Know basics:
- `@OneToMany`
- `@ManyToOne`
- `@OneToOne`
- `@ManyToMany`
- `fetch` and `cascade`

Starter snippet:
```java
@ManyToOne
@JoinColumn(name = "category_id")
private Category category;
```

### 38) Transactions

Use `@Transactional` when multiple DB actions must succeed/fail together.

Snippet:
```java
@Transactional
public void transferOperation() {
    // multiple repository calls
}
```

### 39) Pagination and sorting

Snippet:
```java
Page<Product> page = repo.findAll(PageRequest.of(0, 10, Sort.by("price").descending()));
```

### 40) Custom queries

Method-name query:
```java
List<Product> findByProductNameContainingIgnoreCase(String keyword);
```

Custom JPQL:
```java
@Query("select p from Product p where p.price > :minPrice")
List<Product> findExpensiveProducts(@Param("minPrice") Integer minPrice);
```

### 41) Profiles and environment config

Use profiles:
- `application-dev.properties`
- `application-prod.properties`

Activate:
```properties
spring.profiles.active=dev
```

### 42) CORS basics (for React frontend)

Snippet:
```java
@CrossOrigin(origins = "http://localhost:5173")
@RestController
public class ProductController { }
```

Prefer central config in production apps.

### 43) Testing essentials

Minimum:
- Service unit test
- Controller integration test
- Repository integration test

Starter annotations:
- `@SpringBootTest`
- `@WebMvcTest`
- `@DataJpaTest`

### 44) Security foundation (next major milestone)

Learn:
- Spring Security filter chain basics
- authentication vs authorization
- JWT flow (login, token, protected endpoints)

### 45) Production readiness checklist

- centralized exception responses
- input validation
- no hardcoded secrets
- environment-specific config
- logs with clear levels
- health checks (Actuator)
- proper DB migration tool (Flyway/Liquibase)

---

## Part G - Recommended Learning Path from Here

### 46) Immediate next (to become job-ready faster)

1. Add validation + exception handling to your current product API  
2. Convert raw entity API to DTO-based API  
3. Add pagination + search endpoint  
4. Switch H2 to PostgreSQL  
5. Add Spring Security + JWT  
6. Add tests for core flows  

### 47) Your current level summary

You already understand:
- DI and autowiring basics
- MVC layering
- REST CRUD
- Postman workflow
- JPA repository basics

You are now in the transition from learner -> implementer.

### 48) Final revision card (read before interviews)

```text
Spring Core: IoC + DI
Spring Boot: auto-configuration + starter dependencies + embedded server
Spring MVC: request mapping + controller flow
Spring Data JPA: repository abstraction for CRUD
Production quality: validation + exception handling + security + tests
```

---

If you want next, I can create:
- `SPRING_QUICK_REVISION_1_PAGE.md` (ultra short)
- `SPRING_INTERVIEW_QA.md` (question-answer style, chapter-wise)
## 1) What is Spring?

Spring is a Java ecosystem for building scalable applications (especially backend/enterprise apps).  
It started to solve complexity in older Java enterprise development.

Important idea:
- `Spring` is not just one library now.
- It is an umbrella with many projects: Spring Framework, Spring Boot, Spring Data, Spring Security, Spring Cloud, etc.

Snippet:
```java
// One project can use different Spring modules together
// Example: Spring Web + Spring Data JPA + Spring Security
```

## 2) Why people use Spring

- Helps manage large applications with clean architecture.
- Handles object lifecycle and dependency management.
- Provides modules for web APIs, databases, security, cloud, and more.

Snippet:
```java
// Typical layered flow in Spring apps
// Controller -> Service -> Repository
```

## 3) Prerequisites and tools

Core things you should know:
- Java syntax + OOP + interfaces + collections + exceptions.
- Build tools: Maven/Gradle.
- Basic database concepts (JDBC/Hibernate awareness helps).
- JSON and XML basics.

Tools:
- JDK 17+ (Spring 6 / Boot 3 requires this baseline).
- IDE: IntelliJ / Eclipse / VS Code (IntelliJ is very common for Spring).

Snippet:
```xml
<!-- pom.xml -->
<properties>
    <java.version>17</java.version>
</properties>
```

## 4) IoC and DI (most important foundation)

### IoC (Inversion of Control)
Normally, you create objects with `new`.  
With IoC, you give that control to Spring.

### DI (Dependency Injection)
DI is the practical way to achieve IoC.  
Instead of creating dependencies manually, Spring injects them for you.

Simple mental model:
- Your class says: "I need X"
- Spring says: "I will provide X"

Snippet:
```java
// Without DI
Laptop laptop = new Laptop();

// With DI (Spring injects Laptop/Computer automatically)
public Dev(Computer computer) {
    this.computer = computer;
}
```

## 5) What is Spring Boot?

Spring Boot = Spring Framework with strong defaults and auto-configuration.

Why Boot matters:
- Faster project setup.
- Less manual configuration.
- Embedded server (Tomcat) for web apps.
- Easy start via `start.spring.io`.

Key point:
- Boot is not separate from Spring; it sits on top of Spring to reduce boilerplate.

Snippet:
```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

## 6) DI using Spring Boot (container + bean basics)

When app starts, Spring creates an IoC container.

You can think:
- App classes exist in your project.
- Spring container decides which classes become managed objects (`beans`).
- You request beans from container when needed.

If bean not found:
- Error appears because class is not registered as a Spring-managed component.

Snippet:
```java
ApplicationContext context = SpringApplication.run(DemoApplication.class, args);
Dev dev = context.getBean(Dev.class);
dev.build();
```

## 7) Autowiring in Spring Boot

Autowiring means Spring automatically connects dependencies.

You learned 3 DI styles:
- Field injection
- Constructor injection
- Setter injection

Practical recommendation:
- Prefer constructor injection in real projects.
- Field injection works for learning but is less ideal for testing/clean design.

Snippet:
```java
@Component
public class Dev {
    private final Computer computer;

    public Dev(Computer computer) { // no @Autowired needed for single constructor
        this.computer = computer;
    }
}
```

## 8) Multiple implementations problem (Laptop/Desktop type confusion)

When one interface has multiple implementations:
- Spring sees multiple matching beans.
- It cannot choose automatically (ambiguous dependency error).

How to resolve:
- `@Primary` -> default choice globally.
- `@Qualifier` -> explicit bean choice at injection point.

Important:
- Type-based injection happens first.
- If ambiguous, Spring may try name matching.

Snippet:
```java
@Component
@Primary
class Laptop implements Computer {}

@Component
class Desktop implements Computer {}

@Component
class Dev {
    Dev(@Qualifier("desktop") Computer computer) {}
}
```

## 9) Spring without Boot (XML style)

Without Boot, you do manual setup:
- Create Maven project.
- Add Spring dependencies yourself.
- Define bean wiring in XML (`spring.xml`).
- Create container explicitly (`ApplicationContext`).

Why this still matters:
- Many legacy projects still use XML.
- It helps you understand what Boot automates.

Snippet:
```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
Dev dev = context.getBean("dev1", Dev.class);
```

## 10) Spring XML configuration basics

In XML, each managed object is a `<bean>`.

You define:
- bean id
- bean class

Then Spring creates those objects in container.

No bean definition -> no bean in container.

Snippet:
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dev1" class="org.example.Dev"/>
    <bean id="lap1" class="org.example.Laptop"/>
</beans>
```

## 11) Constructor injection and Setter injection in XML

### Setter injection (property)
- Uses `<property .../>`
- Good when dependency/value is optional

### Constructor injection
- Uses `<constructor-arg .../>`
- Good when dependency is required at object creation

### Primitive vs reference in XML
- Primitive: use `value`
- Object reference: use `ref`

Example thinking:
- `age` -> `value`
- `laptop`/`computer` -> `ref`

Snippet:
```xml
<!-- Setter injection -->
<bean id="dev1" class="org.example.Dev">
    <property name="age" value="12"/>
    <property name="laptop" ref="lap1"/>
</bean>
```

```xml
<!-- Constructor injection -->
<bean id="dev2" class="org.example.Dev">
    <constructor-arg index="0" value="10"/>
    <constructor-arg index="1" ref="lap1"/>
</bean>
```

## 16) Need for Spring Boot MVC (Chapter 12)

Core message from this chapter:
- Spring and Spring Boot can do the same core DI tasks.
- The major difference is configuration style:
  - Old Spring: XML-heavy configuration
  - Spring Boot: annotation-driven + convention-based
- Spring Boot reduces setup friction and debugging overhead for configuration.

Why MVC is needed now:
- Earlier examples were mostly console-style.
- Real projects are web apps (HTTP request/response).
- For web flow, you need MVC concepts and controller mapping.

Snippet:
```java
// Old style (without Boot): manual container + XML
ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");

// Boot style: one-line startup + annotation-based config
SpringApplication.run(DemoApplication.class, args);
```

## 17) Spring Boot Web (Chapter 13)

What changes when moving to web:
- You build HTTP endpoints.
- Client can be browser, React app, or mobile app.
- Backend usually returns data (`JSON`), not UI pages.
- Embedded Tomcat starts with your Boot app.

Important behavior:
- If app starts but no endpoint is mapped, root URL gives `404`.
- A plain `@Controller` + returned string is treated as a view name.
- For API responses, use `@RestController` (or `@ResponseBody`).

Snippet:
```java
@RestController
public class HomeController {

    @RequestMapping("/")
    public String greet() {
        return "Welcome";
    }

    @RequestMapping("/about")
    public String about() {
        return "We don't teach, we educate";
    }
}
```

Multiple controllers are valid:
- Spring's front controller (`DispatcherServlet`) receives request first.
- It routes request to the right controller method using mappings.

Snippet:
```java
@RestController
public class LoginController {
    @RequestMapping("/login")
    public String login() {
        return "Login page";
    }
}
```

## 18) Spring MVC and Layers (Chapter 14)

Main idea:
- Do not keep logic in controller.
- Use layered architecture:
  - Controller: request/response
  - Service: business logic
  - Repository: data access (later with DB)
  - Model: data structures/entities

Typical package structure:
- `controller`
- `service`
- `model`
- `repository` (later)

Returning object/list:
- `@RestController` auto-converts Java object/list to JSON.
- This is what frontend apps consume.

Snippet:
```java
// model/Product.java
public class Product {
    private int productId;
    private String productName;
    private int price;

    public Product(int productId, String productName, int price) {
        this.productId = productId;
        this.productName = productName;
        this.price = price;
    }
}
```

```java
// service/ProductService.java
@Service
public class ProductService {
    public List<Product> getProducts() {
        return Arrays.asList(
            new Product(101, "iPhone", 50000),
            new Product(102, "Canon Camera", 70000),
            new Product(103, "Shure Mic", 10000)
        );
    }
}
```

```java
// controller/ProductController.java
@RestController
public class ProductController {
    private final ProductService service;

    public ProductController(ProductService service) {
        this.service = service;
    }

    @RequestMapping("/products")
    public List<Product> getProducts() {
        return service.getProducts();
    }
}
```

Practical notes from debugging in this chapter:
- Port conflict (`8080` already in use) is common.
- Change port in `application.properties` when needed.
- If IDE behaves oddly, restart app/IDE/system and rerun cleanly.

Snippet:
```properties
server.port=8090
```

## 19) Spring Web HTTP methods GET and PUT (Chapter 15)

Core idea:
- REST APIs use HTTP methods:
  - `GET` -> read/fetch
  - `POST` -> create
  - `PUT` -> update
  - `DELETE` -> remove
- Browser URL bar mostly helps with `GET`.
- For `POST/PUT/DELETE`, use API tools like Postman (or frontend code).

Status code recap:
- `2xx` -> success (`200 OK`)
- `4xx` -> client-side issue (bad request, not found, wrong method usage)
- `5xx` -> server-side issue

Fetch one record by ID:
- Endpoint style: `/products/{id}`
- Use `@PathVariable` to map URL id to method parameter.

Snippet:
```java
@GetMapping("/products/{prodId}")
public Product getProductById(@PathVariable int prodId) {
    return service.getProductById(prodId);
}
```

Service-side filtering approach from this chapter:

Snippet:
```java
public Product getProductById(int prodId) {
    return products.stream()
        .filter(p -> p.getProductId() == prodId)
        .findFirst()
        .orElse(null);
}
```

Add product (`POST`):
- Request body JSON must be mapped into Java object.
- Use `@RequestBody` for that mapping.
- Jackson (from `spring-boot-starter-web`) handles JSON <-> Java conversion.

Snippet:
```java
@PostMapping("/products")
public void addProduct(@RequestBody Product prod) {
    service.addProduct(prod);
}
```

```java
public void addProduct(Product prod) {
    products.add(prod);
}
```

Important pitfall from this chapter:
- `Arrays.asList(...)` gives fixed-size list; adding/removing causes errors.
- Use mutable list when supporting add/update/delete.

Snippet:
```java
private List<Product> products = new ArrayList<>(Arrays.asList(
    new Product(101, "iPhone", 50000),
    new Product(102, "Canon Camera", 70000),
    new Product(103, "Shure Mic", 10000)
));
```

## 20) Spring Web Update and DELETE (Chapter 16)

Update flow (`PUT`):
- Keep same URL resource (`/products`) and change method to `PUT`.
- Accept full product JSON in request body.
- Replace matching product in list.

Snippet:
```java
@PutMapping("/products")
public void updateProduct(@RequestBody Product prod) {
    service.updateProduct(prod);
}
```

```java
public void updateProduct(Product prod) {
    int index = -1;
    for (int i = 0; i < products.size(); i++) {
        if (products.get(i).getProductId() == prod.getProductId()) {
            index = i;
            break;
        }
    }
    if (index != -1) {
        products.set(index, prod);
    }
}
```

Delete flow (`DELETE`):
- Common endpoint style: `/products/{prodId}`
- Use `@PathVariable` to delete by ID.

Snippet:
```java
@DeleteMapping("/products/{prodId}")
public void deleteProduct(@PathVariable int prodId) {
    service.deleteProduct(prodId);
}
```

```java
public void deleteProduct(int prodId) {
    int index = -1;
    for (int i = 0; i < products.size(); i++) {
        if (products.get(i).getProductId() == prodId) {
            index = i;
            break;
        }
    }
    if (index != -1) {
        products.remove(index);
    }
}
```

Common mistakes highlighted in this chapter:
- Wrong content type in Postman (`Text` instead of `JSON`) -> media type errors.
- Missing `@RequestBody` -> request body not mapped to Java object.
- Method not allowed -> endpoint exists but wrong HTTP method.
- Missing mapping (`@PutMapping`/`@DeleteMapping`) -> 404/405 behavior.

## 21) Spring Data JPA (Chapter 17)

Why this chapter matters:
- Until now, data is hardcoded in service layer.
- Real apps should persist data in a database.
- Repository layer is responsible for DB interaction.

Evolution (high-level):
- JDBC -> a lot of boilerplate code
- Spring JDBC -> less boilerplate
- ORM + JPA -> object-centric approach, much easier CRUD

Key concepts:
- ORM maps Java object <-> relational table.
- `Class` name -> table name (default)
- Fields -> columns
- Each object instance -> one row

JPA vs Hibernate:
- JPA = specification/standard (`Jakarta Persistence API`)
- Hibernate = one implementation of JPA
- Spring Data JPA builds on this and gives very fast CRUD development

Snippet:
```text
Object world                 DB world
Product class  ---------->   product table
productId field ---------->  product_id column
Product object ----------->  one row
```

## 22) Spring Data JPA + H2 Setup (Chapter 18)

Dependencies needed:
- `spring-boot-starter-data-jpa`
- `h2`

Common setup flow:
1) Add dependencies
2) Configure datasource URL (and optional username/password)
3) Start app
4) Open H2 console and verify connection

Snippet (`pom.xml`):
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

Snippet (`application.properties`):
```properties
spring.datasource.url=jdbc:h2:mem:telusko
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

Optional debug + SQL visibility:
```properties
spring.jpa.show-sql=true
```

Access H2 console:
- `http://localhost:8090/h2-console` (use your running port)

Important pitfall from this chapter:
- If `h2` dependency scope is wrong, driver may not load at runtime.

## 23) Spring Data JPA with JpaRepository (Chapter 19)

Main shift:
- Replace manual list logic in service with repository calls.
- Create repository as an interface; Spring creates implementation automatically.

Steps:
1) Create `repository` package
2) Add `ProductRepo` interface
3) Extend `JpaRepository<Product, Integer>`
4) Inject repo in service
5) Replace CRUD code with built-in methods

Snippet:
```java
public interface ProductRepo extends JpaRepository<Product, Integer> {
}
```

Entity requirements:
- Mark model class as `@Entity`
- Mark primary key field as `@Id`

Snippet:
```java
@Entity
public class Product {
    @Id
    private int productId;
    private String productName;
    private int price;
}
```

Service with repository:
```java
@Service
public class ProductService {
    @Autowired
    private ProductRepo repo;

    public List<Product> getProducts() {
        return repo.findAll();
    }

    public Product getProductById(int prodId) {
        return repo.findById(prodId).orElse(null);
    }

    public void addProduct(Product prod) {
        repo.save(prod);
    }

    public void updateProduct(Product prod) {
        repo.save(prod); // same method for insert/update
    }

    public void deleteProduct(int prodId) {
        repo.deleteById(prodId);
    }
}
```

Why this is powerful:
- No manual SQL for basic CRUD
- Cleaner service layer
- Much less boilerplate