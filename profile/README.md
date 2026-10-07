<div align="center">

<img src="https://raw.githubusercontent.com/Kntro-Soft/.github/main/profile/banner.png" alt="KntroSoft" width="100%" />

**KntroSoft is a software studio building [ReqsAI](https://reqsai.tech) — an AI copilot that
turns live discovery meetings — in the room or on Meet, Zoom or Teams — into structured, backlog-ready requirements.**

[![Live App](https://img.shields.io/badge/ReqsAI-reqsai.tech-22c55e?style=flat-square&labelColor=0a0a0a)](https://reqsai.tech)
[![Status](https://img.shields.io/badge/status-MVP%20live-4ade80?style=flat-square&labelColor=0a0a0a)](https://reqsai.tech)
[![Deploy](https://img.shields.io/badge/deploy-GitHub%20Actions-16a34a?style=flat-square&logo=githubactions&logoColor=white&labelColor=0a0a0a)](https://github.com/Kntro-Soft/reqsai-infra/actions/workflows/deploy-mvp.yml)

</div>

---

## Who we are

**KntroSoft** is the organization behind [**ReqsAI**](https://reqsai.tech), our flagship
product. This org hosts ReqsAI's full stack (backend, frontend, infrastructure, marketing site)
plus the academic work behind it. We're a small team — this is where the code, decisions, and
architecture records live.

## What we build: ReqsAI

An analyst runs a live discovery session — in person with the microphone, or during a Meet, Zoom
or Teams call by sharing the meeting tab's audio — and an LLM listens in real time, surfacing draft
user stories with Gherkin acceptance criteria, edge cases, clarifying questions, and duplicate/update
detection against the existing backlog as the conversation unfolds. Off-topic talk and instructions
spoken to the AI are ignored, and nothing reaches the backlog without the analyst's approval.

The current release is the **MVP**: capture, live transcription, AI suggestions, human review and a
Gherkin backlog. Jira export, Stripe billing, custom roles and team members are built but switched off
with feature flags until the pilots ask for them.

**Try it:** [reqsai.tech](https://reqsai.tech)

## Repositories

| Repo | What it is | Stack |
|---|---|---|
| [**reqsai-api**](https://github.com/Kntro-Soft/reqsai-api) | Backend — modular monolith (`iam`, `billing`, `workspace`, `discovery`, `gateway`) | Spring Boot 4 · Spring Modulith · PostgreSQL/pgvector |
| [**reqsai-web**](https://github.com/Kntro-Soft/reqsai-web) | Frontend — the SaaS app itself | Angular 22 (zoneless) · TypeScript · Tailwind CSS v4 |
| [**reqsai-infra**](https://github.com/Kntro-Soft/reqsai-infra) | AWS infrastructure as code and the MVP deploy pipeline | Terraform · Ansible · Docker Compose on EC2 · GitHub Actions |
| [**reqsai-landing**](https://github.com/Kntro-Soft/reqsai-landing) | Public marketing site | React · Vite · Tailwind CSS |
| [**reqsai-report**](https://github.com/Kntro-Soft/reqsai-report) | Academic report (UPC Software Engineering) | DDD · C4 model · Lean UX |

## Architecture at a glance

- **Multi-tenant** — schema-per-tenant isolation in a single Postgres instance.
- **Modular monolith** — Spring Modulith-enforced bounded contexts (`iam`, `billing`, `workspace`,
  `discovery`, `gateway`), each with an explicit public API surface; no reaching into another
  module's internals.
- **Real-time** — browser audio (mic, or mic mixed with the meeting tab) streamed as 16 kHz PCM over
  WebSocket to Deepgram/AssemblyAI with keepalive and transparent reconnect; STOMP over WebSocket for
  live transcript, AI suggestions and session presence.
- **AI with guardrails** — OpenAI/Gemini in JSON mode; the transcript is treated as untrusted data, so
  small talk, trivia, code requests and prompt injection never become stories.
- **Vector search** — pgvector-backed similarity search for duplicate/related-story detection.
- **Integrations** — Jira Cloud (OAuth 2.0 + API token) for backlog push/import; Stripe for
  subscription billing.
- **Lean production on AWS** — the MVP runs on a single EC2 Graviton instance (`t4g.small`) with
  Docker Compose and Caddy (automatic HTTPS), provisioned with Terraform and Ansible. A push to `main`
  deploys through GitHub Actions over AWS SSM with OIDC: no open SSH port, no container registry, no
  long-lived AWS keys.

## Tech stack

![Java](https://img.shields.io/badge/Java-25-f89820?style=flat-square&logo=openjdk&logoColor=white&labelColor=0a0a0a)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-4-6db33f?style=flat-square&logo=springboot&logoColor=white&labelColor=0a0a0a)
![Angular](https://img.shields.io/badge/Angular-22-dd0031?style=flat-square&logo=angular&logoColor=white&labelColor=0a0a0a)
![TypeScript](https://img.shields.io/badge/TypeScript-3178c6?style=flat-square&logo=typescript&logoColor=white&labelColor=0a0a0a)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-pgvector-336791?style=flat-square&logo=postgresql&logoColor=white&labelColor=0a0a0a)
![Terraform](https://img.shields.io/badge/Terraform-844fba?style=flat-square&logo=terraform&logoColor=white&labelColor=0a0a0a)
![AWS](https://img.shields.io/badge/AWS-EC2_Graviton-ff9900?style=flat-square&logo=amazonaws&logoColor=white&labelColor=0a0a0a)
![Docker](https://img.shields.io/badge/Docker_Compose-2496ed?style=flat-square&logo=docker&logoColor=white&labelColor=0a0a0a)
![Ansible](https://img.shields.io/badge/Ansible-ee0000?style=flat-square&logo=ansible&logoColor=white&labelColor=0a0a0a)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088ff?style=flat-square&logo=githubactions&logoColor=white&labelColor=0a0a0a)

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
