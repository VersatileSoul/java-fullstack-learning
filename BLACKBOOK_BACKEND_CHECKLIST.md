# Blackbook Backend Checklist (No Code)

Use this as your step-by-step guide to build Blackbook backend yourself.

## 1) Define V1 Scope

- Decide what Blackbook stores (contacts, notes, entries, tasks, etc.).
- Keep V1 to one main entity + CRUD only.
- Write 5-8 clear requirements before coding.

## 2) Create Spring Boot Project

- Use Spring Initializr.
- Add dependencies:
  - Spring Web
  - Spring Data JPA
  - H2 Database (or PostgreSQL directly if you prefer)
- Start app and confirm server runs.

## 3) Set Package Structure

- `controller`
- `service`
- `repository`
- `model` or `entity`
- Optional later: `dto`, `exception`, `config`

## 4) Model/Entity Design

- Create one main entity.
- Include:
  - ID (primary key)
  - 3-6 core fields
  - optional timestamps
- Add proper JPA annotations.

## 5) Repository Layer

- Create interface extending `JpaRepository<Entity, IdType>`.
- Start with default CRUD methods.
- Keep custom queries for later.

## 6) Service Layer

- Add methods for:
  - get all
  - get by id
  - create
  - update
  - delete
- Put business logic here, not in controller.

## 7) Controller Layer

- Expose endpoints:
  - `GET /...` all
  - `GET /.../{id}` one
  - `POST /...` create
  - `PUT /.../{id}` update
  - `DELETE /.../{id}` delete
- Use path variable for ID.
- Use request body for create/update.

## 8) Database Configuration

- Configure datasource in `application.properties`.
- If using H2:
  - enable H2 console
  - verify table creation
- Ensure app starts cleanly with DB connected.

## 9) Postman Testing Flow

1. `GET all` -> expect empty list initially  
2. `POST create` -> add one item  
3. `GET all` -> confirm item appears  
4. `GET by id` -> confirm single item  
5. `PUT update` -> verify changes  
6. `DELETE` -> remove item  
7. `GET all` -> confirm deletion

## 10) Minimum Error Handling for V1

- Missing ID should return a clean response (not crash).
- Invalid request should return understandable message.
- Keep it basic first; improve in V2.

## 11) V1 Done Checklist

- [ ] CRUD endpoints working end-to-end
- [ ] Data stored in DB (not hardcoded list)
- [ ] Layered structure followed
- [ ] Tested with Postman
- [ ] Project README added

## 12) V2 Upgrade Plan (After V1)

- DTOs
- Validation (`@Valid`, field constraints)
- Global exception handling (`@ControllerAdvice`)
- Pagination + sorting
- Move from H2 to PostgreSQL (if not already)
- Add Spring Security + JWT

## 13) Suggested Commit Sequence

1. project setup  
2. entity + repository  
3. service + controller  
4. db config  
5. tested CRUD endpoints  
6. README and cleanup

---

Build first, polish second.  
V1 shipping is more important than perfect architecture on day one.
