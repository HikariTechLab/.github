# HikariTechLab — Organization `.github` Repository

[![Organization](https://img.shields.io/badge/GitHub-HikariTechLab-0F172A?style=flat-square&logo=github&logoColor=38BDF8)](https://github.com/HikariTechLab)
[![Domain](https://img.shields.io/badge/Domain-hikaritech.co-0F172A?style=flat-square&logo=googlecloud&logoColor=34D399)](https://github.com/HikariTechLab/hikari-org-infra)

This repository is the central governance, community health, and public profile source of truth for the **[HikariTechLab](https://github.com/HikariTechLab)** (`hikaritech.co`) GitHub organization.

Files defined here are automatically inherited by all repositories across `HikariTechLab` unless explicitly overridden at the repository level.

---

## Repository Structure

```text
.github/
├── profile/
│   ├── README.md                           # Public Organization Profile rendered at github.com/HikariTechLab
│   └── assets/
│       └── hikari-banner.svg               # Custom SVG hero banner for the organization profile
├── .github/
│   ├── PULL_REQUEST_TEMPLATE.md            # Org-wide PR template (Four Findings, 3-Tier Pyramid, CAS Rollback)
│   └── ISSUE_TEMPLATE/
│       ├── config.yml                      # Issue chooser configuration & security routing
│       ├── feature_spec.yml                # Spec-Driven Feature Demand (Gherkin + @scope contract)
│       ├── bug_report.yml                  # Deterministic Bug & Regression Report
│       └── infra_change.yml                # Declarative GCP / Terraform Infrastructure Change Request
├── CONTRIBUTING.md                         # Engineering workflow, 3-Tier verification & toolchain standards
├── SECURITY.md                             # Vulnerability disclosure & agentic/cloud security invariants
├── CODE_OF_CONDUCT.md                      # Organization code of conduct
├── SUPPORT.md                              # Support channels & repository routing
└── README.md                               # This file
```

---

## Core Engineering Standards Across `HikariTechLab`

1. **Spec-First & PR-Only Changes**:
   - Every code or infrastructure change originates from an explicit specification (a Gherkin behavioral contract, an `AGENTS.md` / `SKILL.md` rule, or a tracked issue) and merges via Pull Request.
   - Cloud infrastructure in [`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra) is strictly **PR-only**—never mutate organization resources imperatively in the GCP Console.
2. **The 3-Tier Verification Pyramid**:
   - **Tier 1 (Unit & Static Checks)**: Fast component tests, type checking (`pyright` / `ty` / `tsc`), and formatting (`ruff` / `biome` / `terraform fmt`).
   - **Tier 2 (Locked Behavioral Specs)**: Executable `Given / When / Then` specifications (`tests/specs/**`) locked via SHA-256 digests during agentic execution.
   - **Tier 3 (Blind Holdout Sentinels & Policy Gates)**: Out-of-sample `@sentinel` suites (`sentinels/**`), `sqlglot` AST whitelists, and blast-radius diff checks.
3. **The Four Findings & Atomic Rollback**:
   - Pull requests and agentic runs summarize changes using **The Four Findings** (`Changed`, `Improved`, `Degraded`, `Recommended Action`) and maintain clean rollback paths (`git revert` or Compare-and-Swap `CASUndoToken`).

---

## Updating the Organization Profile

To update the public profile shown on [`https://github.com/HikariTechLab`](https://github.com/HikariTechLab):
1. Edit [`profile/README.md`](profile/README.md) or [`profile/assets/hikari-banner.svg`](profile/assets/hikari-banner.svg).
2. Open a Pull Request and verify that all Markdown links and SVG assets render cleanly in both light and dark themes.
