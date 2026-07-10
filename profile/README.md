<div align="center">

<img src="https://raw.githubusercontent.com/Kntro-Soft/.github/main/profile/banner.png" alt="KntroSoft" width="100%" />

**A small software studio building ReqsAI — and what's next.**

[![Live App](https://img.shields.io/badge/ReqsAI-app.tamci.app-22c55e?style=flat-square&labelColor=0a0a0a)](https://app.tamci.app)
[![API](https://img.shields.io/badge/API-api.tamci.app-16a34a?style=flat-square&labelColor=0a0a0a)](https://api.tamci.app)
[![Status](https://img.shields.io/badge/status-active%20development-4ade80?style=flat-square&labelColor=0a0a0a)]()

</div>

---

## Who we are

**KntroSoft** is the organization behind [**ReqsAI**](https://app.tamci.app), our flagship
product — a B2B SaaS platform that turns live discovery meetings into structured, backlog-ready
requirements using AI. This org hosts ReqsAI's full stack (backend, frontend, infrastructure,
marketing site) plus the academic work behind it.

## ReqsAI, in one paragraph

An analyst runs a live discovery session (voice or text), and an LLM listens in real time —
surfacing draft user stories, edge cases, clarifying questions, and duplicate/update detection
against the existing backlog as the conversation unfolds. Decisions are reviewed and accepted on
the spot; the backlog stays structured, deduplicated, and exportable (including a direct push to
Jira), with subscription billing via Stripe.

## Repositories

| Repo | What it is | Stack |
|---|---|---|
| [**reqsai-api**](https://github.com/Kntro-Soft/reqsai-api) | Backend — modular monolith (`iam`, `billing`, `workspace`, `discovery`, `gateway`) | Spring Boot 4 · Spring Modulith · PostgreSQL/pgvector |
| [**reqsai-web**](https://github.com/Kntro-Soft/reqsai-web) | Frontend — the SaaS app itself | Angular 22 (zoneless) · TypeScript · Tailwind CSS v4 |
| [**reqsai-infra**](https://github.com/Kntro-Soft/reqsai-infra) | AWS production infrastructure as code | Terraform · ECS Fargate · RDS · CloudFront |
| [**reqsai-landing**](https://github.com/Kntro-Soft/reqsai-landing) | Public marketing site | React · Vite · Tailwind CSS |
| [**reqsai-report**](https://github.com/Kntro-Soft/reqsai-report) | Academic report (UPC Software Engineering) | DDD · C4 model · Lean UX |

## Architecture at a glance

- **Multi-tenant** — schema-per-tenant isolation in a single Postgres instance.
- **Modular monolith** — Spring Modulith-enforced bounded contexts (`iam`, `billing`, `workspace`,
  `discovery`, `gateway`), each with an explicit public API surface; no reaching into another
  module's internals.
- **Real-time** — STOMP over WebSocket for live transcript streaming, AI suggestions, and session
  presence.
- **Vector search** — pgvector-backed similarity search for duplicate/related-story detection.
- **Integrations** — Jira Cloud (OAuth 2.0 + API token) for backlog push/import; Stripe for
  subscription billing.
- **Production on AWS** — ECS Fargate, RDS Postgres, CloudFront + S3, all provisioned via
  Terraform with GitHub Actions OIDC (no long-lived AWS keys).

## Workflow

We follow **Gitflow** across every repo: `feature/*` · `bugfix/*` · `hotfix/*` branches into
`develop`, releases cut from `develop` into `main`. See each repo's `.github/CONTRIBUTING.md` for
the full convention.

---

<div align="center">

KntroSoft · building ReqsAI · UPC Software Engineering · 2026

</div>
