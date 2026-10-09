# Contributing to Kntro-Soft / ReqsAI

This is the default contribution guide for every Kntro-Soft repository that has no `CONTRIBUTING.md` of its own
(today `reqsai-landing` and `reqsai-infra`). `reqsai-api`, `reqsai-web` and `reqsai-report` keep their own guide
in `.github/CONTRIBUTING.md`, which follows the same rules and adds the stack-specific details.

## Table of Contents

- [Work management](#work-management)
- [Gitflow](#gitflow)
- [Commits and pull requests](#commits-and-pull-requests)
- [Quality gates](#quality-gates)
- [Traceability](#traceability)
- [Releases and deployment](#releases-and-deployment)
- [Environments and approvals](#environments-and-approvals)
- [Deploy switches](#deploy-switches)
- [Container registry](#container-registry)
- [Reusable workflows](#reusable-workflows)

---

## Work management

All work lives on the [ReqsAI project board](https://github.com/orgs/Kntro-Soft/projects/3), linked to
`reqsai-api`, `reqsai-web`, `reqsai-landing`, `reqsai-infra` and `reqsai-report`.

| Item | Open it with | Notes |
|------|--------------|-------|
| **User Story** | the *User Story* form | As a / I want / so that, plus acceptance criteria (Gherkin scenarios). |
| **Bug** | the *Bug* form | Steps, expected vs actual, and the acceptance criteria that prove the fix. |
| **Task** | the *Task* form | Technical or operational work with verifiable acceptance criteria. |

The forms set the issue type (the board's **Type** field), a label and add the issue to the board. Fields:

| Field | Values |
|-------|--------|
| Status | **Backlog** (created) → **Ready** (refined, criteria agreed) → **In Progress** (branch open) → **In Review** (PR open) → **Testing** (in `develop` or a release branch) → **Done** (released to `produccion` and tagged) |
| Priority | High, Medium, Low |
| Type | User Story, Bug, Task (the organization issue types) |

## Gitflow

| Branch | From | Merges into | Purpose |
|--------|------|-------------|---------|
| `main` | — | — | What runs in production. Every merge is a tagged release. |
| `develop` | `main` | — | Integration branch (not the default branch: `main` is). |
| `feature/<issue>-<slug>` | `develop` | `develop` | User story or task, e.g. `feature/123-export-to-jira`. |
| `bugfix/<issue>-<slug>` | `develop` | `develop` | Bug found before a release, e.g. `bugfix/130-tenant-leak`. |
| `release/X.Y.Z` | `develop` | `main`, then back into `develop` | Stabilization of version X.Y.Z; it is what gets deployed. |
| `hotfix/X.Y.Z` | `main` | `main`, then back into `develop` | Urgent production fix (patch version). |

No other prefixes (`feat/`, `fix/`, `ci/`, `docs/`...) are used for branches.

## Commits and pull requests

- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) in English:
  `<type>(<scope>): <description>`; reference the issue in the body (`Refs #123`).
- Every pull request targets `develop`; only `release/X.Y.Z` and `hotfix/X.Y.Z` target `main`.
- The description says `Closes #<issue>` so the merge closes the issue and links PR ↔ issue on the board.
- Merge with a merge commit (the rulesets allow nothing else), so the commit that was deployed stays in the
  history of `main`.

## Quality gates

`main` and `develop` of every product repository have a ruleset:

- pull request required, **1 approval**, stale approvals dismissed on new pushes, the last push must be approved
  by someone else, conversations resolved;
- no force-push, no deletion;
- required status checks (the CI jobs that run on **every** pull request, never path-filtered ones);
- organization admins may bypass **only through a pull request** (needed while the team cannot always review
  its own changes).

| Repository | Required checks | Extra rule on `main` |
|------------|-----------------|----------------------|
| reqsai-api | `Unit tests`, `Integration tests`, `Architecture & build`, `Analyze (Java)` | the PR head must be deployed to `produccion` |
| reqsai-web | `lint-test-build`, `Analyze (JavaScript / TypeScript)` | the PR head must be deployed to `produccion` |
| reqsai-report | `Lint Markdown files` | — |
| reqsai-landing | none yet (`Lint & build` once its CI is merged) | — |
| reqsai-infra | none yet (`Lint` once its CI is merged) | — |

## Traceability

```
Issue #123 ─► feature/123-slug ─► commits "Refs #123" ─► PR "Closes #123" → develop
          ─► release/X.Y.Z ─► build once (image ghcr.io/kntro-soft/<repo>:<sha> or Vercel build)
          ─► deployment to "produccion" (approved) ─► PR release/X.Y.Z → main
          ─► tag vX.Y.Z + GitHub Release on the deployed commit
```

Each hop is a GitHub link: the issue lists its PRs, the release notes list the PRs, the tag points to the
deployed commit, the image label `org.opencontainers.image.revision` holds that commit, and the environment
page lists each deployment with its commit and run.

## Releases and deployment

| Repository | Release pipeline | Where it is approved | How it reaches production |
|------------|------------------|----------------------|---------------------------|
| reqsai-api, reqsai-web | `release.yml` / `hotfix.yml` → `delivery.yml`: CI → image built once (`linux/arm64`, tag = commit SHA, GHCR) → `deploy` → `release-pr` | job `deploy`, environment **`produccion`** of the app | `reqsai-infra` `deploy-mvp.yml` with `image_source=registry` deploys that same image to the EC2 host (GitHub OIDC → IAM role → SSH over SSM → Docker Compose) |
| reqsai-landing | `release.yml` / `hotfix.yml` → `delivery.yml`: CI → `vercel build --prod` once → `deploy-preview` → `deploy-production` | environments **`preview`** and **`produccion`** | `vercel deploy --prebuilt --skip-domain`, then `vercel promote` of that same deployment |
| reqsai-infra | `deploy-mvp.yml` on push to `main` (configuration), manual runs | job `approve`, environment **`produccion`** of reqsai-infra, unless the app already approved that SHA | Ansible over SSM (environment `mvp`, the only one the AWS role trusts) |
| reqsai-report | none (the PDF/Word files are workflow artifacts) | — | — |

Order of a release (apps and landing):

1. Cut `release/X.Y.Z` from `develop` (or `hotfix/X.Y.Z` from `main`) and open its PR to `main`.
2. The pipeline builds the artifact **once** and waits for approval in the environment.
3. After the production deployment succeeds, the pipeline comments on the release PR (deployed commit, artifact,
   runs) and marks it ready. A release PR cannot merge before that: the `main` ruleset requires a successful
   `produccion` deployment of its head commit (apps).
4. Merge the PR. `tag-release.yml` checks the deployment again, creates `vX.Y.Z` and its GitHub Release **on the
   deployed commit**, and links the back-merge `release/X.Y.Z → develop`.

Deploying before merging keeps `main` equal to what runs in production and makes the tag a fact, not a promise:
if the deploy fails, nothing is merged or tagged and the branch gets a fix. Redeploys and rollbacks reuse a
tagged artifact (`deploy.yml` on a tag in the apps) instead of rebuilding.

## Environments and approvals

Every environment that receives a deploy has the required reviewer `jhosepmyr` (`prevent_self_review: false`)
and admins cannot bypass it.

| Repository | Environment | Allowed refs | Used by |
|------------|-------------|--------------|---------|
| reqsai-api, reqsai-web | `produccion` | `main`, `release/*`, `hotfix/*`, tags `v*` | `delivery.yml` job `deploy`, `deploy.yml` |
| reqsai-landing | `preview` | `release/*`, `hotfix/*` | `delivery.yml` job `deploy-preview` |
| reqsai-landing | `produccion` | `release/*`, `hotfix/*` | `delivery.yml` job `deploy-production` |
| reqsai-infra | `produccion` | `main` | `deploy-mvp.yml` job `approve` |
| reqsai-infra | `mvp` (unchanged) | `main` | `deploy-mvp.yml` job `deploy`; its name is part of the AWS role trust |

DEV is each developer's machine and TEST is CI (Testcontainers, Vitest) on every PR. There is **no staging**:
ReqsAI runs on a single EC2 host. Adding one costs about US$ 18.3/month for a second `t4g.small` 24×7 in
us-east-1 (EC2 + 30 GB gp3 + public IPv4); see section 14.7 of the
[reqsai-infra deploy guide](https://github.com/Kntro-Soft/reqsai-infra/blob/main/docs/deploy-ec2-docker-compose.md)
for the options and AWS price sources. It would add a `staging` job before `deploy`, using the same image.

## Deploy switches

Every deploy channel has an on/off switch: an **organization variable** in *Kntro-Soft → Settings → Secrets and
variables → Actions → Variables* (one control panel; organization variables reach public repositories on the
Free plan). Only the exact value `true` turns a channel on; unset means off. A job that is switched off is
skipped and the run summary says which variable stopped it.

| Variable | Repository · job | Controls |
|----------|------------------|----------|
| `ENABLE_REQSAI_API_IMAGE` | reqsai-api · `delivery.yml` `image` | Publishing the API image to GHCR (off also stops its deploy) |
| `ENABLE_REQSAI_API_DEPLOY` | reqsai-api · `delivery.yml` `deploy`, `deploy.yml` | Deploying the API |
| `ENABLE_REQSAI_WEB_IMAGE` | reqsai-web · `delivery.yml` `image` | Publishing the web image to GHCR |
| `ENABLE_REQSAI_WEB_DEPLOY` | reqsai-web · `delivery.yml` `deploy`, `deploy.yml` | Deploying the web app |
| `ENABLE_REQSAI_INFRA_DEPLOY` | reqsai-infra · `deploy-mvp.yml` | Every deploy to the MVP host (the app deploy jobs fail if it is off) |
| `ENABLE_REQSAI_LANDING_PREVIEW` | reqsai-landing · `delivery.yml` `build`, `deploy-preview` | The landing build and preview (production only promotes a preview) |
| `ENABLE_REQSAI_LANDING_PRODUCCION` | reqsai-landing · `delivery.yml` `deploy-production` | Promoting the landing to production |

## Container registry

The app images go to **GHCR** (`ghcr.io/kntro-soft/reqsai-api`, `ghcr.io/kntro-soft/reqsai-web`), tagged with the
full commit SHA: free for these public repositories, pushed with the workflow's `GITHUB_TOKEN`, and pulled by
`reqsai-infra` with its own `GITHUB_TOKEN`, so no AWS resource or credential changes. The EC2 host still
receives an image archive over SSM and never talks to a registry. ECR (US$ 0.10 per GB-month, free transfer to
EC2 in the same region) was not created: it needs a new repository, OIDC roles with push rights and pull rights
for the host; the comparison is in section 14.7 of the reqsai-infra guide.

## Reusable workflows

`delivery.yml` is a reusable workflow **inside** each repository, called by `release.yml` and `hotfix.yml`. The
API and web copies differ only in the app name; the landing one in its target (Vercel). Following the strategy,
nothing is moved to a central repository yet. Extract a shared workflow (for example
`Kntro-Soft/.github/.github/workflows/image-release.yml` with an `app` input) only when the pattern is proven:
at least two releases of each app through these pipelines without changes to the copies, and the same need in a
third repository. Until then, a change to one copy is applied to the other in the same pull request.
