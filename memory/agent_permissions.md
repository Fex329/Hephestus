# Agent Auto-Approval Permission Policy v2
> Author: Dominick (Security Engineer)
> Date: 2026-03-23
> Ticket: ISS-147
> Supersedes: `SitoPresepi2-archive/memory/agent_permissions.md` (tools-4, archived)
> Status: Active — Owner-approved (WRK-003, 2026-03-23)

---

## Purpose

This policy defines which tool calls and terminal commands AI agents (Claude, GitHub Copilot) may execute without prompting the Owner, and which require explicit Owner approval.

It also governs status transition authority, `settings.json` modification rules, and the recovery model that replaces approval prompts as the primary safety control.

**Trigger for this version:** Reorg-1 broke the previous policy's path assumptions. Arale's batch execution model, added since tools-4, required a scoped automation allowance. Owner-reported friction in interactive Claire sessions required widening file-edit and git approvals. All decisions recorded in WRK-003 (2026-03-23).

---

## Threat Model

This is a **trusted, single-operator environment** on a personal laptop.

| Risk | Likelihood | Impact | Control |
|---|---|---|---|
| AI executes file operations outside the workspace | Low | Medium | Hard rule: always prompt for out-of-workspace paths |
| Prompt injection via crafted file content | Low | Medium | Workspace boundary + pre-commit hooks catch consequences |
| Agent self-expands permissions | Low | Medium | Rule: no agent modifies `settings.json` without Owner approval |
| Accidental destructive git operation (reset, force-push) | Low | High | Keep Owner prompt for all destructive git commands |
| Credentials committed to repo | Low | High | detect-secrets / gitleaks in pre-commit hooks (ISS-090, resolved) |
| Direct pm.db manipulation bypassing audit trail | Low | Medium | All db writes via `ticket.py` CLI — enforced in all agent files |
| Dependency introduction without review | Low | Medium | pip/npm install always Owner-prompted |

**Key principle:** Approval prompts protect against AI scope creep, not external threats. The laptop's vulnerability to external attacks is unchanged by VS Code permission settings. The recovery model is git (reversible) + pre-commit hooks (last line of defence).

---

## The Three Permission Layers

| Layer | Mechanism | Scope |
|---|---|---|
| **File operations** (Edit/Write tools) | Claude Code tool approval model | Auto-approve within workspace paths |
| **Terminal commands** | `chat.tools.terminal.autoApprove` in `.vscode/settings.json` | Per allowlist below |
| **Git write operations** | Terminal auto-approve | Selective — see below |

**Layer 1 — File operations:** All Edit and Write tool calls within the workspace are auto-approved. The workspace boundary is the control. Any path outside the workspace always requires Owner prompt — this is the one non-negotiable rule in this policy.

---

## Terminal Command Allowlist

### Read-only / zero side-effect (always auto-approve)

```
git status
git log [any flags]
git diff [any flags]
git branch [any flags]
git show [any flags]
git fetch [any flags]
python ticket.py list [any flags]
python ticket.py view ISS-*
python ticket.py search *
python ticket.py sprint *
python handoff.py list [any flags]
python handoff.py latest
python message.py list [any flags]
python message.py view MSG-*
python --version
docker compose ps
docker compose logs [any flags]
```

### Git write operations — selective

```
# Auto-approve
git checkout <branch-name>          # branch switching only
git pull
git add <specific-files>            # feature branches only

git commit -m "..."                 # feature branches only
                                    # pre-commit hooks run — --no-verify is prohibited

# Owner prompt required
git push *
git merge *
git rebase *
git reset *
git stash *
git tag *
git checkout -- <file>              # file restoration form — destructive
```

**Constraint:** `git add` and `git commit` auto-approval applies to feature branches only (`feature/*`, `hotfix/*`). Any commit to `main` or `develop` requires Owner prompt.

### Test execution

```
# Auto-approve
python -m pytest tools/tests/ [any flags]
python -m pytest tools/tests/test_<specific_file>.py [any flags]
python -m pytest django/ [any flags]
docker compose --profile test run --rm test

# Constraint: pytest invocations must target test paths only.
# Confirm conftest uses isolated test DB (not production DB) before adding to allowlist.
```

### Linting and formatting

```
# Auto-approve
black --check .
black .
ruff check .
isort --check-only .
isort .
npm run lint
npx tsc --noEmit
```

### venv activation

```
# Auto-approve — both track paths
source development/tools/.venv/Scripts/activate
source development/presepi-site/.venv/Scripts/activate
source .venv/Scripts/activate        # relative form — also covered
```

**Note:** Use exact path matching in `settings.json` regex to cover both tracks. See Rule 3.

### Session bookkeeping

```
# Auto-approve — known callers only
python handoff.py write [any flags]   # caller: scrum | operation-manager | *-automated
python message.py create [any flags]  # caller: scrum | operation-manager | *-automated

# Owner prompt required for any other caller
```

### Docker

```
# Owner prompt required
docker compose up *
docker compose down *
docker compose build *
```

Rationale: `build` executes Dockerfiles (arbitrary code), `up` opens ports (network listener), `down` removes running containers.

### Frontend toolchain

```
# Auto-approve
npm run lint
npx tsc --noEmit

# Owner prompt required
npm run dev
npm run build
npm run test
npm ci
npm install
npm uninstall
```

### Package and dependency management

```
# Owner prompt required — all of the following
pip install *
pip uninstall *
npm ci
npm install *
npm uninstall *
```

Rationale: introduces third-party code onto the machine.

### Database and migration operations

```
# Owner prompt required — all of the following
python db_init.py *
python migrate_md_to_sqlite.py *
python manage.py migrate *
python manage.py makemigrations *
```

---

## Ticket CLI Operations

### Auto-approve by context

| Command | Auto-approve condition |
|---|---|
| `ticket.py update --field status --value ready --caller scrum` | Always — Rob promoting to ready |
| `ticket.py update --field status --value in-development --caller operation-manager` | Arale batch claim |
| `ticket.py update --field notes --value "..." --caller <any-automated>` | Gate evidence notes |
| `ticket.py update --field status --value in-testing --caller operation-manager` | Arale G3 verified |
| `ticket.py update --field status --value in-development --caller tech-lead` | Alessandro G5 rejection |

### Owner prompt required

| Command | Reason |
|---|---|
| `ticket.py create *` | New scope — always an Owner decision |
| `ticket.py close *` | Gate 7 — Owner sign-off required |
| `ticket.py reopen *` | Reversing a closed state — Owner decision |
| `ticket.py update --field status --value resolved` | Gate 7 — Owner only |
| `ticket.py update --field status --value wont-fix` | Scope decision |
| `ticket.py update --field status --value deferred` | Scope decision |
| `ticket.py update --field status --value ready --caller <not scrum>` | Only Rob may promote to ready without prompt |

---

## Status Transition Authority

| Transition | Who may execute without Owner prompt |
|---|---|
| `open` → `ready` | Rob (`--caller scrum`) |
| `ready` → `in-development` | Arale (`--caller operation-manager`) |
| `in-development` → `in-testing` | Arale (`--caller operation-manager`) |
| `in-testing` → `in-development` | Alessandro (`--caller tech-lead`) — G5 rejection only |
| Any → `resolved` | **Owner only** — Gate 7, no exceptions |
| Any → `wont-fix` / `deferred` | **Owner only** — scope decision |

---

## Agent Execution Protocol — G3 Gate

**G3 Owner gate is removed.** Owner prompt is required at G1 only.

- **G1 (plan):** Hard Owner gate. Owner approves the plan, files to touch, and test names. No shortcuts, no waivers. The entire downstream model depends on this gate being solid.
- **G2 (red tests):** Auto-approve. Evidence of TDD process compliance.
- **G3 (green implementation + commit):** Auto-approve. G3 is execution of the G1-approved plan. Alessandro catches any drift at G5. Pre-commit hooks are the last line of defence at commit time.

---

## The One Non-Negotiable Rule

> **Any file path outside the workspace always requires Owner prompt.**

This rule cannot be relaxed by any agent, any ticket, or any briefing. It applies to all tool calls (Edit, Write, Bash) regardless of any other auto-approve rule in this document.

---

## Rule: Preferred Pattern — Exact Regex Match

When a specific command must be auto-approved for a ticket's workflow, use an exact regex match rather than a prefix glob.

**Too broad (avoid):**
```json
"python": true
```

**Preferred (exact match):**
```json
"^python -m pytest tools/tests/test_sqlite_writer\\.py -q$": true
```

Blanket truthy rules are only acceptable for the read-only commands listed in the Read-only section above. All other auto-approvals must use `matchCommandLine: true` with a regex matching the exact intended invocation.

---

## Rule: settings.json Governance

`.vscode/settings.json` is a control file. Its modification is subject to the following constraints:

1. **No agent may self-modify `settings.json`.** An agent may propose an addition but may not execute the write without Owner approval.
2. **Every addition must reference a ticket.** Propose at G1; Owner approves as part of the G1 gate. Remove the rule when the ticket closes.
3. **Permanent rules** (commands in this document's allowlists) may be added to `settings.json` by the Owner at any time without a ticket — they are governed by this document.
4. **Commits to `settings.json` must use `chore(process):` Conventional Commit** citing the relevant ticket ID.

---

## Recovery Model

The removal of approval prompts does not remove safeguards — it moves them to more effective positions:

| Safeguard | What it catches | When it runs |
|---|---|---|
| **Pre-commit hooks** (Black, isort, Ruff, detect-secrets, gitleaks) | Formatting violations, credential exposure | Every `git commit` — `--no-verify` prohibited |
| **Git history** | Every file change is tracked and reversible | Continuous |
| **Feature branch isolation** | No change reaches `main`/`develop` without G5+G6 review | Every ticket |
| **Alessandro G5 review** | Code drift from G1 plan, architectural violations, test quality | Before merge |
| **Owner G7 sign-off** | Final acceptance before ticket resolves | Before `resolved` |

**All agents must include this reminder in their agent files:** Git is the recovery mechanism. Pre-commit hooks are the last line of defence. The absence of approval prompts does not mean the absence of controls.

---

## Summary Table

| Category | Auto-approve? |
|---|---|
| File Edit/Write within workspace | Yes |
| File Edit/Write outside workspace | **No — hard rule** |
| `ticket.py list/view/search/sprint` | Yes |
| `ticket.py create` | No — Owner prompt |
| `ticket.py update` (notes + status within batch) | Yes — known callers |
| `ticket.py update --field status --value ready` | Yes — `--caller scrum` only |
| `ticket.py update --field status --value in-development` | Yes — `--caller tech-lead` (G5 rejection) or `operation-manager` |
| `ticket.py update --field status --value resolved` | No — Owner only |
| `ticket.py close / reopen` | No — Owner prompt |
| `handoff.py list/latest` | Yes |
| `handoff.py write` | Yes — known callers only |
| `message.py list/view` | Yes |
| `message.py create` | Yes — known callers only |
| `pytest tools/tests/` | Yes |
| `pytest django/` | Yes — test DB confirmed |
| `docker compose --profile test run --rm test` | Yes |
| `docker compose ps / logs` | Yes |
| `docker compose up / down / build` | No — Owner prompt |
| `git status/log/diff/branch/show/fetch` | Yes |
| `git checkout <branch>` | Yes |
| `git checkout -- <file>` | No — Owner prompt |
| `git pull` | Yes |
| `git add` / `git commit` (feature branch) | Yes |
| `git add` / `git commit` (main/develop) | No — Owner prompt |
| `git push / merge / rebase / reset` | No — Owner prompt |
| `black / isort / ruff` | Yes |
| `npm run lint` / `npx tsc --noEmit` | Yes |
| `npm run dev / build / test` | No — Owner prompt |
| `npm ci` / `npm install / uninstall` | No — Owner prompt |
| `pip install / uninstall` | No — Owner prompt |
| `manage.py migrate` / `makemigrations` | No — Owner prompt |
| `db_init.py` / `migrate_md_to_sqlite.py` | No — Owner prompt |
| Blanket `python *` | No — non-compliant; use exact regex |
| Any unlisted command | No — Owner prompt (default) |
