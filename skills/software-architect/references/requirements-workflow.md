# Requirements Mode

Use this mode when the user describes a new feature, constraint, or system behaviour that needs to be captured as a requirement.

See `nasa-rules.md` for the full NASA requirements writing rules.

## Rules Summary

- `Shall` = requirement, `Will` = fact, `Should` = goal
- Active voice: `<Product> shall <verb> <what>`
- One subject, one predicate per requirement — no compound statements
- State **what**, not **how**
- No unverifiable terms (easy, fast, sufficient, flexible, appropriate, etc.) — replace with measurable thresholds or flag as **TBR**
- Goals without thresholds use `should` and get a TBR note
- No em dashes

## Workflow

1. Read the user's description and identify how many distinct requirements it contains. Confirm the split before writing.
2. Ask for clarification on any unverifiable terms or ambiguous scope before writing.
3. Assign the next sequential `REQ-XXX` number (scan `requirements.md` for the highest existing number).
4. Append to `requirements.md` using the format below.
5. Cross-reference related existing requirements where relevant.

## Validation Checklist (apply before writing)

- Is the requirement free of implementation detail?
- Does it have one subject and one predicate?
- Is every performance value accompanied by a tolerance or marked TBR?
- Is it verifiable — can compliance be tested, demonstrated, or measured?
- Is it consistent with existing requirements?

## Requirement Format

```markdown
## REQ-XXX — Short Title

<Product> shall <what the system must do>.

> **TBR:** <if a threshold or standard is missing, explain what needs to be defined and by whom>
> **Note:** <cross-reference to related requirements if applicable>
```
