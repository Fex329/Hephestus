# AI Standards — SitoPresepi2

## How AI Agents Must Behave in This Project

### Communication Rules
- Always explain WHAT you are about to do before doing it
- Always explain WHY a choice is being made
- If multiple options exist, present them — do not pick one silently
- Flag uncertainty explicitly rather than guessing

### What AI Must NOT Do
- Create files or folders without explicit instruction
- Choose a tech stack, library, or tool without approval
- Refactor or "improve" code beyond what was requested
- Add comments, docs, or features that were not asked for
- Make assumptions about design, UX, or content

### What AI MUST Do
- Work in the smallest possible steps
- Confirm scope before starting any task
- Update the `constitution.md` decisions log when a major choice is made
- Use the memory files to stay consistent across sessions

### Owner Profile — Communication Standard
The project owner is a technical professional (corporate infrastructure, identity management, development background). They operate this site permanently as sole developer and maintainer.

AI agents **must**:
- Use precise technical language — no consumer-grade simplifications
- Reference architecture sections by number when relevant
- Assume familiarity with: Django, REST APIs, Docker, Nginx, PostgreSQL, reverse proxies, Git workflows
- Skip explanations of standard concepts unless asked
- Flag trade-offs at a technical level, not a beginner level

AI agents **must not**:
- Over-explain standard tooling
- Soften technical feedback for readability
- Assume the admin interface must be non-technical-user-friendly (the owner is the operator)

### Ticket Creation Rules
- When creating a ticket, always use `ticket.py create` — never manipulate `memory/pm.db` directly
- Controlled fields (Type, Status, Urgency, Severity, Component, Project) must use values defined in `pm/ticket_schema.py`
- If no valid enum value fits, **stop and request a schema extension** from the project owner — do not invent new values
- Owner, Tags, and Sprint are free-text — use sensible values consistent with existing tickets
- Severity is required for `type=defect`; use `—` for all other types
- Component is always required — select the Django app or cross-cutting area the ticket relates to
- Notes are auto-timestamped by `ticket.py create` on creation; subsequent note entries appended manually must follow format: `YYYY-MM-DD Author: text`

---

## Agent Roles

> **Canonical role definitions live in `.github/agents/<role>.agent.md`.** Those files are the authoritative source for both Claude and GitHub Copilot — responsibilities, gate ownership, communication style, and read-first instructions are all there.
>
> The table below is a navigation index only. Do not treat it as a role specification. If a summary here conflicts with the agent file, the agent file wins.

| Slash command | Agent file | Role |
|---|---|---|
| `/architect` | `architect.agent.md` | Patrick — Solution Architect |
| `/tech-lead` | `tech-lead.agent.md` | Alessandro — Tech Lead |
| `/backend` | `backend.agent.md` | Claire — Backend Developer |
| `/frontend` | `frontend.agent.md` | Ash — Frontend Developer |
| `/fullstack` | `fullstack.agent.md` | Chris — Senior Full Stack Developer |
| `/ux` | `ux.agent.md` | Sarah — UX/UI Designer |
| `/security` | `security.agent.md` | Dominick — Security Engineer |
| `/devops` | `devops.agent.md` | Salvatore — DevOps Engineer |
| `/ba` | `ba.agent.md` | Fran — Business Analyst |
| `/po` | `po.agent.md` | Sofia — Product Owner |
| `/qa` | `qa.agent.md` | Rich — QA Engineer |
| `/sdet` | `sdet.agent.md` | Lauren — SDET / Senior QA Lead |
| `/scrum` | `scrum.agent.md` | Rob — Scrum Master |
| `/content` | `content.agent.md` | Helios — Content Strategist |
| `/seo` | `seo.agent.md` | Martina — SEO Specialist |

---

## Technology Stack
> Approved 2026-03-12. See constitution.md decisions log.

| Layer | Technology | Notes |
|---|---|---|
| API | Django 5.2.x (>=5.2.8) + Django REST Framework 3.x | Python 3.14.3 |
| Frontend | Next.js 14.x (App Router) | TypeScript strict mode |
| Admin | Custom Django app | Isolated from public API |
| Database | PostgreSQL 16.x | |
| Reverse proxy | Nginx (latest stable) | |
| Containers | Docker + Docker Compose | |
| Image storage | Local filesystem + django-storages | |
| Auth (admin) | Django sessions + django-otp TOTP | |
| Auth (customer, v2) | SimpleJWT | Separate domain from admin |
| Testing (Django) | pytest + pytest-django | |
| Testing (Next.js) | Jest + React Testing Library | |
| Hosting | DigitalOcean Frankfurt, 2-core/4GB | ~€18/month |

---

## Repository and Branching
> Approved 2026-03-13. Updated 2026-03-23 — all repos migrated to GitHub as part of reorg-1 (ISS-129–ISS-141).

### Repository structure
```
c:\temp\ClaudeProjects\
├── repos\                    ← legacy bare repos (archived — no longer origin for any repo)
├── hephestus\                ← governance repo → github.com/Fex329/Hephestus  (origin)
├── development\
│   ├── tools\                ← PM tooling repo  → github.com/Fex329/Tools      (origin)
│   ├── presepi-site\         ← site code        → github.com/Fex329/SitoPresepe (origin)
│   └── worktrees\            ← ephemeral agent worktrees (gitignored)
├── docs\                     ← documentation    → github.com/Fex329/Docs        (origin)
├── backoffice\               ← operational files → github.com/Fex329/Backoffice (origin)
└── SitoPresepi2\             ← legacy monorepo (archived after ISS-141)
```

All 5 repos use `origin` pointing to GitHub (SSH). The `repos\` bare repo folder is retained on disk but is no longer the origin for any active repo.

### Branch model — development/presepi-site (site code)
```
main        Production only. Tagged on every deployment.
develop     Integration branch. Tests must always be green here.
feature/*   Branched from develop. Merged to develop via PR.
hotfix/*    Branched from main. Merged to main AND develop.
```

### Branch model — hephestus + development/tools (governance and PM/tooling) [ISS-036, 2026-03-16]
```
main        Stable governance and tooling. Direct commits permitted for trivial fixes.
feature/*   Branched from main. Merged to main via PR for any non-trivial change.
```
No `develop` branch — there is no deployment step for PM files.
Upgrade to full GitFlow (add `develop`) if parallel PM work requires it.

### Branch naming
`feature/short-kebab-description`, `fix/description`, `test/description`,
`chore/description`, `hotfix/description`

---

## pm Package Conventions
> Established ISS-016 / TASK-A, 2026-03-14. Path updated ISS-138 reorg-1.

The `pm/` package (at `development/tools/pm/`) is the sole home for project management logic. CLI scripts at `development/tools/` root call `pm` functions — they do not contain logic themselves.

### Structure
```
pm/
├── __init__.py           # re-exports all public names
├── protocols.py          # BacklogReader + BacklogWriter + MessageWriter Protocols
├── md_reader.py          # MarkdownBacklogReader — markdown implementation of BacklogReader
├── md_writer.py          # MarkdownBacklogWriter — markdown implementation of BacklogWriter
├── md_message_writer.py  # MarkdownMessageWriter — markdown implementation of MessageWriter
└── ticket_schema.py      # Ticket + Message dataclasses + all enums (format-agnostic domain model)
```

### Import convention
```python
from pm import BacklogReader, BacklogWriter              # Protocols via __init__
from pm import MarkdownBacklogReader, MarkdownBacklogWriter  # concrete classes via __init__
from pm.protocols import BacklogReader                   # direct module import (also valid)
from pm.md_reader import MarkdownBacklogReader           # direct module import (also valid)
```

### Rules
- `pm/` is pure Python — no Django, no database, no HTTP
- CLI scripts (`ticket.py`, etc.) at `development/tools/` root call `pm` functions — never the reverse
- Every public function in `pm/` must have at least one test in `tests/`
- `tests/` mirrors `pm/` — one test file per module: `test_md_reader.py`, `test_md_writer.py`, `test_md_message_writer.py`
- Pre-existing scripts (`dispatch_reader.py`, `issue_reader.py`) are excluded from coverage until their own tickets are raised

---

## Coding Standards

### Python / Django
- **Formatter:** Black (zero config)
- **Imports:** isort (Black-compatible profile)
- **Linter:** Ruff
- **Pre-commit:** All three run via pre-commit hooks before every commit

**Naming**
- Files/modules: `snake_case.py`
- Classes: `PascalCase`
- Functions/variables: `snake_case`
- Constants: `SCREAMING_SNAKE_CASE`
- Private: `_leading_underscore`
- URL patterns: `kebab-case`

**App structure** — every Django app:
```
app_name/
├── models.py       # models only — no business logic
├── services.py     # all business logic
├── serializers.py
├── views.py        # thin — calls services only
├── urls.py
└── tests/
    ├── test_models.py
    ├── test_services.py
    └── test_views.py
```

**Rule: no business logic in views. Enforced in every code review.**

### TypeScript / Next.js
- **Language:** TypeScript, strict mode (`"strict": true` in tsconfig)
- **Formatter:** Prettier
- **Linter:** ESLint (Next.js recommended config)

**Naming**
- Components: `PascalCase.tsx`
- Hooks: `camelCase.ts` with `use` prefix
- Utilities: `camelCase.ts`
- Types/interfaces: `PascalCase` — no `I` or `T` prefix
- Constants: `SCREAMING_SNAKE_CASE`

**`lib/types.ts` is the API contract file.**
TypeScript types here must mirror Django serializer output exactly.
Both Backend and Frontend agents are responsible for keeping them in sync.

### Pattern-Fix Tickets — Search Before Close
> Retro action reorg-1. Applies to all implementers, all languages.

When a ticket asks to fix a pattern — stale paths, wrong imports, broken references, renamed symbols, or any other systemic inconsistency — the implementer **must** search for all instances of the pattern before marking the ticket done. A fix applied to one location while the same problem exists elsewhere is an incomplete fix.

Required steps:
1. Run a project-wide search (e.g. `grep -r`, `rg`, or the Grep tool) for the affected pattern before starting.
2. Fix every instance found — not just the one that triggered the ticket.
3. Record the search command and the number of instances found in the ticket notes.

A ticket that fixes one instance and misses others **does not pass G3**.

---

## Terminal and Environment Rules

### venv — mandatory for all Python commands

The tooling track uses a venv at `development/tools/.venv`. **All agents must activate it before running any Python command in the terminal.** This applies to every agent, every session — do not assume it is already active.

```bash
# Activate (Git Bash / Unix shell) — from development/tools/
source .venv/Scripts/activate

# Verify
python --version   # must show 3.14.3
```

**Rule:** If you are about to run `python`, `pytest`, `ticket.py`, or any other Python command, check that the venv is active first. If it is not, activate it. Never run Python commands against the system interpreter.

### cd && chaining — prohibited

**Never write `cd /path && command` as a single shell string.** In both Claude Code Bash tool calls and VSCode terminal sessions, this pattern breaks auto-approval because the auto-approve rules match on command string prefix — a string starting with `cd` never matches `^python ticket.py` or `^git`.

**Prohibited pattern — do not do this:**
```bash
# BAD — the full string starts with "cd", not "python"
cd c:/temp/ClaudeProjects/development/tools && python ticket.py list

# BAD — the full string starts with "cd", not "git"
cd c:/temp/ClaudeProjects/hephestus && git status
```

#### Claude Code Bash tool

cwd does **not** persist between separate Bash tool calls. Each invocation resets to the session default cwd — a `cd` in one Bash call has no effect on any subsequent call. Use a **single Bash call with two lines** — no `&&`:

```bash
# BAD — two separate Bash calls; the cd in call 1 is lost before call 2 runs
# Call 1:
cd c:/temp/ClaudeProjects/development/tools
# Call 2 (cwd already reset — cd above had no effect):
python ticket.py list
```

```bash
# GOOD — single Bash call, two lines; one shell process, cd on line 1 affects line 2
cd c:/temp/ClaudeProjects/development/tools
python ticket.py list
```

This is **one Bash tool invocation** with two lines in its body — not two separate calls.

#### VSCode terminal (Copilot / Arale)

cwd **does** persist between separate terminal commands in the same session. Run the `cd` as a first terminal command; run the actual command as a second terminal command — two separate calls, cwd carries over:

```bash
# BAD — single chained string; starts with "cd", never matches auto-approve prefix
cd c:/temp/ClaudeProjects/development/tools && python ticket.py list
```

```bash
# GOOD — two separate terminal commands; cwd persists between them
# Terminal command 1:
cd c:/temp/ClaudeProjects/development/tools
# Terminal command 2:
python ticket.py list
```

The `cd` navigation commands for workspace roots are auto-approved in `settings.json` — Copilot/Arale can run the `cd` step without prompting.

Applies to all auto-approved commands: `ticket.py`, `handoff.py`, `message.py`, `git`, `pytest`, and any other command on the auto-approve allowlist.

---

## Testing Standards
> TDD adopted as a governing principle (Constitution §6).

### Django
- Test naming: `test_<subject>_<condition>_<expected_result>`
- Structure: Arrange / Act / Assert
- Fixtures: pytest fixtures in `conftest.py` — no Django XML fixtures
- DB tests: `@pytest.mark.django_db`
- Test files: in `tests/` subfolder of each app

### Next.js
- Test files: co-located with component (`ProductCard.test.tsx`)
- Test: renders without crashing, correct content, user interactions, mocked API data
- Do NOT test: CSS, visual appearance, pixel layout

### Minimum test
The smallest test that proves the behaviour. Not exhaustive edge cases upfront.
No "I'll add tests later" — ever.

---

## Code Review Process
> Two-reviewer model approved 2026-03-13.

Every PR receives two independent reviews before merge:

| Reviewer | Scope |
|---|---|
| Tech Lead (`/tech-lead`) | Architecture alignment, boundary violations, standards, security, commit format |
| Domain specialist | Django PRs → `/backend` agent. Next.js PRs → `/frontend` agent. |

Reviews are **independent** — domain specialist reads code fresh, without seeing Tech Lead comments first.

### PR description (required fields)
1. What this does
2. Tests written (list function names + what they prove)
3. Architecture alignment (which section of architecture_document.md)
4. Anything unusual

### Merge checklist
- [ ] PR description complete
- [ ] Tests written first (TDD)
- [ ] All tests passing
- [ ] No business logic in views
- [ ] No secrets in diff
- [ ] No app boundary violations
- [ ] Conventional Commits format
- [ ] `lib/types.ts` updated if API shapes changed

---

## Agent Execution Protocol
> Approved 2026-03-14. Applies to all implementation tickets, all sprints, all tracks.

Each gate specifies whether Owner acknowledgment is required — follow the "Owner action" column exactly. No gate may be skipped. Owner-blocking gates are: **G1, G4, G5, G6, G7**. G2 and G3 are auto-proceed evidence gates.

> **G2 and G3 are not Owner gates.** G1 is the commitment gate — the Owner approves the plan, scope, and files at G1. G2 and G3 are evidence gates — agents proceed automatically. Agents auto-advance to `in-testing` after a green G3. Alessandro catches any drift at G5.

### Gates (in order)

| Gate | Agent reports | Owner action |
|------|--------------|--------------|
| **G1 — Analysis** | Task interpretation, implementation plan, files to be touched, risks/unknowns. **Alessandro must confirm feature branch created before approving** (see G1 Approval Template below) | Approve plan or redirect — **blocker** |
| **G2 — TDD** | Failing test written and run — paste full red output | *(not Owner-blocking — TDD evidence. Proceed to G3 automatically.)* |
| **G3 — Implementation** | Code written, tests green — full output + coverage % committed. Advance to `in-testing` automatically. | *(not Owner-blocking — G1 is the commitment gate. Alessandro catches drift at G5.)* |
| **G4 — Failure** *(if applicable)* | What failed, root cause analysis, remediation strategy chosen | Approve strategy — **blocker** |
| **G5 — Tech Lead review** | Alessandro posts review findings (approve or request changes) | Owner acknowledges — **blocker** |
| **G6 — Domain review** | Domain specialist posts independent review findings, runs full test suite on branch, then merges feature branch to `main` via `--no-ff`. Merge only if G5 and G6 both approved in the same review cycle. | Owner acknowledges — **blocker** |
| **G7 — PO acceptance** | Agent sets status to `po-acceptance`, posts what was built against each acceptance criterion | **Step 1:** Sofia (PO) reviews and posts acceptance against each criterion. **Step 2:** Owner gives final sign-off → `resolved`. Both steps required — **blocker** |

### Status transitions
`ready` → *(G1 approved)* → `in-development` → *(G2+G3 approved)* → `in-testing` → *(G5+G6 approved)* → `po-acceptance` → *(G7 signed off)* → `resolved`

### Escalation gate
At **any point**, if an agent is blocked, uncertain, or encounters unexpected complexity, it **must stop immediately** and escalate — to the owner directly, or by requesting another agent role be invoked for questions or brainstorming. Guessing or proceeding without clarity is not permitted.

Escalation is a **blocker**: the agent may not continue on the ticket until the escalation is resolved and the owner explicitly says to proceed.

### G1 Approval Template
> Approved ISS-054, tools-2. Mandatory for Alessandro on every G1.

Alessandro must use this exact structure when posting a G1 approval. No G1 is approved without the branch line:

```
G1 APPROVED — ISS-XXX

- Branch `feature/iss-xxx-short-description` created from [base branch] — confirmed
- Plan interpretation: [one-line summary of what the ticket delivers]
- Files to be touched: [list]
- Concerns: [none / description]

Go ahead to G2.
```

**Rules:**
- Branch confirmation is line one — non-negotiable
- If the implementer has not yet created the branch, Alessandro asks for it before approving
- The branch name must match the convention: `feature/iss-NNN-short-kebab-description`
- Applies to all tracks (tooling and site), all implementers, all ticket types

**Base branch by track (ISS-056, approved 2026-03-17):**
- `tooling` track → branch from `main`
- `site` track → branch will be specified when the site track opens (likely `develop` — TBD at site-1 planning)
- When in doubt, state the base branch explicitly in the G1 approval — never assume

### Reviewer Assignment — When Alessandro is the Implementer
> Approved 2026-03-17 (ISS-074). Formalised after tools-2 retrospective brainstorm.

The standard model (G5 = Alessandro, G6 = domain specialist) creates a self-review conflict when Alessandro is the implementer. The following rules apply instead:

| Ticket type | G5 | G6 |
|---|---|---|
| Implementation (code changes) | Chris | Athena |
| Process / architecture (`ai-standards`, gate protocol, agent files) | Patrick | Athena |
| Documentary only (no behavioural change) | Owner waiver — single reviewer | — |

**Classification rule:** If the ticket changes behaviour that agents are expected to follow → Patrick G5. If it changes code that agents use as tooling → Chris G5.

**Athena at G6:** reviews whether the implementation matches the G1 plan, complies with `ai-standards`, and raises any process or architecture concerns the G5 reviewer missed.

**Alessandro has no gate role on his own tickets at any gate — no exceptions.**

### Sprint Close — Zero Open Ticket Check
> Retro action reorg-1. Mandatory for all roles, every sprint.

Before any sprint is closed, the responsible agent **must** run the sprint check command. Use the form appropriate for your execution context:

**VSCode terminal (Copilot / Arale) — two separate terminal commands:**
```bash
# Terminal command 1 (cd is auto-approved; cwd persists to next command):
cd c:/temp/ClaudeProjects/development/tools
# Terminal command 2:
python ticket.py sprint <sprint-name>
```

**Claude Code Bash tool — single Bash call, two lines:**
```bash
cd c:/temp/ClaudeProjects/development/tools
python ticket.py sprint <sprint-name>
```

Confirm that zero tickets remain in an open status (`open`, `in-development`, `in-testing`, `in-refinement`, `po-acceptance`). If any open tickets are found, they must be resolved, deferred, or explicitly carried forward to the next sprint before the sprint is marked closed. Do not close a sprint with silently lingering tickets.

### Ticket notes
Each gate transition must be recorded in the ticket notes with format:
`YYYY-MM-DD <role>: [GATE N] <summary of what was reported and owner response>`

> **Canonical format is `[GATE N]` — no variations permitted.**
> Alternatives such as `G5:`, `GATE 5:`, or `[G5]` are invalid.
> The `ticket.py list --gate` filter performs a substring match on `[GATE N]` markers; any deviation silently breaks gate-queue visibility.

**Gate notes are cumulative — never replace.** When updating the notes field at any gate, include all prior gate notes followed by the new entry. The `ticket.py update --field notes` command replaces the field entirely; agents must read the current notes first and prepend them. The `--gate` filter in `ticket.py list` depends on `[GATE N]` markers being present in the notes — overwriting them breaks gate-queue visibility.

Correct pattern:
```
# Read current notes first
python ticket.py view ISS-XXX  # copy the notes field

# Then update with full accumulated content
python ticket.py update ISS-XXX --field notes \
  --value "<existing notes> | YYYY-MM-DD <role>: [GATE N] <new entry>"
```

---

## Session File Standard
> Approved 2026-03-13. See discovery_tooling_001.md Addendum — Meetings & Sessions.

### File naming convention
`session_<role>_<name>_<NNN>_<main topic in one word>.md`

Examples: `session_architect_patrick_001_architecture.md`, `session_ux_sarah_001_ia.md`

### Required header (all session files)
```
---
ID: <filename without .md>
Type: session
Role: <role abbreviation: ba, architect, tech-lead, ux, security, frontend, backend, devops, scrum, po, qa, sdet, content, seo>
Topic: <one-line description>
Date: YYYY-MM-DD
Participants: Owner, <role agents present>
Ticket: <ticket ID or —>
Sprint: <sprint name or —>
Status: open | closed
Project: tooling | site | general
Outputs: <comma-separated: files created, decisions made, tickets raised>
---
```

### Rules
- `Status: open` = in-progress or awaiting follow-up. `Status: closed` = work complete.
- `Outputs` is the primary search field — list every artefact produced.
- All new session files must use this header from day one.
- Existing files are backfilled under ISS-011.

---

## CLI Note Authorship Convention
> Approved 2026-03-13. Resolves A1 from US-T11 / ISS-012.

All CLI tools that append notes to tickets or messages must accept a `--caller` flag:

```
--caller <name>   Optional. Identifies the note author. Default: agent
```

**Format written to file:** `YYYY-MM-DD <caller>: <text>`

**Convention:**
- AI agents pass their role name: `--caller backend`, `--caller scrum`
- Owner passes `--caller owner` or their name when running manually
- Omitted → `agent` — unambiguous that a script wrote the note

**Applies to:** `update_ticket.py` and any future CLI tool that writes notes. Enforced at coding standards level — not optional.

---

## Commit Message Format
**Conventional Commits** — `<type>(<scope>): <imperative description>`

Types: `feat`, `fix`, `test`, `refactor`, `chore`, `docs`, `style`, `perf`

Rules: imperative mood, max 72 chars on line 1, no trailing full stop,
body explains WHY not WHAT.
