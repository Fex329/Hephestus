# SitoPresepi2 — Infrastructure Facts
> Established: 2026-03-28
> Owner-approved via proposal `backoffice/proposals/proposal-storage-url-remote-repository-github.md`
>
> This file documents non-secret infrastructure facts for discoverability.
> **Canonical source for remote URLs is always `git remote get-url origin` inside each repo.**
> This file must not override the repo config — it is a reference, not an authority.

---

## GitHub Organisation

| Field | Value |
|---|---|
| Organisation | `Ame-no-Mahitotsu` |
| Visibility | All repos are **private** |
| SSH auth | Key registered under `Fex329` GitHub account — `Fex329` is the Owner and org owner of `Ame-no-Mahitotsu`. Same person, no separate key needed. |

---

## Repositories

| Repo | Purpose | SSH Remote | HTTPS Remote |
|---|---|---|---|
| Hephestus | Governance, AI config, agent files | `git@github.com:Ame-no-Mahitotsu/Hephestus.git` | `https://github.com/Ame-no-Mahitotsu/Hephestus.git` |
| Tools | PM tooling (ticket.py, message.py, dashboard) | `git@github.com:Ame-no-Mahitotsu/Tools.git` | `https://github.com/Ame-no-Mahitotsu/Tools.git` |
| SitoPresepe | Site source code (Django + Next.js) | `git@github.com:Ame-no-Mahitotsu/SitoPresepe.git` | `https://github.com/Ame-no-Mahitotsu/SitoPresepe.git` |
| Docs | Project documentation | `git@github.com:Ame-no-Mahitotsu/Docs.git` | `https://github.com/Ame-no-Mahitotsu/Docs.git` |
| Backoffice | Operational records, ceremonies, handoffs | `git@github.com:Ame-no-Mahitotsu/Backoffice.git` | `https://github.com/Ame-no-Mahitotsu/Backoffice.git` |

> All 5 root clones use SSH remotes.

---

## Local Workspace Layout

| Path | Repo |
|---|---|
| `c:\temp\ClaudeProjects\hephestus\` | Hephestus (root, pull-only) |
| `c:\temp\ClaudeProjects\backoffice\` | Backoffice (root, pull-only) |
| `c:\temp\ClaudeProjects\docs\` | Docs (root, pull-only) |
| `c:\temp\ClaudeProjects\development\tools\` | Tools (root, pull-only) |
| `c:\temp\ClaudeProjects\development\presepi-site\` | SitoPresepe (root, pull-only) |
| `c:\temp\ClaudeProjects\Office\{Agent}-Desk\` | Agent desk clones — development work only |

---

## Hosting (planned)

| Field | Value |
|---|---|
| Provider | **Hetzner Cloud CPX21** — Nuremberg (NBG1) or Falkenstein (FSN1), Germany |
| Plan | CPX21: 3 AMD vCPU / 4 GB RAM / 80 GB NVMe SSD / 20 TB traffic |
| Estimated cost | ~€11.30/mo (instance €8.99 + IPv4 ~€0.50 + automated backups ~€1.80) |
| Traffic overage | €1.00/TB beyond 20 TB (billed per 100 MB block) |
| Rationale | Best price-performance ratio. German GmbH = cleanest GDPR posture. ISO 27001. Supersedes DigitalOcean Frankfurt (2026-03-12). See docs/site/vps_provider_comparison.md. |
| Status | **Not yet provisioned** — deferred until a working website is ready for deployment |
| Domain | **Purchased** as of 2026-03-30 — URL not yet recorded here (owner discretion) |
| Environment URLs | TBD — not yet provisioned |

### Provisioning Checklist (when ready)
- Location: Nuremberg (NBG1) or Falkenstein (FSN1)
- Image: Ubuntu 24.04 LTS
- SSH key uploaded at creation; password auth disabled immediately
- Add-ons: Primary IPv4 + Automated Backups
- Cloud Firewall: allow 22, 80, 443 inbound only (free)
- Install Docker Engine via official apt repo (not one-click app)
