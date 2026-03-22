# Development Process — SitoPresepi2
> Formalised: 2026-03-13. See constitution.md § Development Process for governing invariants.
> This document is the full step-by-step reference. The constitution section is the authoritative summary.

---

## PHASE 1 — Discovery
> Once, before any sprint. Triggered by: client/owner has an idea.

```
Client/Owner:   shares idea, goals, constraints, budget, target users
BA:             interviews client → captures requirements as user stories + acceptance criteria
PO:             reviews stories → assigns MoSCoW priority → owns the Product Backlog
Architect:      reviews backlog → produces architecture document, tech stack, data model
Tech Lead:      reviews architecture → raises concerns, approves coding standards
Security:       threat model → defines security constraints and pre-launch checklist
UX:             information architecture, user flows, wireframes
Scrum Master:   sets up team, ceremonies, tooling, definition of done
PO:             confirms backlog is groomed and ready for Sprint 0
```

**Gate before Phase 2:**
- Product Backlog exists with MoSCoW priority
- Architecture document approved by Owner
- Tech stack locked in constitution.md decisions log
- Security baseline defined
- UX IA approved

---

## PHASE 2 — Sprint 0
> Once, before the first feature sprint. No user-facing features built here.

```
Architect + Tech Lead + DevOps:   scaffold repos, Docker dev environment, branching strategy
Tech Lead:                         coding standards, PR template, pre-commit hooks, test framework
Scrum Master:                      backlog tooling, ticket schema, team communication channels
PO:                                confirms Sprint 1 backlog items meet Definition of Ready
Owner:                             approves Sprint 0 output before Sprint 1 begins
```

**Gate before Sprint 1:**
- Dev environment runs locally
- CI pipeline (if applicable) is green
- At least one health-check test passes
- Sprint 1 backlog items are `ready` status

---

## PHASE 3 — Sprint Cycle
> Repeats until product is complete. Typical length: 1–2 weeks.

```
FOR EACH sprint:

  ┌─ SPRINT PLANNING (Scrum Master facilitates) ──────────────────────────────────
  │  PO:           presents top-priority `ready` backlog items
  │  PO:           states acceptance criteria for each item
  │  Team:         estimates effort
  │  Scrum Master: confirms capacity, locks sprint backlog
  │  Tech Lead:    breaks stories into technical tasks if needed
  │  Output:       sprint backlog with assigned tickets
  └───────────────────────────────────────────────────────────────────────────────

  ┌─ DAILY STANDUP (Scrum Master facilitates, ≤15 min) ───────────────────────────
  │  Each team member:
  │    - What I completed since last standup
  │    - What I am working on today
  │    - Any blockers
  │  Scrum Master: logs blockers → assigns resolution owner → unblocks or escalates
  └───────────────────────────────────────────────────────────────────────────────

  ┌─ DEVELOPMENT (repeats per ticket) ────────────────────────────────────────────
  │  FOR EACH ticket in sprint backlog:
  │
  │    Developer:    reads ticket + acceptance criteria in full
  │    Developer:    writes failing test first  ← TDD, no exceptions
  │    Developer:    writes minimum code to make test pass
  │    Developer:    refactors → all tests still green
  │    Developer:    opens PR:
  │                    - What this does
  │                    - Tests written (function names + what they prove)
  │                    - Architecture alignment (which section)
  │                    - Anything unusual
  │
  │    Tech Lead:    reviews independently:
  │                    architecture alignment, boundary violations,
  │                    standards, security, commit format
  │    Domain dev:   reviews independently (Backend or Frontend specialist):
  │                    domain correctness, implementation quality
  │
  │    IF changes requested → Developer fixes → re-review
  │    IF both approved     → Tech Lead merges to develop
  │
  │    Ticket status: open → in-development → in-testing → po-acceptance → resolved
  └───────────────────────────────────────────────────────────────────────────────

  ┌─ TESTING (runs during and after development) ─────────────────────────────────
  │  QA:     exploratory testing on develop branch throughout sprint
  │  SDET:   E2E tests, regression suite, CI gate checks
  │  QA/SDET → raises defect tickets if issues found
  │  Developer: fixes defects → back through PR process
  └───────────────────────────────────────────────────────────────────────────────

  ┌─ SPRINT REVIEW (Scrum Master facilitates) ────────────────────────────────────
  │  Team:   demos working software to PO (and client if invited)
  │  PO:     accepts or rejects each item against acceptance criteria
  │    IF rejected → ticket returns to backlog with feedback notes
  │    IF accepted → ticket marked resolved, sprint credit given
  └───────────────────────────────────────────────────────────────────────────────

  ┌─ SPRINT RETROSPECTIVE (Scrum Master facilitates, ≤1h) ────────────────────────
  │  Team:           What went well / what didn't / what to change
  │  Scrum Master:   records action items → carried as tasks into next sprint
  └───────────────────────────────────────────────────────────────────────────────

END SPRINT → repeat from SPRINT PLANNING
```

---

## PHASE 4 — Release
> At the end of each releasable increment (not necessarily every sprint).

```
DevOps:      prepares and verifies production environment
Security:    runs pre-launch security checklist (security_checklist.md)
SDET + QA:   full regression pass on staging environment
PO:          final acceptance sign-off on all items in release
Tech Lead:   tags release on main, approves deployment command
DevOps:      deploys to production, monitors for errors
Scrum Master: notifies client, closes release sprint
Client/Owner: confirms acceptance in production
```

**Gate:**
- All checklist items green (or risk-accepted with written note)
- Zero open `critical` or `major` defects
- PO sign-off recorded

---

## PHASE 5 — Post-Launch
> Ongoing for the life of the product.

```
FOR EACH incident or change request:

  Client/User/Owner → raises issue or request
  PO:           triages → adds to backlog with MoSCoW priority and ticket type
  Scrum Master: decides: hotfix (immediate) or next sprint
    IF hotfix:  branch hotfix/* from main → fix → merge to main AND develop → tag
    IF sprint:  ticket enters next sprint planning → Sprint Cycle
```

---

## Definition of Done (applies to every ticket)

A ticket is only `resolved` when ALL of the following are true:

- [ ] Acceptance criteria met (verified by QA or PO)
- [ ] Tests written first, all passing
- [ ] No business logic in views
- [ ] No secrets in code
- [ ] No app boundary violations
- [ ] PR description complete
- [ ] Two independent reviews approved
- [ ] `lib/types.ts` updated if API shapes changed
- [ ] Conventional Commits format on all commits
- [ ] PO acceptance confirmed
