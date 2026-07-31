# Architecture Mode

Use this mode when the user wants to document how the software works: components, layers, deployment, or how a feature is implemented.

## Document Types

| Type | C4 Level | Scope |
|---|---|---|
| `context` | L1 | System and external actors |
| `container` | L2 | Deployable units and their interactions |
| `component` | L3 | Internal components within a container |
| `code` | L4 | Implementation detail of a specific component |
| `feature` | cross-cutting | How a specific feature works end-to-end |
| `deployment` | infra | How the system is deployed and operated |
| `adr` | decision | A single architectural decision record |

## Rules

- Every document starts with a YAML front matter block.
- Write in clear prose. Use sections and paragraphs, not structured key-value lists.
- State **what** and **why**, not just **how**.
- Each document covers one subject. Split if scope grows.
- No em dashes.
- Every component introduced in any ARCH document must be defined in `ARCH-000-component-registry.md`. If the registry does not exist, create it first.
- In ARCH documents, refer to components by name (matching the registry). Do not repeat registry fields (type, technology, responsibility) in the body.

## Assumptions

Whenever a design decision relies on an assumption that has not been explicitly confirmed by the user, an Architecture Assumption Record (AAR) must be created. Do not silently embed assumptions in documents.
This only applies to important not trivial assumptions.

- AAR files live in `docs/architecture/assumptions/`
- Filename: `AAR-XXX-<slug>.md` (scan the folder for the highest existing ID)
- Every AAR is referenced from the document that depends on it

### AAR Format

```markdown
---
id: AAR-XXX
title: <Short Title>
status: <open | confirmed | invalidated>
---

## Assumption

<State the assumption clearly and concisely.>

## Rationale

<Why this assumption was made. What information was missing that forced it.>

## Impact if Wrong

<What would need to change in the architecture if this assumption turns out to be incorrect.>

## Resolution

<Leave blank while status is open. Fill in when confirmed or invalidated, including who confirmed it and when.>
```

## Workflow

1. Read `project.md` for system context.
2. Identify the document type and C4 level from the user's description.
3. If the scope spans multiple types, confirm the split before writing.
4. **For feature documents:** challenge the description before writing. Ask clarifying questions about anything ambiguous, underspecified, or where an assumption would be required. Do not assume behavior, ownership, data flow, security boundaries, or failure modes. Apply software engineering best practices (single responsibility, clear ownership, failure handling, idempotency) and security best practices (least privilege, no credential persistence, audit trail, input validation) as lenses when forming questions. Only proceed once answers are sufficient to write without assumptions.
5. **Before writing any document:** scan existing ARCH documents and ADRs for conflicts — overlapping responsibilities, contradictory constraints, or decisions already made differently. If a conflict is found, surface it and ask for clarification before proceeding. Do not silently override or ignore existing decisions.
6. Assign the next sequential ID (scan existing files to find the next `ARCH-XXX` or `ADR-XXX`).
7. Check if `ARCH-000-component-registry.md` exists. If not, create it first.
8. Add any new components introduced by this document to `ARCH-000-component-registry.md`.
9. Write the document in prose using the format for its type.
10. For decisions, always write an ADR regardless of whether a broader doc is also produced.
11. For every assumption made during design, create an AAR in `docs/architecture/assumptions/` and reference it from the document. Prefer asking the user over making assumptions, but when an assumption is unavoidable, record it immediately.

## ID Sequences

- Architecture docs: `ARCH-XXX` (scan `docs/architecture/` for the highest existing ID)
- ADRs: `ADR-XXX` (scan `docs/architecture/decisions/` for the highest existing ID)

## Document Format — Architecture Doc

Filename: `ARCH-XXX-<slug>.md`

```markdown
---
id: ARCH-XXX
title: <Short Title>
type: <context | container | component | code | feature | deployment>
c4_level: <L1 | L2 | L3 | L4 | cross-cutting | infra>
status: <draft | review | approved>
tags: []
---

## Purpose

<One paragraph: what this document describes and why it exists.>

## Overview

<Prose description of the subject: what it is, what problem it solves, how it fits into the broader system.>

## Components

<For each component in scope, one paragraph describing what it does and why it exists. Reference components by name as defined in ARCH-000. Do not repeat type/technology/responsibility from the registry.>

## Interactions

<Prose description of how components communicate: protocols, data flows, sequence of operations where relevant.>

## Constraints and Decisions

<Prose or short list of key constraints and decisions. Link to ADRs where applicable.>

- <constraint or decision> → see [ADR-XXX](decisions/ADR-XXX-<slug>.md)
```

## Document Format — ADR

Filename: `ADR-XXX-<slug>.md` (in `docs/architecture/decisions/`)

```markdown
---
id: ADR-XXX
title: <Short Title>
status: <proposed | accepted | deprecated | superseded>
supersedes: []
superseded_by: []
tags: []
---

## Context

<What situation or problem forced this decision.>

## Decision

<What was decided, stated clearly.>

## Consequences

### Positive
- <benefit>

### Negative
- <tradeoff or cost>

## Alternatives Considered

- **<Alternative>:** <why it was rejected>
```
