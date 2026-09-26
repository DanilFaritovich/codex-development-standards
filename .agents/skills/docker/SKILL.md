---
name: docker
description: Docker and Docker Compose conventions for packaging full-stack applications, production-safe networking, health checks, minimal images, configuration, migrations, and one-command startup. Use when containerizing or changing application runtime infrastructure.
---

# Docker Standard

Package the application so that a developer or deployment environment can start the required services predictably with one documented command.

Prefer Docker Compose for multi-service local/deployment orchestration when it fits the project.

## Main goals

- reproducible builds;
- minimal production images;
- clear service boundaries;
- safe network exposure;
- explicit configuration;
- health checks;
- persistent state only where required;
- predictable startup and shutdown.

## One-command startup

Provide a documented entry point such as:

`docker compose up -d`

or a stable Make target such as:

`make up`

If development and production configurations differ, document both explicitly.

Do not require developers to manually start internal services one by one unless there is a strong reason.

## Dockerfiles

Prefer:

- explicit base image versions;
- multi-stage builds when they materially reduce the runtime image;
- dependency layers that make cache reuse effective;
- a non-root runtime user when practical;
- a minimal runtime image;
- deterministic dependency installation.

Do not bake secrets into images.

## .dockerignore

Use `.dockerignore` to exclude unnecessary build context.

Typical exclusions include:

- `.git`;
- virtual environments;
- `node_modules`;
- caches;
- coverage;
- local environment files;
- build outputs not required by the image;
- IDE files.

Do not exclude source or dependency metadata required for reproducible builds.

## Runtime configuration

Use environment variables or mounted configuration for runtime settings.

Never store production secrets in:

- Dockerfiles;
- Compose files committed with real values;
- image layers;
- source code.

Provide safe example configuration when useful.

## Production network model

For a browser-based full-stack application, expose only the public gateway/reverse proxy whenever practical.

Preferred production flow:

```text
Internet / Browser
       |
       v
Public gateway / reverse proxy
       |
       +---- static frontend
       |
       +---- /api/* -> backend:8000
                         |
                         v
                     database
```

Backend and database should normally live on an internal Docker network and should not publish host ports in production.

The browser cannot call an internal Docker hostname directly. Public API traffic must reach the backend through the public gateway/reverse proxy.

Examples of public gateway technology include Caddy or Nginx.

## Development ports

Development may expose backend/database ports when required for debugging or local tooling.

Do not copy development exposure into production configuration automatically.

Keep production network rules stricter than local development rules.

## Compose networks

Use explicit networks where they improve isolation.

A common model:

- public/gateway network;
- internal application network.

Database services should normally only join the internal network.

Backend should join the network needed to communicate with the gateway and database.

## Volumes

Use persistent volumes only for state that must survive container replacement.

Examples:

- PostgreSQL data;
- intentionally persistent application storage.

Do not mount source-code volumes in production unless the deployment model specifically requires it.

## Health checks

Define meaningful health checks for long-running services.

A backend health endpoint should verify enough application readiness to be useful without performing expensive work.

Do not use a health check that always succeeds regardless of application state.

Compose/deployment ordering should rely on health/readiness where startup dependencies require it.

## Database migrations

Run schema migrations before the backend begins serving production traffic.

Preferred flow:

```text
build
 -> start required database
 -> run migration job/step
 -> start backend
 -> start/verify gateway
 -> health validation
```

Do not hide failing migrations inside an endless application restart loop.

## Frontend

For production Vue/static frontend builds:

- build assets in a dedicated build stage;
- serve compiled assets from the chosen gateway/static server;
- route API requests to the internal backend.

Do not ship the full Node development toolchain in the final static runtime image unless required.

## Backend

Backend containers should:

- install only required runtime dependencies;
- use the project's production server command;
- expose the container port internally;
- avoid publishing that port to the host in production when a gateway is present;
- handle termination gracefully.

## Database

PostgreSQL should:

- use persistent storage;
- use configuration from environment/secrets;
- remain internal in production;
- have a health check when other services depend on readiness.

## Validation

Provide lightweight validation commands when possible:

- `docker compose config` for configuration validation;
- targeted image build for changed services;
- health validation after startup.

Do not repeatedly rebuild every image after changes that cannot affect image contents.

Full build/start/health validation can run in GitHub Actions when it is expensive or verbose.

## CI relationship

Local agent workflow should use targeted Docker checks.

GitHub CI may perform:

- complete image builds;
- Compose validation;
- migration startup checks;
- service health checks;
- selected E2E scenarios.

Do not use repeated full Docker logs as the normal local debugging loop.

## Security baseline

At minimum:

- no embedded secrets;
- minimal published ports;
- internal database/backend networking in production where possible;
- supported base images;
- non-root execution where practical;
- minimal packages in runtime images;
- explicit health/readiness behavior.

Adapt stricter security controls to the deployment environment.
