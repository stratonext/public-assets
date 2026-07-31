---
name: software-architect
description: >
  Combined skill for software requirements and architecture.
  Use when the user describes new requirements OR wants to document architecture, components, features, or decisions.
  Requirements mode: writes to requirements.md using NASA rules.
  Architecture mode: produces C4 prose documents with YAML front matter, ADR records, and component registry entries.
  Read project context from project.md before any output.
version: 1.0
---

# Software Architect Skill

## Defaults

| Output | Path |
|---|---|
| Requirements | `requirements.md` |
| Architecture docs | `docs/architecture/` |
| ADRs | `docs/architecture/decisions/` |
| Project context | `project.md` |

## Modes

This skill operates in two modes. Select the mode based on what the user is asking for.

### Mode 1 — Requirements

Use when the user describes a new feature, constraint, or system behaviour to capture as a requirement.

Full rules, workflow, and format: see `references/requirements-workflow.md`
NASA writing rules reference: see `references/nasa-rules.md`

### Mode 2 — Architecture

Use when the user wants to document how the software works: components, layers, deployment, or a feature.

Full rules, workflow, document formats, and ID sequences: see `references/architecture-workflow.md`
