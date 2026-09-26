---
name: api-guardrails
description: API protection conventions for rate limiting, request size limits, Pydantic input constraints, upload limits, trusted client identification, and consistent HTTP errors. Use when designing or modifying public/private HTTP APIs.
---

# API Guardrails Standard

Every HTTP API should define explicit resource and input limits.

Limits must protect the application without embedding arbitrary magic numbers throughout routers.

Use centralized, configurable defaults with per-endpoint overrides where the use case requires them.

## Rate limiting

Public and user-facing API endpoints should be covered by a rate-limit policy unless there is a documented reason to exempt an endpoint.

Support multiple windows when appropriate, for example:

- requests per minute;
- requests per hour when useful;
- requests per day.

Do not hardcode one universal number into every route.

Prefer configuration such as:

- `RATE_LIMIT_PER_MINUTE`;
- `RATE_LIMIT_PER_DAY`;
- route-specific overrides.

The actual numeric values are project/product policy and should be selected during initialization or configuration.

## Rate-limit identity

Choose the limiter key deliberately.

Possible identities include:

- authenticated user/account ID;
- API key/client ID;
- IP address for anonymous traffic;
- a combination of identity and endpoint.

Prefer authenticated identity when available.

Do not trust arbitrary `X-Forwarded-For` values from untrusted clients.

When deployed behind a reverse proxy, configure trusted proxy handling so client IP extraction cannot be trivially spoofed.

## Distributed deployments

For more than one backend instance, use shared rate-limit state when limits must be globally consistent.

A shared store such as Redis is a common solution.

In-memory limiting is acceptable only when:

- the application is single-instance;
- the environment is local/test;
- approximate per-instance limiting is explicitly acceptable.

Do not present per-process memory limits as globally accurate in a horizontally scaled deployment.

## HTTP response

When a rate limit is exceeded:

- return HTTP 429;
- use a consistent error schema;
- provide retry information such as `Retry-After` when practical;
- do not expose internal limiter implementation details.

Rate-limit failures should be observable through metrics/logging without producing excessive noisy logs.

## Endpoint policies

Allow stricter limits for expensive or abuse-sensitive operations.

Examples:

- authentication;
- password reset;
- LLM inference;
- file processing;
- exports;
- external API fan-out;
- expensive search.

Cheap read endpoints may use a different policy.

Document intentional exemptions.

## Input constraints

Validate semantic input constraints using schemas.

For Pydantic models, define explicit constraints where appropriate:

- string `min_length` / `max_length`;
- numeric ranges;
- list/set item limits;
- collection lengths;
- enum/literal values;
- nested object constraints.

Do not accept unbounded user-controlled strings or collections when the domain has a practical maximum.

Input limits must reflect product/domain needs rather than arbitrary implementation convenience.

## Request body size

Define a maximum HTTP request body size for endpoints that accept user-controlled bodies.

Enforce it at the most appropriate boundary:

- reverse proxy/gateway;
- ASGI/application middleware when required;
- upload handler for file endpoints.

Prefer defense in depth for internet-facing production deployments.

When the request is too large, return HTTP 413 where appropriate.

## File uploads

For upload endpoints define:

- maximum file size;
- allowed content types when meaningful;
- file count;
- filename/path safety;
- streaming behavior for large accepted files.

Do not read an unbounded upload fully into memory.

Content-Type alone must not be treated as a security guarantee.

## Text and collection limits

For text-processing/LLM endpoints, define limits such as:

- maximum ticket/document length;
- maximum number of items;
- maximum template size;
- maximum metadata size.

If token-based model limits matter, validate both practical input size and downstream model constraints where appropriate.

Reject oversized input before expensive downstream processing.

## Pagination

List endpoints should have bounded pagination.

Define:

- default page size;
- maximum page size;
- cursor/page rules.

Do not allow a client to request an unbounded dataset in one normal API request.

## Timeouts

Expensive external operations should have explicit timeouts.

Do not allow user requests to wait forever on:

- external HTTP services;
- databases;
- LLM APIs;
- other network dependencies.

Timeout values are project-specific and should be configuration-driven where appropriate.

## Concurrency and expensive work

Rate limiting is not a replacement for concurrency/resource controls.

For expensive endpoints, consider:

- bounded concurrency;
- background jobs/queues;
- per-user quotas;
- upstream provider limits.

Do not hold HTTP workers indefinitely for workloads that belong in background processing.

## Configuration

Centralize API guardrail settings.

A project may expose settings such as:

```text
RATE_LIMIT_PER_MINUTE
RATE_LIMIT_PER_DAY
MAX_REQUEST_BODY_BYTES
MAX_UPLOAD_BYTES
DEFAULT_PAGE_SIZE
MAX_PAGE_SIZE
```

Endpoint-specific policies may override defaults through a clear declarative mechanism.

Do not scatter duplicated numeric limits across routers.

## FastAPI integration

Keep enforcement at the presentation/infrastructure boundary.

FastAPI dependencies or middleware may enforce:

- rate limits;
- request size limits;
- authentication-derived identity.

Pydantic schemas enforce field-level constraints.

Business services should receive already validated bounded inputs, but domain invariants must still be enforced in domain/application code when they are true business rules.

## Reverse proxy integration

When using Caddy, Nginx, or another gateway, configure request-size/proxy behavior consistently with the application limits.

The gateway may reject obviously oversized requests before they consume backend resources.

Do not create contradictory gateway/application limits without documenting the reason.

## Logging

Log limit violations in a controlled structured form when operationally useful.

Include safe context such as:

- route;
- limiter policy;
- request ID;
- authenticated client/user identifier when allowed.

Do not log sensitive request bodies.

Avoid flooding logs for abusive traffic; sampling/aggregation may be more appropriate at high volume.

## Testing

Add tests for guardrails that are part of the API contract.

Examples:

- request below limit succeeds;
- request above field length fails;
- oversized payload returns the expected error;
- rate limit returns 429;
- endpoint-specific overrides work;
- trusted identity selection works.

Do not make unit tests depend on real wall-clock delays when the limiter can use a testable clock/store abstraction.

## Documentation

Document externally relevant limits when API consumers need to know them.

Do not expose internal anti-abuse thresholds if doing so would create unnecessary security risk.

At minimum, keep operator/developer configuration documented.
