# Repository Instructions

This repository defines reusable Codex development standards.

## Skill authoring

Keep each `SKILL.md` focused on the compact rules Codex must load to make decisions:

- mandatory principles and invariants;
- decision rules;
- the normal workflow;
- explicit guidance for when additional reference material is required.

Do not grow a core skill with long implementation recipes, exhaustive examples, or technology-specific detail that only some tasks need.

Move optional detail into colocated reference files, for example:

```text
.agents/skills/api-guardrails/
├── SKILL.md
└── references/
    ├── redis-quota.md
    ├── nginx-rate-limit.md
    └── input-limits.md
```

A core skill should tell Codex when to read a reference. Codex should load that reference only when the current task requires that topic.

Avoid duplicating the same normative rule in both `SKILL.md` and a reference file. Keep the rule in the core skill and use references for implementation detail, examples, and deeper guidance.

When updating standards, prefer small targeted edits over broad rewrites and preserve modularity between skills.
