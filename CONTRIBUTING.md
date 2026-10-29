# Contributing to HikariTechLab

Thank you for contributing to **HikariTechLab** (`hikaritech.co`). Whether you are operating an agentic harness like **[`bansho`](https://github.com/HikariTechLab/bansho)**, developing an autonomous ADK agent like **[`greyhound-gcp`](https://github.com/HikariTechLab/greyhound-gcp)** or **[`job-search-agent`](https://github.com/HikariTechLab/job-search-agent)**, or authoring declarative Terraform in **[`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra)**, all contributions follow our deterministic engineering standards.

---

## 1. Core Principles

1. **Spec Before Code**:
   - Define the behavioral contract before writing implementation code. In repositories equipped with [`bansho`](https://github.com/HikariTechLab/bansho), draft and confirm a Gherkin specification (`Feature` / `Scenario` / `Given` / `When` / `Then`) with explicit `@scope(...)` boundaries first.
   - Read the repository's `AGENTS.md`, `GEMINI.md`, `skill.yaml`, and `skills/<domain>/SKILL.md` before proposing changes.
2. **Scoped Blast Radius**:
   - Keep every Pull Request focused on a single ticket or specification.
   - Do not modify files outside the target feature's `allowed_scope` or touch `protected_paths` unless explicitly required by an infrastructure/harness ticket.
3. **Never Weaken Tests or Guardrails (No Reward Hacking)**:
   - Do not delete assertions, loosen type checks, bypass `before_tool_callback` security hooks, or modify hidden `sentinels/**` to make a failing build pass.
4. **PR-Only Infrastructure**:
   - In [`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra), never apply manual fixes in the Google Cloud Console. Every change must be committed, planned (`make plan`), reviewed, and applied (`make apply`) from version control.

---

## 2. The 3-Tier Verification Pyramid

Before requesting review or promoting a candidate checkpoint, verify your work against all applicable tiers:

| Tier | Purpose | Verification Command Examples |
| :--- | :--- | :--- |
| **Tier 1: Component, Lint & Types** | Fast unit tests, formatting, and strict static analysis; new logic requires unit tests in `test_scope`. | `uv run pytest tests/unit`<br>`ruff check && ruff format --check`<br>`bun run check && bun run lint && bun test`<br>`terraform fmt -check -recursive && terraform validate` |
| **Tier 2: Behavioral Spec (`tests/specs/`)** | Proves the `Given / When / Then` contract transitions cleanly from Red to Green. | `uv run pytest tests/specs -v`<br>`agents-cli eval run` |
| **Tier 3: Holdout Sentinels & Policy Gates** | Ensures zero regressions across security, auth, billing, and data-equivalence invariants. | `uv run pytest sentinels -v`<br>`just check` |

---

## 3. Toolchain Standards by Stack

### Python & Google Cloud ADK Repositories (`bansho`, `greyhound-gcp`, `job-search-agent`, `agentic-harness`)
- **Package & Environment Management**: Always use [`uv`](https://docs.astral.sh/uv/) (`uv sync`, `uv run ...`) or `agents-cli install`. Never run global `pip install`.
- **Code Style & Typing**:
  - Format and lint with `ruff` (`uv run ruff check` and `uv run ruff format --check`).
  - Enforce static type annotations using Pydantic v2 schemas and `pyright` / `ty`.
- **Agent Security Callbacks**:
  - Any tool interacting with external data sources, SQL engines, or subprocesses must enforce `shell=False`, sanitize untrusted outputs with XML delimiters, and validate AST/arguments in `before_tool_callback`.

### TypeScript, CLI & Web Applications (`bansho/packages/*`, `aftermark`)
- **Tooling**: Use `bun` (for `aftermark`) or `npm --prefix packages/<pkg>` (for `bansho` workspaces) as documented in the repository `README.md`.
- **Quality Gates**:
  - Strict TypeScript compilation (`tsc --noEmit` / `bun run check`).
  - Formatting and linting via `biome` or `eslint`.
  - Unit and component tests via `vitest` or `bun test`.

### Terraform & Google Cloud Infrastructure (`hikari-org-infra`)
- **Layered State Discipline**:
  - **Layer 0 (`bootstrap/`)**: Org-level foundation (`Common`, `prd`, `nonprod` folders, Shared VPCs, centralized logging, billing export). High blast radius—review carefully.
  - **Layer 1 (`apps/<app>/<env>/` & `clients/<client>/<env>/`)**: Isolated state per application or client environment. Consume Layer 0 outputs via `terraform_remote_state`—never hardcode folder or network IDs.
- **Service Decomposition**: Keep resources separated into one file per domain (`project.tf`, `apis.tf`, `bigquery.tf`, `service_accounts.tf`, `secrets.tf`, `budgets.tf`, `monitoring.tf`).
- **Mandatory Attribution Labels**: Ensure all resources inherit `default_labels` (`app`, `environment`, `component`, `managed_by = "terraform"`).
- **Formatting & Lockfiles**: Run `terraform fmt -recursive` before committing. Commit `.terraform.lock.hcl` and `.terraform-version`; never commit `.terraform/`, `.tfstate`, or `tfplan` files.

---

## 4. Branching, Commits & Pull Requests

### Branch Naming
- Feature / Spec branches: `feat/<ticket-or-slug>` or `bansho/feat-<id>`
- Skill Evolution (AEOS 3) branches: `skill/feat-<id>`
- Bug fixes: `fix/<ticket-or-slug>`
- Infrastructure changes: `infra/<layer>-<slug>`

### Conventional Commits
Use clear, imperative commit messages following Conventional Commits:
- `feat(scope): add currency validation to checkout service`
- `fix(guardrails): strip multiline SQL comments before AST check`
- `infra(clients/tbf): provision nonprod Vertex AI service account and budget`
- `skill(checkout-api): codify decimal rounding invariant from FEAT-405`

### Pull Request Summary (The Four Findings)
Every Pull Request should clearly state **The Four Findings**:
1. **Changed**: Exact files, modules, or Terraform resources modified within scope.
2. **Improved**: Behavioral specs satisfied, byte/cost savings proved, or test coverage added.
3. **Degraded**: Any known trade-offs, latency impacts, or follow-up items (`None` if zero regressions).
4. **Recommended Action**: `Promote / Merge`, `Senior Review Required`, or `Hold` with a clear rollback command (`git revert` or `CASUndoToken`).
