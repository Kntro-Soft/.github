<div align="center">

<!-- Banner: drop banner.png (1600x400) at profile/banner.png and uncomment the line below -->
<!-- <img src="https://raw.githubusercontent.com/Kntro-Soft/.github/main/profile/banner.png" alt="ReqsAI" width="100%" /> -->

# ReqsAI

**AI-powered requirements elicitation, from meeting to backlog.**

ReqsAI turns discovery conversations into structured user stories, Gherkin acceptance criteria,
and backlog-ready outputs — in real time, as the meeting happens.

[![Live App](https://img.shields.io/badge/live-app.tamci.app-ef4444?style=flat-square)](https://app.tamci.app)
[![API](https://img.shields.io/badge/API-api.tamci.app-0f172a?style=flat-square)](https://api.tamci.app)
[![License](https://img.shields.io/badge/status-active%20development-facc15?style=flat-square)]()

</div>

---

## What is ReqsAI?

A B2B SaaS platform where an analyst runs a live discovery session (voice or text), and an LLM
listens in real time — surfacing draft user stories, edge cases, clarifying questions, and
duplicate/update detection against the existing backlog as the conversation unfolds. Decisions are
reviewed and accepted on the spot; the backlog stays structured, deduplicated, and exportable
(including a direct push to Jira).

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

Built by the ReqsAI team · UPC Software Engineering · 2026

</div>
