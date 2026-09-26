---
name: fastapi
description: FastAPI presentation-layer conventions for routers, request and response schemas, dependency injection, exception mapping, middleware, lifespan, and application-service wiring. Use when creating or modifying FastAPI endpoints or HTTP integration.
---

# FastAPI Standard

Treat FastAPI as a presentation and transport boundary.

FastAPI may know which application service or use case to invoke, but it must not contain or reimplement the business logic itself.

## Responsibility

A typical request flow is:

```text
HTTP request
    |
    v
Pydantic request schema
    |
    v
FastAPI router
    |
    v
dependency wiring
    |
    v
application service / use case
    |
    v
domain + ports
```

The router should:

1. receive the HTTP request;
2. validate transport data;
3. obtain required dependencies;
4. call the application service;
5. map application results/errors to HTTP responses.

## Routers

Use `APIRouter` to organize endpoint groups.

Routers should remain thin.

Routers must not directly own:

- SQL queries;
- repository implementations;
- external API requests;
- transaction internals;
- domain decisions;
- complex orchestration.

If a router starts making business decisions, move that logic into an application service or domain component.

## Schemas

Use Pydantic schemas for transport validation and serialization.

Keep request/response schemas at the presentation boundary.

Typical categories:

- request schemas;
- response schemas;
- query/filter schemas;
- error schemas when explicit API contracts require them.

Do not use ORM models directly as public API contracts.

Do not force presentation schemas to become domain models simply to avoid mapping.

Map transport objects into application/domain inputs where appropriate.

## Dependencies

Use FastAPI dependencies to wire outer-layer components.

Dependencies may construct or provide:

- database sessions;
- repository adapters;
- connector adapters;
- application services;
- authentication context;
- configuration-derived infrastructure.

Dependencies should not implement business logic.

A dependency can compose:

```text
Session
  -> SQLAlchemyRepository
  -> ExternalConnector
  -> ApplicationService
  -> Router
```

The application service itself should remain framework-independent.

## Application service example

Conceptual boundary:

```python
@router.post("/tickets", response_model=TicketResponse)
async def create_ticket(
    request: CreateTicketRequest,
    service: TicketService = Depends(get_ticket_service),
) -> TicketResponse:
    result = await service.create(request.to_command())
    return TicketResponse.from_result(result)
```

The specific mapping API may differ, but the router should remain transport-focused.

## Error handling

Map application/domain errors to HTTP responses at the presentation boundary.

Prefer centralized exception handlers for reusable mappings.

Examples:

- domain validation error -> 422 or project-defined validation response;
- missing resource -> 404;
- conflict -> 409;
- authentication/authorization errors -> appropriate 401/403 responses.

Do not leak internal stack traces, SQLAlchemy exceptions, or external API payloads to clients.

## Status codes

Use explicit status codes that match API behavior.

Do not return HTTP 200 for every outcome by default.

Keep status-code decisions in the HTTP layer rather than embedding HTTP concepts in the domain.

## Middleware

Use middleware only for cross-cutting HTTP concerns, such as:

- request IDs;
- logging context;
- CORS;
- tracing;
- timing;
- security headers when appropriate.

Do not place use-case business logic in middleware.

## Lifespan

Use FastAPI lifespan for application lifecycle concerns when required.

Examples:

- shared HTTP client creation;
- connection pool initialization;
- startup resource verification;
- graceful shutdown.

Avoid opening expensive shared clients per request when a safe application-scoped client is appropriate.

## Database session dependency

Provide sessions through a dependency or another explicit composition mechanism.

Session lifecycle should be clear and bounded.

The session dependency may manage:

- opening;
- rollback on failure;
- cleanup/close.

Transaction ownership must follow the project's SQLAlchemy/application transaction convention.

## API versioning and structure

For larger APIs, use a clear structure such as:

```text
presentation/
└── api/
    ├── dependencies/
    ├── routers/
    ├── schemas/
    ├── exception_handlers.py
    └── router.py
```

Do not force this exact layout onto a small or established project if its current structure is already clear.

## OpenAPI

Keep endpoint types, status codes, summaries, descriptions, and response models accurate enough that generated OpenAPI reflects the real API.

Do not duplicate extensive README documentation in endpoint descriptions.

## Business logic rule

FastAPI must not become the business layer.

Bad boundary:

```text
router
 -> query database
 -> apply business rule
 -> call external API
 -> update database
 -> return response
```

Preferred boundary:

```text
router
 -> application service
      -> repository port
      -> connector port
      -> domain logic
 -> response mapping
```

## Testing

Use unit tests for application services independently of FastAPI.

Use integration tests for:

- route wiring;
- validation;
- dependency composition;
- exception-to-HTTP mapping;
- authentication integration.

Do not test every business branch through HTTP if the same behavior is better covered at the application/domain level.
