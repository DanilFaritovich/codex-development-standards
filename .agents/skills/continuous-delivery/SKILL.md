---
name: continuous-delivery
description: Continuous Delivery conventions for Docker Compose projects deployed from GitHub Actions to a server using immutable images, GitHub Environment secrets, server-side runtime env files, migrations, health checks, and safe rollback. Use when adding or changing production deployment.
---

# Continuous Delivery Standard

Use this standard for production deployment of Dockerized applications when Docker Compose is sufficient.

Do not introduce Kubernetes only to implement CD.

## 1. Deployment flow

Preferred production flow:

```text
Pull Request
  -> CI
  -> merge to main
  -> build production images
  -> push images to registry
  -> deploy exact image versions
  -> update production runtime configuration
  -> migrations
  -> update services
  -> health checks
  -> success
```

Production deployment should normally start only from the stable branch, release, tag, or another explicitly approved production source.

Do not deploy arbitrary feature branches to production.

## 2. Build once, deploy the same artifact

Build application images in GitHub Actions.

Publish them to the configured registry, normally GHCR for GitHub-hosted portfolio projects.

Do not SSH to the server and rebuild application source there.

Preferred:

```text
GitHub Actions
  -> docker build
  -> GHCR
  -> server pulls exact image
```

Production must run the same artifact produced by the deployment workflow.

## 3. Immutable image references

Every deployment must identify the exact application image.

Prefer an immutable digest:

```text
ghcr.io/<owner>/<image>@sha256:<digest>
```

A commit-SHA tag may also be published for readability.

Do not rely only on mutable tags such as `latest` for production state or rollback.

The deployment must make it possible to determine which Git commit and image digest are currently running.

## 4. Production Docker Compose belongs in the repository

Keep production Compose configuration in Git.

Recommended name:

```text
compose.production.yml
```

or another clearly documented equivalent.

The file may safely live in a public repository when it contains no secrets.

Production Compose should normally reference published images rather than build application services:

```yaml
services:
  backend:
    image: ${BACKEND_IMAGE}

  frontend:
    image: ${FRONTEND_IMAGE}
```

Do not commit production credentials or secret values into Compose.

## 5. Synchronize Compose to the server during deployment

The deployment workflow should copy or synchronize the repository-controlled production Compose file to a stable application directory on the server.

Example:

```text
/opt/<project>/
├── compose.production.yml
└── .env
```

This keeps the server deployment definition aligned with the exact repository revision being deployed.

Do not require manual Compose edits on the server during normal releases.

## 6. Production environment configuration

Production runtime configuration must not be committed to the repository.

Use a GitHub Environment, normally:

```text
production
```

Store sensitive runtime values in GitHub Environment Secrets.

Non-sensitive deployment values may use GitHub Environment Variables when useful.

For small portfolio/single-server projects, a complete production env file may be stored as one multiline GitHub Environment secret such as:

```text
PRODUCTION_ENV_FILE
```

The repository should still contain a safe:

```text
.env.example
```

documenting required variable names without real secrets.

## 7. Deploy the runtime .env to the server securely

During deployment, GitHub Actions should create/update the server-side runtime env file from the configured GitHub Environment secrets/variables.

Recommended target:

```text
/opt/<project>/.env
```

Requirements:

- never commit the production `.env`;
- never print its contents in workflow logs;
- write it only during the protected deployment job;
- restrict server file permissions, normally owner-readable only such as `chmod 600`;
- overwrite/update it deliberately as part of deployment;
- do not leave temporary plaintext copies in the repository or runner workspace longer than required.

A deployment may use either:

- one multiline secret containing the full production env file; or
- individual GitHub Environment Secrets/Variables rendered into the env file.

Choose the simpler mechanism that remains safe and maintainable for the project.

## 8. Server runtime files are deployment state

The production server may keep:

```text
/opt/<project>/compose.production.yml
/opt/<project>/.env
```

between deployments.

This is intentional.

The Compose file comes from the repository.

The production env file comes from protected GitHub Environment configuration.

The server should therefore be able to restart the already deployed stack after a host/container restart without requiring the GitHub runner to remain connected.

Do not make the running application depend on ephemeral GitHub Actions environment variables after deployment completes.

## 9. Secrets and public repositories

A deployed project may remain public.

It is acceptable to publish:

- source code;
- Dockerfiles;
- `compose.production.yml`;
- Nginx/Caddy configuration;
- deployment scripts;
- GitHub Actions workflows;
- Makefiles;
- migrations;
- `.env.example`.

Never publish:

- production `.env`;
- passwords;
- API keys;
- private SSH keys;
- JWT/signing secrets;
- database credentials;
- private certificates/keys;
- provider tokens.

Deployment credentials must be available only to trusted deployment jobs.

## 10. Production server topology

Expose only the public gateway when practical.

Typical topology:

```text
Internet
  |
  v
Nginx / Caddy
  |
  +--> frontend
  |
  +--> /api/ -> backend
                  |
                  +--> PostgreSQL
                  +--> Redis
```

Backend, PostgreSQL, and Redis should normally remain on internal Docker networks and should not publish production host ports without an explicit operational need.

Preserve the project's chosen reverse proxy. Do not replace Nginx with Caddy or vice versa merely because another standard uses a different example.

## 11. Deployment directory and transport

For a single VPS, SSH is an acceptable deployment transport.

Use:

- a stable deployment directory such as `/opt/<project>/`;
- key-based authentication;
- a dedicated deployment user where practical;
- host-key verification;
- only the permissions required for deployment.

Do not require root SSH when a less-privileged deployment user is sufficient.

Avoid disabling SSH host verification as a convenience.

## 12. Deployment sequence

A normal deployment should perform, in this order where appropriate:

```text
1. identify exact image digests
2. authenticate/pull required images
3. sync compose.production.yml
4. securely update server .env
5. validate Docker Compose configuration
6. wait for required infrastructure readiness
7. run database migrations
8. update application services
9. wait for container health
10. verify the public application
11. record/report deployed revision
```

Do not report deployment success before required health verification passes.

## 13. Database migrations

Run migrations as an explicit deployment step before the new backend serves production traffic.

For Alembic-based projects, use the project's stable migration command.

A failed required migration must fail the deployment.

Do not use uncontrolled application startup code as the production schema migration mechanism.

Do not automatically execute database downgrade during application rollback.

Application rollback and database rollback are separate operational decisions.

Prefer backward-compatible schema changes when rollback of application images must remain possible.

## 14. Health checks, failure, and rollback

Deployment must have bounded readiness/health waits.

Do not use unbounded loops or arbitrary long sleeps.

A deployment fails when a required step fails, including:

- image pull;
- Compose validation;
- migration;
- service readiness;
- public health check.

Before replacing the current application version, preserve the previous immutable image references when automatic application rollback is supported.

If the new application version fails after service replacement and rollback is safe:

```text
restore previous application image references
  -> update services
  -> verify health
  -> report deployment failure
```

Do not hide the original deployment failure even if rollback succeeds.

Do not assume reverting an image reverses database or other persistent state.

## 15. GitHub Actions deployment controls

Use a GitHub Environment for production deployment.

Keep production deployment secrets scoped to that environment.

Allow only one production deployment at a time using workflow concurrency or an equivalent mechanism.

Do not expose production secrets to untrusted Pull Request jobs.

Use least-privilege workflow permissions.

A manual `workflow_dispatch` may be provided for controlled redeploy/recovery, but it must not bypass required validation or deploy an untrusted arbitrary artifact.

## 16. Keep deployment logic maintainable

Avoid a large opaque shell script embedded entirely inside workflow YAML.

Prefer:

```text
.github/workflows/deploy.yml
        |
        v
Makefile / version-controlled deploy script
        |
        v
Docker Compose
```

Use project-owned commands where they improve reproducibility.

The workflow should orchestrate deployment, not duplicate every implementation detail.

## 17. Validation and CI/CD boundary

Pull Request CI should validate deployment artifacts without performing a real production deployment.

Useful checks include:

- production image build;
- `docker compose -f compose.production.yml config`;
- deployment script syntax;
- migration startup against a test environment when justified;
- reverse-proxy configuration validation;
- service health smoke tests where reasonable.

The production deployment job runs only after the required CI gate.

Do not repeatedly run the full production stack locally as the normal debugging loop.

## 18. Required documentation

Document:

- what triggers production deployment;
- image/registry naming;
- required GitHub Environment Secrets/Variables;
- server deployment directory;
- server bootstrap prerequisites;
- runtime `.env` delivery model;
- production Compose location;
- migration behavior;
- health verification;
- rollback behavior.

Never document real secret values.

## Completion criteria

CD is complete when:

- required CI gates production deployment;
- production images are built outside the server;
- deployment uses immutable image references;
- images are published to the configured registry;
- `compose.production.yml` is version-controlled and synchronized to the server;
- production runtime env is stored in protected GitHub Environment configuration and securely written to the server;
- production secrets are absent from the repository and logs;
- internal services are not unnecessarily exposed;
- migrations are explicit;
- health checks determine deployment success;
- concurrent production deploys are prevented;
- failure/rollback behavior is defined;
- deployment documentation is current.
