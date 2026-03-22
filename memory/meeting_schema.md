# Meeting Schema — SitoPresepi2
> Approved: 2026-03-13. Living standard — extend when patterns require it.

---

## Header Format

Every formal meeting file must open with this header block (one field per line):

```
ID: ARQ-001
Type: architecture-review
Tags: data-model, payment-stub, css
Date: 2026-03-13
Chair: Orchestrator
Participants: Patrick, Dominick, Alessandro, Ash, Owner
Ticket: —
Sprint: —
Status: closed
```

All fields are required. Use `—` for nullable fields with no value.

---

## Field Definitions

| Field | Required | Notes |
|---|---|---|
| ID | Yes | `PREFIX-NNN` — see ID Prefix Convention below |
| Type | Yes | Controlled enum — see Meeting Types |
| Tags | Yes | Free-text, comma-separated. Primary grep target. Use topic keywords: `security`, `rbac`, `admin`, `data-model`, etc. |
| Date | Yes | ISO 8601 — `YYYY-MM-DD` |
| Chair | Yes | Who facilitated |
| Participants | Yes | Comma-separated names/roles. Include Owner if present. |
| Ticket | Nullable | ISS-NNN if meeting was triggered by or resulted in a specific ticket. `—` otherwise. |
| Sprint | Nullable | Sprint identifier (e.g. `sprint-1`) if applicable. `—` in pre-sprint phase. |
| Status | Yes | `open` / `closed` / `draft` |

---

## Meeting Types (controlled)

| Value | Use for |
|---|---|
| `architecture-review` | Technical architecture decisions |
| `ba-review` | Requirements review or clarification |
| `security-session` | Security baseline, threat model, or security decisions |
| `working-session` | General topic that doesn't fit another type |
| `scrum-ceremony` | Sprint planning, retrospective, review, standup |

---

## ID Prefix Convention

| Prefix | Type |
|---|---|
| `ARQ` | architecture-review |
| `BAR` | ba-review |
| `SEC` | security-session |
| `WRK` | working-session |
| `SCR` | scrum-ceremony |

IDs are sequential per prefix: `ARQ-001`, `ARQ-002`, etc.

---

## File Naming

Formal meeting minutes: `memory/workspace/meeting_<prefix_lower>_<NNN>.md`

Examples: `meeting_arq_001.md`, `meeting_bar_001.md`, `meeting_scr_001.md`

Note: `session_*` files in `memory/workspace/` are informal working session notes, not formal meeting minutes. They follow a different, looser format and are not subject to this schema.

---

## Schema Extension

Add a new meeting type or field by:
1. Proposing the change to the project owner
2. Updating this file on approval
3. Retrofitting the new field to existing meeting files if applicable

Do not invent values not in the controlled lists.
