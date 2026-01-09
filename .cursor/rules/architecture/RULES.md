---
description: "Guidelines for code organization, layering, and separation of concerns across services."
alwaysApply: true
---

# Code Organization & Architecture Rules

These rules define how code should be structured across projects in this organization, with a focus on clarity, maintainability, and separation of concerns.

## 1. Layered Architecture

Code must be organized into clear layers. A typical structure:

- **API / Interface layer**
  - HTTP handlers, controllers, views, GraphQL resolvers, CLI entrypoints, etc.
  - Handles requests/responses, validation, serialization.
  - No business logic here.

- **Application / Service layer**
  - Use cases, workflows, orchestration.
  - Coordinates domain logic, repositories, external services.
  - Contains business rules, but no framework-specific details.

- **Domain / Model layer**
  - Entities, value objects, domain services.
  - Pure business logic, no I/O, no HTTP, no DB/ORM, no Kubernetes/Cloud SDKs.

- **Infrastructure layer**
  - Database repositories, Kubernetes clients, message brokers, HTTP clients, file storage, etc.
  - Implements interfaces defined in the application/domain layer.

### Requirements

- Handlers/controllers must not call the database or external services directly.
  They should delegate to a service/use-case.

- Domain and application layers must not depend on web frameworks or infrastructure libraries.

- Infrastructure layer must not contain business rules.

---

## 2. Folder Structure

Projects should favor feature/domain-based organization over purely technical buckets.

Example (Good):

```
app/
  applications/
    api/
    domain/
    infra/
  clusters/
    api/
    domain/
    infra/
  shared/
```

Example (Bad):

```
app/
  controllers/
  models/
  services/
  utils/
  misc/
```

---

## 3. Dependency Direction

Dependencies between layers must be one-way:

- API → Application → Domain
- Application → Domain
- Infrastructure → Domain/Application interfaces

Circular dependencies between layers are not allowed.

---

## 4. Keep Handlers Thin, Services Focused

- Handlers should be thin and free of business rules.
- Services should encapsulate business logic.
- Avoid “God services” that do everything.

---

## Rationale

These rules ensure that:

- Codebases remain navigable as they grow.
- Business logic is reusable and testable.
- Infrastructure and frameworks can be replaced with minimal impact.
- The same mental model applies across all services in the organization.
