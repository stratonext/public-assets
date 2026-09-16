---
name: software-developer
description: >
  Skill for implementing well-architected platform code. Enforces the tech stack,
  naming conventions, file structure, testing, linting, CI/CD, and documentation
  rules defined below.
  Read AGENTS.md in the target repository before writing any code.
  Read architecture documents in docs/architecture/ for context on what to implement.
version: 1.0
---

# Software Developer Skill

## Before Writing Any Code

1. Read `AGENTS.md` in the target repository for repository-specific rules and entry points.
2. Read the relevant architecture document(s) in `docs/architecture/` to understand what is being implemented and why.
3. Read `requirements.md` to confirm the requirement being satisfied.
4. Check `docs/architecture/assumptions/` for open AARs that may affect the implementation.
5. Check `_AGENT/OPEN_POINTS.md` for unresolved items relevant to the area being touched.

## During Implementation

Two tiers, depending on whether the issue blocks correct implementation:

**Blocking** — the spec cannot be followed as written, conflicts with the architecture, or a required decision has no documented answer. Do not guess, do not pick the closest alternative and move on:

1. Add a `// TODO|CLARIFICATION NEEDED:` comment at the exact location in the code explaining what the problem is and what was done instead (if anything).
2. Stop and ask the developer for explicit confirmation before proceeding past that point.

```csharp
// TODO|CLARIFICATION NEEDED: ARCH-004 specifies that CredentialBrokerService generates
// credentials at fetch time, but the AWS STS SDK requires a role ARN that is not
// present in the current ApprovalRecord model. Proceeding requires either adding
// role_arn to the approval record or sourcing it from a different location.
// Stopping here pending developer input.
```

**Non-blocking** — a deliberate simplification, deferred edge case, known gap, or a decision that has a reasonable default but deserves a second look. Do not silently swallow it and do not stop the session for it either. Instead, append one entry to `_AGENT/OPEN_POINTS.md` and keep going:

```markdown
## 2026-07-13 — TaskRepository.list()
Pagination is offset-based, not cursor-based. Fine at current row counts;
revisit if a table this queries passes ~100k rows.
```

Each entry: date, location (`file:line` or component name), one or two lines on what was deferred and why. No status field to maintain — an entry is open until someone deletes it.

---

## After Implementation

After completing an implementation, review what was built and identify anything that would be useful for future agents working in the same repository: non-obvious patterns, key entry points, important constraints, reusable utilities, or gotchas discovered during implementation.

If this session added entries to `_AGENT/OPEN_POINTS.md`, list them for the developer in the summary. If it resolved any existing entries, delete them from the file as part of the same change.

For each candidate entry, propose it to the developer before writing:

> "I found the following worth adding to AGENTS.md: [entry]. Shall I add it?"

Only update `AGENTS.md` after explicit confirmation. Do not add trivial or obvious information.

---

## Tech Stack

| Concern | Choice |
|---|---|
| Backend services | C# on .NET |
| Agents | Python 3.13, managed with `uv`, using AWS Strands framework |
| Frontend | React SPA with Vite for interactive UI. Astro for static site generation. |
| Frontend package manager | pnpm |
| Python package manager | uv |
| .NET package manager | NuGet |

---

## Repository Structure

Every repository must have this layout. `src` and `tests` are required; others only when needed.

```
src/        all application source code
tests/      all test code
docs/       repository-specific documentation (optional)
assets/     static assets (optional)
scripts/    utility and automation scripts (optional)
```

---

## Taskfile

Every repository must include a `Taskfile.yml` with at least these tasks:

| Task | Description |
|---|---|
| `task compile` | Compile or type-check |
| `task build` | Produce a deployable artifact |
| `task run` | Run locally |
| `task test` | Run unit tests, exit non-zero on failure |
| `task lint` | Run linter, zero warnings required |

---

## CI/CD

- All pipelines run on **GitHub Actions**.
- Every repository must have a workflow that runs `task compile` and `task test` on every push and PR.
- A failing build or failing tests must block merge. Enforce via branch protection on `main`.
- Direct commits to `main` are banned. All changes go through PRs.

---

## Naming Conventions

### Components
- `Service` suffix — callable components (e.g., `AgentService`, `TaskService`)
- `Engine` suffix — event/timer-driven components (e.g., `CoreEngine`, `AgentGateway`)
- `Repository` suffix — persistence components (e.g., `TaskRepository`)
- Inner sub-components use domain role names with no suffix (e.g., `RiskClassifier`)

### Files and Directories
- All files and directories use **lowercase kebab-case**: `word-word.ext`
- CamelCase, PascalCase, and snake_case are banned for file and directory names across all languages

---

## Code Quality Rules

### All Languages
- All code, comments, documentation, commit messages, and PR descriptions must be in **English**
- All code must be **fully typed**. Untyped code is banned except in bash scripts
- Every public function, method, and class must have a **docstring** with a plain English description. Return type must be documented; parameter values are optional; types go in signatures only
- No dead code — unused code, commented-out blocks, and TODO stubs must not be merged without a linked issue
- Dependencies pinned to exact versions in lock files (`packages.lock.json`, `pnpm-lock.yaml`, `uv.lock`). Lock files are committed

### Python
- Use **Pydantic** for data models
- All functions must have type annotations

### C#
- Use explicit types; `var` is allowed only when the type is unambiguous from the right-hand side
- Use **Roslyn analyzers** for linting

### TypeScript
- Strict mode enabled
- `any` is banned
- Use **ESLint** for linting

### Python Linting
- Use **Ruff**

---

## PR and Commit Rules

- Each PR addresses **one concern**. Large multi-purpose PRs are discouraged
- Commit messages use **imperative mood** and reference issue numbers where applicable
- `task lint` must pass with zero warnings before a PR can be merged
- `task test` must pass before a PR can be merged
- Environment parity: local `task` commands and CI steps must run the same commands

---

## Required Repository Files

Every repository must include:

| File | Content |
|---|---|
| `README.md` | What the service does, how to run it locally, how to run tests |
| `AGENTS.md` | Purpose of the repository for agents, key entry points, local rules, copy of common rules from this skill |
| `Taskfile.yml` | All required tasks |
| `.env.example` | All required env keys with no values |
| `_AGENT/OPEN_POINTS.md` | Log of non-blocking deferred decisions and known gaps surfaced during implementation (see During Implementation). Created on first use, not required to pre-exist |

`.env` files are gitignored and must never be committed.
