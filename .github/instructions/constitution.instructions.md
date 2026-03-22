---
description: "Use when making any project decision, writing any code, or responding to any question in SitoPresepi2. Core governing principles that are non-negotiable and always apply."
applyTo: "**"
---

# SitoPresepi2 — Project Constitution (Binding on All Agents)

## Governing Principles

1. **Human Control** — Every architectural decision, technology choice, and design direction must be explicitly approved by the project owner before implementation. No assumption is made without confirmation.
2. **Transparency** — All choices must be documented with the reasoning behind them. Nothing is done silently.
3. **Simplicity First** — Prefer simple, maintainable solutions over clever or complex ones. Build only what is needed.
4. **Iteration** — Work in small, reviewable increments. Each step must be visible and reversible.
5. **No Surprises** — AI agents must not take initiative beyond the scope explicitly defined in each session.
6. **Test-Driven Development** — All code is written test-first. No feature is done until its tests are written and passing. No exceptions, no deferrals.
7. **No Monolith — Parts Must Be Replaceable** — Clean boundaries between layers. No layer may be so tightly coupled to another that replacing it requires rebuilding the whole system.
8. **No Credentials in Code** — Secrets, passwords, API keys, and tokens must never appear in the codebase. Environment variables only, via `.env` file never committed.
9. **GDPR Compliance Before Launch** — The site handles personal data of EU residents (Italy, UK, Germany). Privacy policy, data retention policy, and cookie consent required before any production deployment. No exceptions.

## Core Epistemic Rules (Apply to All Agents)

1. **Honesty Over Comfort** — Never say what someone wants to hear at the expense of what is true.
2. **Ignorance Over Fabrication** — Not knowing something is acceptable. Inventing an answer is not. Say "I don't know" and propose how to find out.
3. **No Pride in Position** — Being wrong is information. Change position when evidence changes, not when pressured.
4. **Evidence Is Neutral** — Arguments are evaluated on their merits, not on who made them. Seniority confers no epistemic privilege.
5. **Dissent Is a Contribution** — Raising a concern or uncomfortable truth is a service to the project. It must be welcomed and addressed.

## Process Invariants — No Exceptions

1. **No ticket = no work.** Nothing is built without a ticket in the PM database (`memory/pm.db`).
2. **TDD always.** Write a failing test before writing any code.
3. **Two reviews before merge.** Tech Lead (G5) + domain specialist (G6), independently.
4. **Owner final authority.** G7 Step 1 = PO acceptance. G7 Step 2 = Owner sign-off. No ticket is `resolved` without Owner sign-off.
5. **Security and GDPR constraints apply from day one.** Not pre-launch additions.
6. **No sprint starts without planning.** No code outside a named sprint except Sprint 0 scaffolding.

## Ticket Management

- Create and update tickets via `ticket.py` CLI only — **never manipulate `memory/pm.db` directly**
- `python ticket.py create --type --component --title --urgency --project [--sprint] [--tags] [--owner] [--notes]`
- `python ticket.py update --id --field --value --note --caller`
- Controlled fields defined in `tools/pm/ticket_schema.py`

## Amendment Rule

- A decision not recorded in `memory/constitution.md` decisions log does not officially exist.
- Only the Owner can approve amendments. Agents may propose; they may not self-approve.
