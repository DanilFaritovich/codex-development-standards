# Bootstrap a Central Docker Deployment Platform

Source: https://github.com/DanilFaritovich/codex-development-standards

Use this guide to install development standards into a repository that centrally registers, deploys and routes independently built Docker services on a single VPS. This is a **standards-only bootstrap** unless implementation is separately requested.

## Select standards

1. Read the new project's `AGENTS.md` when present; inspect the actual tree and current Git branch/worktree state.
2. Read upstream `catalog.yaml`, then `profiles/docker-deployment-platform.yaml` and the required skills only. Do not scan all upstream skills or load every `references/` file.
3. Install the complete selected skill packages, including references as files; keep optional references unloaded until relevant work.
4. Enable `ansible` only if the deployment repo actually owns playbooks; avoid duplicating the separately maintained base VPS provisioning system.
5. Enable `python-testing` if the controller is Python. Do not choose Python or implement a daemon merely because a profile mentions them.
6. Record the exact selected upstream commit, profile version, installed skills and optional selection in `.codex-standards.lock.yaml`.
7. Keep local adaptations and standing PR authorization, if any, in project-local `AGENTS.md`; default is local commit and no push/PR/merge without explicit request.

## Establish architecture boundaries

- Server-infrastructure repo: host security, SSH, Docker Engine, foundational access.
- Deployment-platform repo: service registry, gateway/Caddy, shared network, digest selection, reconciliation and rollout.
- App repos: source, app tests, GHCR image production and documented internal runtime contracts.

Document desired interfaces rather than inventing deployed services. A new registry entry is not automatically deployable without a compatible image, validated image digest, internal port, health contract, domain approval and environment prerequisites.

## Plan an incremental implementation

1. **Offline foundation:** versioned manifest schema, validator, `plan`/dry-run, Caddy generation and validation, Makefile, unit tests and offline CI. No real VPS contact.
2. **Disposable local E2E:** mock upstreams and isolated Compose/Caddy containers; registration, no-op, update, unsafe input rejection, route reload and simulated failure recovery.
3. **Protected production integration:** only after explicit operator approval, verify host access, registry pulls, trusted domains/DNS, certificate storage, selected digest and release policy; perform a controlled deployment.
4. **Recovery/automation:** review security gates, define approved automatic update scope, test rollback/reboot and monitoring without overstating resilience.

Do not implement controller logic, configure production credentials, perform host SSH or publish public routes during standards-only initialization. The new project must remain bootstrappable without production DNS, TLS, credentials or a reachable VPS.

## Validation

Verify installed profile/skill paths, lock revision and concise local setup instructions. Do not claim image rollout, live HTTPS or GitOps reconciliation worked until actual implementation and environment tests establish those results. Use the project's Makefile and concise output during subsequent work.
