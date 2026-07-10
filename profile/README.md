<div align="center">

<img src="https://raw.githubusercontent.com/Kntro-Soft/.github/main/profile/banner.png" alt="KntroSoft" width="100%" />

**KntroSoft is a software studio building [ReqsAI](https://app.tamci.app) — an AI copilot that
turns live discovery meetings into structured, backlog-ready requirements.**

[![Live App](https://img.shields.io/badge/ReqsAI-app.tamci.app-22c55e?style=flat-square&labelColor=0a0a0a)](https://app.tamci.app)
[![API](https://img.shields.io/badge/API-api.tamci.app-16a34a?style=flat-square&labelColor=0a0a0a)](https://api.tamci.app)
[![Status](https://img.shields.io/badge/status-active%20development-4ade80?style=flat-square&labelColor=0a0a0a)]()

</div>

---

## Who we are

**KntroSoft** is the organization behind [**ReqsAI**](https://app.tamci.app), our flagship
product. This org hosts ReqsAI's full stack (backend, frontend, infrastructure, marketing site)
plus the academic work behind it. We're a small team — this is where the code, decisions, and
architecture records live.

## What we build: ReqsAI

An analyst runs a live discovery session (voice or text), and an LLM listens in real time —
surfacing draft user stories, edge cases, clarifying questions, and duplicate/update detection
against the existing backlog as the conversation unfolds. Decisions are reviewed and accepted on
the spot; the backlog stays structured, deduplicated, and exportable (including a direct push to
Jira), with subscription billing via Stripe.

**Try it:** [app.tamci.app](https://app.tamci.app)

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

## Tech stack

![Java](https://img.shields.io/badge/Java-25-f89820?style=flat-square&logo=openjdk&logoColor=white&labelColor=0a0a0a)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-4-6db33f?style=flat-square&logo=springboot&logoColor=white&labelColor=0a0a0a)
![Angular](https://img.shields.io/badge/Angular-22-dd0031?style=flat-square&logo=angular&logoColor=white&labelColor=0a0a0a)
![TypeScript](https://img.shields.io/badge/TypeScript-3178c6?style=flat-square&logo=typescript&logoColor=white&labelColor=0a0a0a)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-pgvector-336791?style=flat-square&logo=postgresql&logoColor=white&labelColor=0a0a0a)
![Terraform](https://img.shields.io/badge/Terraform-844fba?style=flat-square&logo=terraform&logoColor=white&labelColor=0a0a0a)
![AWS](https://img.shields.io/badge/AWS-ECS_Fargate-ff9900?style=flat-square&logo=amazonaws&logoColor=white&labelColor=0a0a0a)

## Workflow

We follow **Gitflow** across every repo: `feature/*` · `bugfix/*` · `hotfix/*` branches into
`develop`, releases cut from `develop` into `main`. See each repo's `.github/CONTRIBUTING.md` for
the full convention.

## Get in touch

Questions, bug reports, or ideas? Open an issue on the relevant repo above — that's where we track
everything.

---

<div align="center">

KntroSoft · building ReqsAI · UPC Software Engineering · 2026

</div>
