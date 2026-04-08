# Java Fullstack Learning Repository

This repository tracks my end-to-end Java fullstack learning journey.
I am building projects step by step and documenting what I learn, what breaks, and how I fix it.

---

## Learning Goal

Become confident in building production-style fullstack applications using:
- **Backend:** Java, Spring Boot, Spring Data JPA, Spring Security
- **Frontend:** React
- **Database:** H2 (learning), PostgreSQL/MySQL (real usage)
- **Tools:** Maven, Postman, Git/GitHub

---

## Current Notes

- [`SPRING_NOTES.md`](./SPRING_NOTES.md) - detailed Spring + Spring Boot learning handbook

---

## Repository Structure

```text
java-fullstack-learning/
  README.md
  SPRING_NOTES.md
  backend/
    springboot-crud-v1/
    springboot-jpa-v1/
    springboot-security-jwt/
  frontend/
    react-basics/
    react-with-spring-api/
  db/
    sql-notes.md
    schema-design/
  interview/
    qa-java.md
    qa-spring.md
  progress/
    weekly-log.md
```

---

## Learning Roadmap

### Phase 1 - Core Spring
- [x] IoC and DI basics
- [x] `@Autowired`, `@Primary`, `@Qualifier`
- [x] Spring MVC basics
- [x] REST APIs with GET/POST/PUT/DELETE
- [x] Layered architecture (Controller -> Service -> Repository)

### Phase 2 - Data Layer
- [x] Spring Data JPA basics
- [x] H2 setup + CRUD integration
- [ ] Custom queries (`@Query`, derived methods)
- [ ] Pagination + sorting
- [ ] Entity relationships (`@OneToMany`, `@ManyToOne`, etc.)

### Phase 3 - API Quality
- [ ] DTO pattern
- [ ] Validation (`@Valid`, constraints)
- [ ] Global exception handling (`@ControllerAdvice`)
- [ ] Proper response design (`ResponseEntity`, status codes)

### Phase 4 - Fullstack Integration
- [ ] React app integration with Spring Boot APIs
- [ ] CORS setup (dev + production style)
- [ ] Auth flow between frontend and backend

### Phase 5 - Security + Production Readiness
- [ ] Spring Security fundamentals
- [ ] JWT authentication and authorization
- [ ] Testing (`@WebMvcTest`, `@DataJpaTest`, service tests)
- [ ] Actuator, logging, profile-based configs
- [ ] DB migrations (Flyway/Liquibase)

---

## How I Use This Repo

- Add one concept/project at a time.
- Keep code and notes together.
- Track progress weekly in `progress/weekly-log.md`.
- Prefer building from scratch first, then compare and improve.

---

## Project Template (for each module)

For each new project folder, I will include:
- **Objective**
- **Tech stack**
- **Endpoints**
- **What I learned**
- **Problems I faced**
- **How I solved them**

---

## Why This Repo Exists

This is not just a code dump.
It is a structured learning log to show:
- consistency
- problem-solving ability
- growth from fundamentals to production-level practices

---

## Next Immediate Steps

1. Clean and maintain `SPRING_NOTES.md`
2. Build and add `backend/springboot-crud-v1`
3. Add DTO + validation + exception handling in next project
4. Connect React frontend with Spring Boot backend

---

## Author

**Ajay**  
Learning in public, one module at a time.

