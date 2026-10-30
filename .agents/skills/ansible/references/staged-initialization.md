# Staged Infrastructure Initialization and Recovery

Read when building or reviewing a single operator workflow that spans bootstrap, system provisioning, SSH/firewall transitions, human access, or operations. This is a **reusable design pattern**, not a requirement that every Ansible project have seven stages, specific Make targets, or a Python CLI.

## Contract before implementation

Decide and document:

- the operator's **single public entry point** (for example, `make init-server`) and independently callable stage-specific entry points;
- initial administrator, managed automation identity, optional human recovery administrator, key sources, target inventory, and operator trust boundary;
- which stages apply host changes, which only verify, which mutate only runtime smoke-test state, and which require interactive consent;
- each stage's prerequisites, expected postconditions, independently observable evidence, idempotency contract, and recovery strategy;
- explicit STOP conditions for ambiguous permissions, unmanaged drift, stale host keys, lost SSH routes, pending mutation markers or incomplete operations.

Do not bundle unrelated application/reverse-proxy/CD deployment into a host-bootstrap command without an explicit project requirement.

## Example stage dependency graph

Use only applicable capabilities and preserve dependencies. A useful project may evolve through:

| Stage | Purpose | Independent proof |
| --- | --- | --- |
| Controller/bootstrap | Tooling, first host trust, managed account/key/sudo | Fresh key-only automation login, identity and `sudo -n` |
| SWAP (optional) | Owned, persistent swapfile with capacity checks | Active swap, size, ownership, `fstab` and post-reboot state |
| Docker host (optional) | Vendor packages/plugins/services and bounded logs | daemon/plugin checks; disposable container smoke when authorized |
| SSH/UFW host hardening | Configure new listeners/firewall without locking out the operator | effective SSH/socket listeners, firewall state and fresh reachability |
| Human recovery admin | Distinct account, verified key and explicit sudo policy | independent human login and privilege check |
| Final SSH policy | Restrict root/password/interactive methods after replacement access works | effective policy **and** human/automation key-only login |
| Operations | Updates, journald/log bounds, service/system policies | actual persisted configuration and service status |

Keep the dependency graph smaller for projects with smaller requirements; never enforce this table as a rigid framework. Never treat a successful Ansible `PLAY RECAP` as a substitute for an independent access check.

## Orchestration and safe continuation

- A multi-step CLI should display a stable plan: target, stages, exact authorized mutation frontier, access identities, network/port and recovery preconditions; approval must be **default deny**. Native SSH, sudo and key/passphrase prompts are separate.
- Verify earlier stages afresh when resuming from an explicitly selected stage. A `verify-only` mode must not implicitly apply provisioning or infer completion from a saved log or cached success. Inspect live effects of verification: smoke checks may run disposable containers or update caches.
- Each stage completes only after an independent verifier has succeeded; when its own wrapper already executes a complete independent postflight, reuse that evidence within **the same invocation** instead of running an identical verifier again. Do not reuse it across separate invocations.
- Make remote operations serialized within the controller process and guard against stale plan inputs where possible. A local lock **does not coordinate other controllers** or direct manual Ansible runs.
- On failures, network loss, interruption, timeout, log-write error or ambiguous remote execution: **STOP later dispatch**. Do not automatically retry, reboot, roll back, reset, delete pending evidence, skip to the next stage, or assume an SSH failure proves the remote process ended.
- If a stage uses pending activation/ownership records, preserve them on failure and provide a read-only inspector and manual recovery instructions. Do not clear markers solely to make an automated check pass.
- Distinguish idempotent **managed configuration** (unchanged when already converged) from transient runtime effects such as Docker smoke-test containers and SSH probes.

## SSH port migration

When a host starts at port 22 and needs 2222 (example only):

`current access verified → add new listener and provider/UFW allow → validate effective socket/sshd config → verify new key-only access and sudo → transfer trusted endpoint identity safely → change controller inventory route → freshly verify all stages → retire old route only by separate reviewed action`

- Distinguish `listen_ports` on the host, UFW rules, provider firewall, `controller_port` and `external_verify_ports`; they are different contracts. On a network where outbound 22 is blocked, external verification may use **only the independently proven new port** while the host continues listening on both.
- Host-key acceptance requires identity evidence; when a VPS is rebuilt and `known_hosts` conflicts, compare the new fingerprint through a trusted independent channel before updating the affected host:port entry. Never disable strict checking or blindly accept a keyscan.
- Handle systemd SSH socket activation when present. `sshd_config` alone may not describe actual listeners. Do not close the old port until retained administrator access and provider console recovery are proven.
- Root/password denial is a **later** access-policy transition, after distinct human and automation key access work. Treat provider console reachability and a verified alternate session as recovery preconditions, not something an optimistic port probe replaces.

## Status, timing and operator evidence

When needed, emit a minimal local, access-restricted, non-secret machine-readable event log (for example, JSONL with stage, status, elapsed seconds). Keep it out of Git. Do not log inventories, addresses, usernames, tokens, private/public keys, command arguments, raw SSH failures or subprocess output.

Keep completion, verified-skip, failure and interruption distinct. Report whole-run elapsed time from the run record rather than summing overlapping stage/status events. Prompts and network waiting can make timings non-comparable across machines; never claim benchmarks without comparable workloads and conditions.

See [vm-e2e.md](./vm-e2e.md) for manual integration acceptance and [validation.md](./validation.md) for offline/CI checks. The project may use different stage names or logging formats; adopt the **invariants**, not a hardcoded implementation.
