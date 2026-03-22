---
description: "Use when writing code, reviewing PRs, creating tickets, running tests, or making any technical decision in SitoPresepi2. Defines coding standards, agent protocol, and tooling conventions."
applyTo: "**"
---

# SitoPresepi2 — AI Standards (Binding on All Agents)

## Owner Communication Standard

All agents **must**:
- Use precise technical language — no consumer-grade simplifications
- Assume familiarity with Django, REST APIs, Docker, Nginx, PostgreSQL, Git
- Skip explanations of standard concepts unless asked
- Flag trade-offs at a technical level, not a beginner level

## Technology Stack (Locked)

| Layer | Technology |
|---|---|
| API | Django 5.2.x + DRF 3.x (Python 3.14.3) |
| Frontend | Next.js 14.x App Router (TypeScript strict) |
| Database | PostgreSQL 16.x |
| Reverse proxy | Nginx (latest stable) |
| Containers | Docker + Docker Compose |
| Hosting | DigitalOcean Frankfurt, 2-core/4GB |
| Admin auth | Django session + django-otp (TOTP) |
| Testing (Django) | pytest + pytest-django |
| Testing (Next.js) | Jest + React Testing Library |

## Agent Execution Protocol (All Implementation Tickets)

| Gate | Agent | Owner action |
|------|-------|-------------|
| G1 | Analysis + plan | Approve plan — **blocker** |
| G2 | Failing test (red output) | Confirm red — **blocker** |
| G3 | Tests green + coverage | Confirm green — **blocker** |
| G4 (if needed) | Root cause + fix strategy | Approve strategy — **blocker** |
| G5 | Alessandro: standards/arch/security review | Owner acknowledges — **blocker** |
| G6 | Chris (fullstack) or Ash (frontend): domain review | Owner acknowledges — **blocker** |
| G7 Step 1 | Sofia (PO): acceptance against each criterion | — |
| G7 Step 2 | **Owner: final sign-off → `resolved`** | **Owner authority is final** |

Status transitions: `ready` → `in-development` → `in-testing` → `po-acceptance` → `resolved`

Each gate transition recorded in ticket notes: `YYYY-MM-DD <role>: [GATE N] <summary>`

## Coding Standards — Python / Django

- **Formatter:** Black | **Imports:** isort | **Linter:** Ruff | **Pre-commit:** all three
- **Naming:** `snake_case` files/functions/variables, `PascalCase` classes, `SCREAMING_SNAKE_CASE` constants, `_leading_underscore` private, `kebab-case` URL patterns
- **App structure:** `models.py` (models only), `services.py` (all business logic), `serializers.py`, `views.py` (thin, calls services only), `urls.py`, `tests/`
- **Rule: no business logic in views. Enforced in every code review.**

## Coding Standards — TypeScript / Next.js

- TypeScript strict mode (`"strict": true`)
- **Formatter:** Prettier | **Linter:** ESLint (Next.js recommended)
- `PascalCase.tsx` components, `camelCase.ts` hooks (with `use` prefix), `PascalCase` types
- `lib/types.ts` is the API contract file — must mirror Django serializer output exactly

## Testing Standards

- Test naming: `test_<subject>_<condition>_<expected_result>`
- Structure: Arrange / Act / Assert (AAA)
- Minimum test: smallest test that proves the behaviour. No exhaustive edge cases upfront.
- No "I'll add tests later" — ever.
- Django: `@pytest.mark.django_db`, fixtures in `conftest.py`
- Next.js: co-located test files (`Component.test.tsx`)

## tools/pm Package

- `tools/pm/` is pure Python — no Django, no database, no HTTP
- `protocols.py` (BacklogReader + BacklogWriter + MessageWriter), `md_reader.py`, `md_writer.py`, `md_message_writer.py`, `ticket_schema.py`
- CLI scripts at project root call `pm` functions — never the reverse
- Every public function must have at least one test in `tools/tests/`

## Repository and Branching

```
main        Production only. Tagged on every deployment.
develop     Integration branch. Tests must always be green here.
feature/*   Branched from develop. Merged to develop via PR.
hotfix/*    Branched from main. Merged to main AND develop.
```

- Commit format: Conventional Commits
- Branch naming: `feature/short-kebab-description`, `fix/`, `test/`, `chore/`, `hotfix/`

## Code Review (Two-Reviewer Model)

| Reviewer | Gate | Scope |
|---|---|---|
| Alessandro (Tech Lead) | G5 | Architecture alignment, boundary violations, standards, security |
| Chris (fullstack domain) | G6 | Django + Next.js PRs |
| Ash (frontend domain) | G6 | Next.js-only PRs |

Reviews are **independent** — G6 reviewer reads code fresh, without seeing G5 comments first.
