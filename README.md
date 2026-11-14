# DNP42/.github

Shared Claude workflows. Every run acts as the DNPclaude GitHub App (App ID 5756777).
A repo calls a workflow with a few lines. Copy a file from `examples/` into `.github/workflows/`.

## Workflows

| File | What it does | When it runs |
| --- | --- | --- |
| `claude.yml` | Answers `@claude` mentions in issues, PRs and reviews. | On every mention. |
| `tests.yml` | Runs your `gate` command. Posts one sticky PR comment with PASS or FAIL and the last 30 lines of output. No Claude call. | On every PR. |
| `ui-review.yml` | Claude reviews the PR for layout, accessibility, i18n strings and responsive behavior. Read-only. | On demand. |
| `api-review.yml` | Claude reviews the PR for routes, validation, error handling, auth and breaking changes. Read-only. | On demand. |

The review workflows run on demand only, to save subscription usage. Trigger one of two ways:

- Add the PR label `ui-review` or `api-review`.
- Comment `/ui-review` or `/api-review` on the PR (owner, member or collaborator only).

They do not run on push.

## Opt in

1. Copy the example files you want into `.github/workflows/` in your repo.
2. Set the secrets and variables below.
3. For review workflows, create the labels `ui-review` and `api-review`.

Inputs (all optional except `gate` in `tests.yml`):

- `app-id`: DNPclaude App ID. Default `5756777`.
- `agent-log-issue`: issue number. Each run adds one line to it. Empty means no log.
- `gate` and `node-version` (`tests.yml` only).
- `allowed-tools` (`claude.yml` only): Claude tool allowlist. Default is npm install, ci, audit, lint and build.

## Secrets and variables

| Name | Kind | Needed |
| --- | --- | --- |
| `CLAUDE_CODE_OAUTH_TOKEN` | secret | `claude.yml`, `ui-review.yml`, `api-review.yml` (not `tests.yml`) |
| `DNPCLAUDE_PRIVATE_KEY` | secret | all workflows |
| `DNPCLAUDE_APP_ID` | variable | optional, the examples fall back to 5756777 |
| `AGENT_LOG_ISSUE` | variable | optional |

- DNP42 repos: set `DNPCLAUDE_PRIVATE_KEY` and `CLAUDE_CODE_OAUTH_TOKEN` as org secrets, and `DNPCLAUDE_APP_ID` as an org variable.
- hrco repos: set each one per repo (Settings, Secrets and variables, Actions).
- The callers use `secrets: inherit`.

## Safety

- Each run mints a short-lived app token for the calling repo only, with only the permissions that workflow needs.
- Review workflows give Claude read-only tools. No edits, no pushes.
- Comment and title text goes through `env:` only, never into `run:` scripts.
- Public repo: never put secrets in these files.
