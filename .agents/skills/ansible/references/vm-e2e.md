# Isolated VM E2E Before Real-Host Rollout

Read when implementing or accepting host-changing infrastructure, especially bootstrap, user and SSH-key lifecycle, firewall/SSH transitions, SWAP, reboot or recovery. This describes a validation strategy; it does not authorize creating a VM or contacting a live host.

## Separate validation layers

1. **Offline:** YAML/Ansible/syntax/Make or CLI regression tests with mock SSH, no live inventory or credentials. CI runs this layer only unless an explicitly isolated CI fixture is approved.
2. **Owned isolated VM:** real target OS, initial empty state, real SSH/Ansible, consent handling and failure boundaries, but separate from production. This is where potentially destructive lifecycle cases belong.
3. **Real personal/production VPS:** only after explicit approval of named actions, verified console/alternate access and successful prerequisite testing; use non-destructive independent checks after provisioning.
4. **Release evidence:** PR/merge, green CI, a VM E2E run, production provisioning, post-reboot verification and a published tag are distinct assertions. Record the source and outcome of each separately.

A merged implementation, 100% offline tests or a README test scenario does **not** prove that a real VM or VPS execution succeeded.

## Fixture ownership and repeatability

An optional local fixture can use KVM/QEMU + system libvirt, virt-install and cloud-init. GNOME Boxes or another desktop VM environment can also be used for manual tests, but **do not claim the CLI owns Boxes** unless it actually does.

- Isolate VM identifier, virtual disk, network, SSH known_hosts, controller inventory and disposable key identities from the real server.
- Verify local hypervisor privileges and ownership, image provenance, supported guest distro and required CPU/RAM/storage before creating anything. Prefer explicit consent before installing host dependencies or changing controller groups.
- Verify guest identity and first-boot readiness (`cloud-init`, SSH/service health, console or guest-agent, stable network) before making a reusable baseline or running provisioning.
- Require a working VM console even after final SSH hardening. Do not assume there is SSH recovery if the last managed route is removed.
- Snapshots/reset/delete are **mutating or destructive** and require independent consent, exact fixture ownership/path verification, no unowned resources, and safe retention of failures. Do not reset a failing VM or delete logs merely to rerun automatically.
- Use separate disposable human test accounts/keys for remove/revoke cases; retain independent automation and admin recovery identities throughout.

## E2E acceptance scenarios

For each capability implemented, test realistic **public operator commands**, not just raw Ansible tasks, first on a clean VM and then on an already-converged VM:

| Capability | Scenarios worth testing |
| --- | --- |
| Bootstrap and SSH | First trust, wrong/changed host key refusal, password-to-key handoff, `sudo -n`, complete account/connection verification |
| Multi-stage initializer | Fresh full path, verify-only, deliberate continuation from stage N with *fresh earlier verification*, STOP at failure/pending/drift, consent denied |
| Docker and UFW | Installation, repeat convergence, actual listeners and firewall rules; verify a disposable container only when allowed |
| Human users/keys | Create/inspect, add and revoke key, deny revoked login, remove test user, deny removed login, retain recovery admin |
| SWAP | Configure, resize, disable, re-enable, persist across reboot, no-op repeat; guard memory/disk ownership and avoid damaging system swap |
| Reboot/operations | Separately approved reboot, reconnect, repeat independent access/Docker/UFW/SWAP/security/operations checks |

Run destructive user removal, SSH-rule cleanup and SWAP resizing **only on the isolated fixture** unless the user separately and specifically approves such operations on a real host. For commands that say `verify`, inspect whether they create temporary containers, touch local known_hosts, or otherwise have runtime side effects.

On failure, preserve fixture + evidence and diagnose the smallest issue. Never blindly replay a stage whose remote work may still be running. For reruns, differentiate `changed=0` for managed configuration from disposable smoke-test changes.

## Production acceptance and the documentation trail

- After VM acceptance, require explicit operator approval for any VPS contact or mutation; review exact inventory, ports, secrets handling and console recovery.
- Use staged port migration (if required) and a retained known-good session. Repeat independent verification on the chosen port, then independently approve reboot and check persistence after reconnect.
- Keep run metadata (OS/VM type, stage outcomes, CI link, date, total time, failed cases, and caveats) without sensitive host details. Do not publish raw inventory, passwords, host fingerprints, private keys or internal network data.
- Documentation should distinguish **code implemented**, **offline CI passed**, **operator-reported VM E2E passed**, **operator-reported VPS passed**, **independently observed live test**. If one is missing, say it is unverified. Never invent elapsed time, availability or a platform-support matrix.
- Request/approval to update documentation does not authorize an otherwise unapproved VM run, VPS deployment, reset, reboot or release. A passing VPS verify does not prove destructive production user/SWAP tests.

For orchestration design use [staged-initialization.md](./staged-initialization.md); for compact offline output and wrapper coverage use [validation.md](./validation.md).
