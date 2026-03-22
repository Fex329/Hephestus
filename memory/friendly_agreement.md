# Friendly Agreement — AI Handoff Protocol
> Version: 2.0 | Updated: 2026-03-16 | Scope: All AI agents working in SitoPresepi2
> Discussion history → `memory/workspace/shared/ai_discussion_log.md`

---

## Role Split (owner-approved 2026-03-16)

| AI | Primary responsibility |
|---|---|
| **Claude** | Planning, architecture, gate analysis, cross-document reasoning, default reviewer for G5/G6/G7, role-play of all named agents |
| **GitHub Copilot** | In-editor implementation, targeted edits, refactors, test execution against an approved plan |

**Rule:** role-first, tokens-second. If Claude tokens are exhausted mid-planning, Copilot holds the line using the handoff log and keeps scope narrow. Flag any non-preferred work in the handoff entry.

---

## Definitions

**Session:** any continuous block of work with one AI ending when the owner closes the chat or switches tool. Copilot inline completions without a chat window are NOT sessions — no handoff needed.

**Conflict resolution:** if the arriving AI finds recorded state is wrong — flag to owner, propose fix, wait for approval. Do not self-correct without owner confirmation.

**Error escalation:** trivial error (typo, wrong date) → fix inline, note in commit. Substantive error (bug, bad architectural call) → raise ticket + write to discussion log.

---

## Binding Governance (read at every session start)

| Document | Path | Read when |
|---|---|---|
| Constitution | `memory/constitution.md` | Every session |
| AI Standards | `memory/ai-standards.md` | Every session |
| Issue Tracker | `memory/pm.db` (SQLite) | Before any ticket work — query via `python ticket.py list` |
| Latest handoff | `memory/handoff/YYYY-MM-DD.md` (most recent) | Every session |
| Architecture | `memory/workspace/site/architecture_document.md` | Before backend/devops work |
| Agent Permissions | `memory/agent_permissions.md` | Before modifying `.vscode/settings.json` or adding any auto-approve rule |

**Role definitions:** `.github/agents/*.agent.md` is the canonical source for both AIs. Read the relevant agent file when a role is invoked — do not rely on internal role tables.

---

## Non-negotiable Rules

1. **No ticket = no work.** No code or file change without a ticket.
2. **TDD always.** Failing test first. No exceptions.
3. **Gates G1–G7 are owner-blocking.** No gate self-approved.
4. **No business logic in views.** `services.py` only.
5. **No credentials in code.** `.env` only, never committed.
6. **Tickets via CLI only.** `python ticket.py create` / `python ticket.py update`.

---

## Session Start Checklist

- [ ] Read `memory/constitution.md` — any amendments since last session?
- [ ] Read `memory/ai-standards.md` — any standard changes?
- [ ] Read latest `memory/handoff/YYYY-MM-DD.md` — what did the previous AI leave?
- [ ] Run `python ticket.py list --status in-development` and `--status ready` — what is active?
- [ ] Confirm active sprint and next priority with owner before touching any code.

---

## Session End Checklist

- [ ] All ticket state changes committed via `python ticket.py update`.
- [ ] Any architectural decisions noted in `memory/constitution.md` decisions log.
- [ ] New tickets raised via `python ticket.py create`.
- [ ] Handoff entry written via `python handoff.py write` (ISS-037). Until ISS-037 ships: write structured block below manually.

---

## Handoff Log
> Prepend newest entry first (owner-authorized 2026-03-16). Keep existing historical entries unchanged. Use fixed fields — no prose substitution.
> Once ISS-037 ships, `handoff.py` writes here automatically.

```
Date:             2026-03-16
AI:               Claude (Salvatore / Orchestrator)
Tickets touched:  ISS-036 (resolved), ISS-037, ISS-038, ISS-039 (raised)
Gate status:      ISS-036 fully resolved. ISS-037/038/039 open, unstarted.
Tests:            34/34 passing (tools/ + memory/) as of 2026-03-16
Blockers:         ISS-034 (pipe separator defect, high) — still unstarted
                  ISS-006 (IA) unstarted — blocks all frontend work
Next action:      Owner to confirm: ISS-034 or ISS-006 next?
                  ISS-037 (handoff.py) should be prioritised — protocol depends on it.
```

---

## Quality Commitments

- Black + isort + Ruff on all Python. TypeScript strict mode on all Next.js.
- Test naming: `test_<subject>_<condition>_<expected_result>`
- Precise technical language. Present options with trade-offs. Flag gate approvals.
- When unsure: read the relevant memory file first. If still unsure: state it, propose options, ask.

---

## Notes for GitHub Copilot

You are the **implementer**. When invoked as a named role (e.g. `@backend`), read `.github/agents/backend.agent.md` — that is the canonical definition. Follow the Agent Execution Protocol gates. Record gate outcomes via `python ticket.py update`. Do not duplicate work already in ticket notes or session files.

## Notes for Claude

Check the handoff log above before doing anything. Do not re-run gates already confirmed. If a gate outcome is ambiguous, ask the owner before proceeding. You play all named agent roles — read the relevant `.github/agents/*.agent.md` file when a role is invoked.
