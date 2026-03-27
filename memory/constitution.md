# SitoPresepi2 — Project Constitution
> Last amended: 2026-03-13

## Purpose
This is the second attempt at building a website for **handcrafted nativity houses (presepi)**.
Products are:
- Handmade / artisanal
- Available in standard measures
- Available made-to-order (su misura)

## Project Context

**This is a technically-owned and technically-operated project.**

The project owner is a technical professional with corporate infrastructure, identity management, and development experience. They are the sole operator, developer, and maintainer of this site — from day one through the end of the project's life. There is no planned handover to a non-technical party. The owner uses AI assistance (Claude) for development and maintenance — AI agents are first-class participants in the build process, operating under the governance structure defined in this document.

The artisan (Gabriele) is the business owner and the subject of the site's content. He has limited, content-scoped access to the admin panel (`artisan` Django Group) — image uploads, text edits, and read-only order/sales visibility. He is not a system operator: he cannot manage products, users, languages, or site configuration. The Owner retains full operational control.

**Implications for all decisions:**
- The admin interface is designed for a technical operator, not a consumer-grade user
- AI agents use precise technical language — no over-simplification
- Architecture constraint C3 in the architecture document is revised accordingly (see Decisions Log 2026-03-13)
- Any team member who based a prior decision on a non-technical operator assumption must review and revise it

## Governing Principles

### 1. Human Control
Every architectural decision, technology choice, and design direction must be explicitly approved by the project owner before implementation. No assumption is made without confirmation.

### 2. Transparency
All choices must be documented with the reasoning behind them. Nothing is done silently.

### 3. Simplicity First
Prefer simple, maintainable solutions over clever or complex ones. Build only what is needed.

### 4. Iteration
Work in small, reviewable increments. Each step must be visible and reversible.

### 5. No Surprises
AI agents must not take initiative beyond the scope explicitly defined in each session.

### 6. Test-Driven Development
All code is written test-first. No feature is considered done until its tests are written and passing. There are no exceptions and no deferral of tests to later. The minimum test that proves the behaviour is sufficient — exhaustive edge-case coverage is not required upfront.

### 7. No Monolith — Parts Must Be Replaceable
The architecture must maintain clean boundaries between layers. No layer may be so tightly coupled to another that replacing it requires rebuilding the whole system. This principle governs all future technical decisions.

### 8. No Credentials in Code
Secrets, passwords, API keys, and tokens must never appear in the codebase. Environment variables only, via a `.env` file on the server that is never committed to the repository.

### 9. GDPR Compliance Before Launch
The site handles personal data of EU residents (Italy, UK, Germany). A privacy policy, data retention policy, and cookie consent mechanism are required before any production deployment. No exceptions.

## Development Process

All work follows this Agile cycle from idea to delivery. No phase may be skipped. See `memory/development_process.md` for the full step-by-step reference including individual responsibilities per phase.

### Phases (in order)

| Phase | When | Purpose |
|---|---|---|
| **Discovery** | Once, before any sprint | Capture requirements, architecture, UX, security baseline |
| **Sprint 0** | Once, before first feature sprint | Scaffold repo, dev environment, tooling, standards |
| **Sprint Cycle** | Repeating until done | Plan → Build → Test → Review → Retrospective |
| **Release** | End of each releasable increment | Security check, regression, PO sign-off, deploy |
| **Post-Launch** | Ongoing | Triage issues → backlog → sprint cycle |

### Process Invariants — No Exceptions

These rules apply to every ticket, every sprint, every agent:

1. **No ticket = no work.** Nothing is built without a ticket in the backlog.
2. **TDD always.** Write a failing test before writing any code. (See also: Governing Principle §6)
3. **Two reviews before merge.** Tech Lead + domain specialist, independently. (See also: `ai-standards.md` Code Review Process)
4. **PO acceptance before close.** No ticket moves to `resolved` without PO sign-off against acceptance criteria.
5. **Security and GDPR constraints apply from day one.** Not pre-launch additions.
6. **No sprint starts without planning.** No code is written outside a named sprint except Sprint 0 scaffolding.

---

## Project Documents

All agents must read relevant documents before acting. The constitution is always required.

| File | Purpose |
|------|---------|
| `memory/constitution.md` | Governing principles, amendment process, decisions log |
| `memory/expedition_dispatches.md` | Chronological project journal. Entries use `EXP-NNN` IDs and labels |
| `development/tools/pm.db` | Unified issue tracker (SQLite). Query via `python ticket.py list/view/search/sprint` from `development/tools/`. |
| `memory/glossary.md` | Controlled vocabulary. All terms used across documents are defined here |
| `memory/development_process.md` | Full step-by-step Agile process: phases, individual responsibilities, Definition of Done |

## Core Epistemic Rules

These rules apply to all participants — Project Owner, AI Orchestrator, and all role agents — without exception.

### 1. Honesty Over Comfort
Never say what someone wants to hear at the expense of what is true. If a plan has a flaw, name it. If an assumption is weak, say so. Flattery and false agreement are failures of duty.

### 2. Ignorance Over Fabrication
Not knowing something is acceptable. Inventing an answer to avoid admitting ignorance is not. When in doubt, say "I don't know" and propose how to find out.

### 3. No Pride in Position
Being wrong is not a defeat — it is information. No participant should defend a position because they held it, only because the evidence still supports it. When evidence changes, position must change.

### 4. Evidence Is Neutral
Arguments are evaluated on their merits, not on who made them. A correct observation from a junior agent outweighs a flawed one from a senior role. Seniority confers no epistemic privilege.

### 5. Dissent Is a Contribution
Raising a concern, a contradiction, or an uncomfortable truth is a service to the project — not an act of opposition. It must be welcomed and addressed, never dismissed or penalized.

## Amendment Process

### Who May Propose Amendments
Any participant may propose an amendment to this constitution:
- The **Project Owner** (human)
- The **AI Orchestrator** (Claude)
- Any **role agent** operating within the system

Amendments proposed by role agents must be escalated to the Project Owner and AI Orchestrator before any discussion begins. Agents cannot directly amend the constitution.

### Mandatory Discussion
Every proposed amendment — regardless of origin — must go through a **structured discussion** between the Project Owner and the AI Orchestrator before any change is made.

The discussion is only valid if:
- Both parties have stated their position and reasoning
- The AI Orchestrator has explicitly confirmed it has no further concerns, **or** has registered a formal concern (see below)

### Registered Concerns
The AI Orchestrator may raise a **Registered Concern** against any proposed amendment. A Registered Concern:
- Must include a clear reason
- Must be acknowledged by the Project Owner before a decision is made
- **Does not block the amendment** — the Project Owner may proceed regardless

The Project Owner's decision is **always final**.

### What Requires the Amendment Process
The amendment process applies to changes to:
- Governing Principles
- Core Epistemic Rules
- The Amendment Process itself

### What Does NOT Require the Amendment Process
Updates to the **Decisions Log** are routine record-keeping and do not require the amendment process. The AI Orchestrator may update the Decisions Log after any owner-approved decision.

### Recording Amendments
Every amendment must be logged in the **Decisions Log** with:
- The date
- The change made
- The reason
- Whether a Registered Concern was raised and, if so, its outcome

## Decisions Log
> Record all major decisions here as they are made.

| Date | Decision | Reason | Approved by |
|------|----------|---------|-------------|
| 2026-03-11 | Project restarted as SitoPresepi2 | First attempt lessons applied | Owner |
| 2026-03-11 | Added Core Epistemic Rules | Establish honesty, humility, and evidence-based conduct for all participants | Owner + Orchestrator |
| 2026-03-11 | Added Amendment Process | Formalize how the constitution can be changed, including Registered Concerns mechanism | Owner + Orchestrator |
| 2026-03-11 | Added Project Documents section | Make document registry part of the constitution so all agents know what exists | Owner + Orchestrator |
| 2026-03-12 | Stack selected: Django + Wagtail, self-hosted | Open source, integrated stack, localisation built-in, clean admin, extensible, low cost | Owner + Patrick + Alessandro |
| 2026-03-12 | Stack revised: Django REST Framework + Next.js + custom admin | Replaceability of parts required; no monolith; owner is developer using AI assistance | Owner + full team |
| 2026-03-12 | Hosting: DigitalOcean Frankfurt | Best security tooling, EU jurisdiction, GDPR compliant, cloud firewall | Owner + Alessandro + Dominick |
| 2026-03-12 | Testing: TDD from day one | Quality and pivotability over speed; pytest+pytest-django for Django, Jest+RTL for Next.js | Owner + Alessandro |
| 2026-03-12 | Deployment: manual for v1, Docker Compose | Simpler and safer for one-person project; CI/CD added when test suite matures | Owner + Alessandro |
| 2026-03-12 | Image storage: local filesystem + django-storages abstraction | Scale doesn't justify object storage at launch; easy to migrate later | Owner + Patrick |
| 2026-03-12 | Next.js rendering: SSG with revalidation | Better performance and SEO for catalogue site | Owner + Patrick |
| 2026-03-12 | Added Governing Principles 6–9: TDD, No Monolith, No Credentials in Code, GDPR | Enshrine key technical and legal principles decided in full team sessions | Owner + Orchestrator |
| 2026-03-12 | Clarified amendment process scope | Decisions Log updates are routine; amendment process applies to principles only | Owner + Orchestrator |
| 2026-03-13 | Repository: local bare repo | `SitoPresepi2/remote.git` as origin, `SitoPresepi2-src/` as working clone; migrate to GitHub when CI/CD added | Owner + Alessandro |
| 2026-03-13 | Frontend language: TypeScript strict mode | Type safety across API contract; AI produces fewer bugs in TypeScript; better IDE support | Owner + Alessandro |
| 2026-03-13 | Branching: lightweight GitFlow | `main` / `develop` / `feature/*` / `hotfix/*`; no direct commits to main or develop | Owner + Alessandro |
| 2026-03-13 | Python toolchain: Black + isort + Ruff + pre-commit | Zero-config formatting, fast linting, enforced before every commit | Owner + Alessandro |
| 2026-03-13 | Commit format: Conventional Commits | Structured history, machine-readable, clear scope | Owner + Alessandro |
| 2026-03-13 | Code review: two-reviewer model | Tech Lead reviews all PRs; domain specialist (Backend/Frontend agent) reviews in parallel, independently | Owner + Alessandro |
| 2026-03-13 | `customers/` as dedicated Django app from day one | Customer identity must stay isolated from commerce and admin; adding auth in v2 must not dirty the commerce app boundary | Owner + ARQ-001 |
| 2026-03-13 | `Payment` stub added to data model | Architecture constraint C7 required a payment seam; stub was missing from data model — added: id, order FK, provider (nullable), status, amount, created_at | Owner + ARQ-001 |
| 2026-03-13 | `AuditLog` model added to admin_app; append-only enforced at model and DB level | Dominick's security baseline required it from day one; missing from data model — added | Owner + ARQ-001 |
| 2026-03-13 | Application logging: Python logging module, modular namespaces, level from LOG_LEVEL env var | Separate from security audit log; subsystem loggers (db, catalogue, commerce, admin, api); configurable from ERROR to DEBUG | Owner + ARQ-001 |
| 2026-03-13 | Page.body = structured JSON blocks with typed block schema | Markdown rejected: gives freeform freedom, poor fit for defined-blocks constraint, no clean migration path; JSON enforces block types, extensible, owner is comfortable with JSON | Owner + ARQ-001 |
| 2026-03-13 | CSS: Tailwind CSS with tailwind.config.ts as design token source | Productivity for one-developer project; custom theme enforces design consistency; App Router compatible | Owner + ARQ-001 |
| 2026-03-13 | Architecture constraint C3 revised: owner is sole technical operator for lifetime of project | C3 ("Admin operable by non-technical user") was a false assumption — artisan is content subject, not operator; all downstream decisions to be reviewed (see meeting_arq_001.md) | Owner + Orchestrator |
| 2026-03-13 | Admin role model: two roles — Django superuser (Owner) + `artisan` Django Group (Gabriele) | No third role needed; Django built-in RBAC sufficient; artisan group has content and read-only order/sales access only | Owner + BAR-001 |
| 2026-03-13 | Gabriele access scope: upload images, edit text, view orders/sales — no product/user/config management | Artisan is content subject contributing media; operational control stays with Owner | Owner + BAR-001 |
| 2026-03-13 | TOTP required for all admin users including Gabriele | Consistent auth policy; any admin login is a privileged action regardless of group permissions | Owner + BAR-001 (Dominick) |
| 2026-03-13 | Admin session timeouts: Owner = 1h, Gabriele = 2h; both env-configurable | Inactivity-based (resets on each request); 1h appropriate for superuser; shorter than prior 8h default | Owner + BAR-001 (Dominick) |
| 2026-03-13 | Ticket schema extended: Types added (`story`, `task`); Statuses expanded to full dev lifecycle (`in-refinement`, `ready`, `in-development`, `in-testing`, `po-acceptance`, `deferred`); fields added (`Updated`, `Sprint`, `Closed`); notes mandatory timestamped format | Rob (Scrum Master) proposal; schema must serve the sprint workflow | Owner + Rob |
| 2026-03-13 | Ticket schema: `Component` field added (13 values mapping to Django app structure + cross-cutting areas); `Severity` field added (`critical`/`major`/`minor`/`trivial` — technical impact, separate from `Urgency`/business priority); Severity nullable for non-defect tickets | Type and Component are orthogonal; defect triage requires both dimensions independently | Owner + Rob |
| 2026-03-13 | Meeting metadata schema formalised: 9-field header (ID, Type, Tags, Date, Chair, Participants, Ticket, Sprint, Status); 5 controlled meeting types; ID prefix convention (ARQ/BAR/SEC/WRK/SCR); file naming `meeting_<prefix>_<NNN>.md` | Grep-driven indexing; cross-reference with tickets via Ticket field | Owner + Rob |
| 2026-03-13 | AI-assisted development recorded in constitution Project Context; AI agents are first-class participants operating under project governance | Closes gap: constitution governed AI behaviour but never stated AI's role in the build process | Owner + Orchestrator |
| 2026-03-13 | Architecture §13 constraint added: AI-generated code requires a failing test before acceptance; TDD applies regardless of code origin | Constitution §6 already covers this universally; §13 makes it explicit for AI context | Owner + Patrick |
| 2026-03-13 | Dev environment: fully containerised via `docker-compose.override.yml`; Nginx excluded in dev (profile-gated); all dev ports bound to `127.0.0.1`; Django `runserver` + Next.js `next dev` with source volume mounts; PostgreSQL unchanged | Production parity; AI agents run pytest in same container image; laptop LAN exposure eliminated (WRK-001) | Owner + Patrick + Alessandro |
| 2026-03-13 | Dedicated `test` Docker service (profile-gated, separate test DB); `docker compose --profile test run --rm test` is the canonical test invocation | Prevents test container competing with dev container for the same database during continuous TDD | Owner + Alessandro |
| 2026-03-13 | `conftest.py` at two levels: `django/conftest.py` (project-level) and `app_name/tests/conftest.py` (app-specific fixtures only) | Canonical fixture anchor prevents per-agent divergence; scaffolded by Alessandro on day one | Owner + Alessandro |
| 2026-03-13 | `SESSION_COOKIE_SECURE=False` and `CSRF_COOKIE_SECURE=False` required in dev `.env`; production always `True` | Dev runs HTTP only; `Secure=True` silently drops cookies over HTTP — admin login fails without this | Owner + Patrick + Alessandro |
| 2026-03-13 | `docker-compose.override.yml` lives in `SitoPresepi2-src/` (gitignored); `SitoPresepi2/` never holds runtime artefacts | Runtime files belong with code, not project management | Owner + Patrick |
| 2026-03-13 | v1 has no cart or checkout (PB-06 deferred); B2C order intent expressed via Contact form pre-filled with product name; Gabriele follows up personally | Artisan product — personal conversation is the sale; reduces GDPR surface area; matches actual order reality | Owner + Lota + Orchestrator |
| 2026-03-13 | Navigation labels: IT "Il Lavoro" / EN "The Making" / DE "Das Handwerk" for time-lapse/process page | "The Craft" overused; "The Making" specific and fresh; "Das Handwerk" dignified German trade term; DE label to receive native speaker spot-check before launch (non-blocking) | Owner + Lota + Orchestrator |
| 2026-03-13 | "Chi sono / The Maker / Der Meister" page added to v1 page list (P-06b) | Artisan identity page essential for trust and brand; home page cannot carry both routing and personal story without conflict | Owner + Lota + Orchestrator |
| 2026-03-13 | URL slugs are locale-specific (e.g. `/en/catalogue/`, `/de/katalog/`); product slugs language-neutral in v1 | SEO benefit for EN/DE outweighs routing complexity at this scale; product slug localisation deferred post-launch | Owner + Lota |
| 2026-03-13 | Development Process formalised in constitution and `memory/development_process.md` | Team built tooling without TDD, without completed security review, and outside a named sprint — process must be explicit and governing, not implicit | Owner + Orchestrator |
| 2026-03-13 | Sprint naming convention: `tools-N` for tooling track, `site-N` for website track | Two active project tracks require distinguishable sprint identifiers in the backlog `Sprint` field | Owner + Rob |
| 2026-03-13 | Workspace subfolder structure: `memory/workspace/tooling/`, `memory/workspace/site/`, `memory/workspace/shared/`; shared files stay at `memory/` root | Flat workspace degrades as both tracks generate files; subfolders keep each track self-contained; folder is organisational aid only — `project` field in file header is authoritative | Owner + Patrick + Alessandro |
| 2026-03-13 | Repository layout: `repos/` folder at `c:\temp\ClaudeProjects\` level; bare repos live there (e.g. `repos/SitoPresepi2.git`); working clones remain as siblings (`SitoPresepi2-src/`) | Single known location for all bare repos regardless of how many projects are onboarded; move tracked as ISS-015 | Owner + Patrick |
| 2026-03-13 | `TicketProject` enum (`tooling`, `site`, `general`) as canonical multi-project extension point; mandatory on all record types (tickets, messages, meetings, sessions); `general` for cross-project records; extensible by adding enum values | One backlog, one dashboard, multiple project tracks; filtering and search require structured metadata not sprint name inference | Owner + Rob + Patrick + Alessandro |
| 2026-03-14 | `pytest-cov` approved as dev-only test dependency; three security conditions: (1) pin exact version in pyproject.toml, (2) declared under dev extras only — never in production image, (3) `.coverage` and `htmlcov/` added to `.gitignore` | Coverage gate enforcement; conditions per Dominick security review | Owner + Dominick + Lauren |
| 2026-03-14 | Sprint tools-1 team structure: Claire (implementer, all tickets), Alessandro (G5 Tech Lead review), Chris (G6 Fullstack domain review) | No self-review — three distinct roles enforced per Agent Execution Protocol Option A | Owner + Rob |
| 2026-03-14 | Python version: 3.14.3 for all project Python (tooling track and site track) — replaces Python 3.12 entry (2026-03-12) | Python 3.14.3 stable since 2026-02-03; supported by Django 5.2.x and DRF 3.x latest; single version eliminates host/container split; longer support window | Owner + Alessandro + Lauren |
| 2026-03-14 | Django pinned to >=5.2.8 — narrows prior "Django 5.x" approval | Python 3.14 support in Django requires minimum 5.2.8; earlier 5.x releases (5.0, 5.1) do not support Python 3.14 | Owner + Alessandro |
| 2026-03-15 | ISS-027 D3 overridden: protocols.py approved for pm/ package; storage format abstraction is a design invariant | Athena review (WRK-002) established that pm/ must be format-agnostic — md/json swappable without touching business logic. YAGNI deferral at D3 time was correct given information then; invariant supersedes it. New structure: protocols.py (Protocols only) + md_reader.py + md_writer.py. Re-exported via __init__.py. | Owner + Alessandro + Patrick (Athena review) |
| 2026-03-15 | Message log: central message_log.md at memory/ root as queryable index + session files in memory/workspace/ | Session-file-only approach gives no queryable index; dashboard messages tab needs a structured source. message_log.md mirrors issue_backlog.md pattern — summary row per session, file_path column points to full content. ISS-020 implements. | Owner + Alessandro |
| 2026-03-16 | CLI Model 2: ticket.py (create + update subcommands) + message.py (create subcommand) replace separate create_ticket.py, update_ticket.py, create_message.py | Unified subcommand CLI is more ergonomic and standard pattern; pm/ package design unchanged. ISS-019 delivers ticket.py create; ISS-012 adds ticket.py update; ISS-020 delivers message.py create. | Owner + Alessandro |
| 2026-03-16 | update_ticket_field() uses pure field replacement (Option C); CLI owns note formatting | BacklogWriter Protocol stays format-agnostic. Notes appending logic (timestamp + caller + pipe separator) lives in ticket.py update. Immutable field rejection enforced inside update_ticket_field(). | Owner + Alessandro |
| 2026-03-16 | German register: „du" (informal) sitewide — applies to all DE copy, UI text, CTAs, and future content | Artisan brand is warm and personal; „du" matches the tone and the artisan context; „Sie" would create distance inconsistent with the brand voice. Decision is sitewide and binding: no page may use „Sie". | Owner + Helios |
| 2026-03-16 | Storage backend for ticket and message records changed from markdown tables to SQLite | Markdown table format is structurally fragile: pipe escaping (ISS-033, ISS-034), silent row loss, column alignment drift (ISS-030), no crash safety on write. SQLite eliminates all failure modes with zero new dependencies (sqlite3 stdlib). BacklogReader/BacklogWriter/MessageWriter protocol abstraction makes the swap clean. Specifics: DB at memory/pm.db; two tables (tickets, messages); create_session_file() stays .md (session files are narrative documents not records); md_reader/md_writer/md_message_writer kept as archive implementations; issue_reader.py deleted (reads stale markdown, replaced by ticket.py list in future). | Owner + Alessandro |
| 2026-03-22 | Workspace reorganised from `SitoPresepi2/` monorepo into 5 dedicated repositories | Separation of concerns: AI config/governance (hephestus), PM tooling (development/tools), documentation (docs), operational records (backoffice), site code (development/presepi-site). Monorepo archived at `SitoPresepi2-archive/`. VS Code multi-root workspace wires all repos into one editor window. Sprint reorg-1, ISS-129–ISS-141. See EXP-007. | Owner + Alessandro |
| 2026-03-23 | All 5 repos migrated to GitHub as `origin`; local bare repos in `repos\` are archived and no longer used | Completed as part of reorg-1. Remotes: Fex329/Hephestus, Fex329/Tools, Fex329/SitoPresepe, Fex329/Docs, Fex329/Backoffice. Prior constitution entry (2026-03-13) referring to local bare repo as origin is superseded. `ai-standards.md` repository structure updated accordingly. | Owner + Alessandro |
| 2026-03-24 | German register override: „Sie" (formal) permitted for DE CTAs — supersedes 2026-03-16 „du"-only decision | Owner reviewed „Kontaktieren Sie mich zu dieser Krippe" for ISS-112 catalogue CTA and explicitly chose „Sie"-form. German customers expect formal address in a commercial context; „du" sitewide was too broad a constraint. Revised rule: DE CTAs may use „Sie"; narrative/editorial copy (Chi sono, introductory text) retains „du"/informal register. | Owner |
| 2026-03-24 | Product.slug is language-neutral and lives on `Product`, not `ProductTranslation` | Architecture doc incorrectly placed slug on ProductTranslation (unique per language), contradicting the 2026-03-13 decision that product slugs are language-neutral in v1. Corrected: slug moved to Product with `unique=True`. API lookup `GET /api/products/{slug}/` queries Product.slug directly — no language ambiguity. ISS-158 delivers the migration; ISS-104 blocked until complete. Architecture doc updated. | Owner + Patrick |
