## Summary & Spec Reference

- **Implements / Closes**: `#` / `FEAT-`
- **Target Scope / Layer**: `e.g., app/ | packages/cli | apps/<app>/nonprod`

---

## The Four Findings

| Dimension | Details |
| :--- | :--- |
| **1. Changed** | <!-- Exact files, modules, or Terraform resources modified within allowed_scope --> |
| **2. Improved** | <!-- Behavioral specs satisfied, byte/cost savings proved, or unit/sentinel coverage added --> |
| **3. Degraded** | <!-- Known trade-offs, latency/cost impact, or follow-ups (`None` if zero regressions) --> |
| **4. Recommended Action** | <!-- `Promote / Merge` | `Senior Review Required` | `Hold` --> |

---

## 3-Tier Verification Pyramid

- [ ] **Tier 1 — Component, Lint & Static Types**: Unit tests (`pytest tests/unit` / `cargo test` / `vitest`), formatter (`ruff` / `cargo fmt` / `biome` / `terraform fmt`), and type/lint checker (`pyright` / `cargo clippy` / `tsc`) pass cleanly.
- [ ] **Tier 2 — Behavioral Spec (`tests/specs/` or Eval Suite)**: Executable Gherkin / ADK evaluation suite passes (Red $\rightarrow$ Green verified).
- [ ] **Tier 3 — Holdout Sentinels & Policy Gates**: Out-of-sample `@sentinel` suite (`pytest sentinels`) or `make plan` / AST validation passes with zero regressions.

---

## Security, Blast Radius & Governance

- [ ] Changes stay strictly within the ticket's `allowed_scope` and do not weaken existing tests or `protected_paths`.
- [ ] Untrusted external inputs are sanitized, XML-delimited, or AST-validated (`sqlglot` / `before_tool_callback`).
- [ ] *(If Infrastructure / `hikari-org-infra`)* `terraform fmt -recursive` was run, state layer boundaries are respected, and mandatory `default_labels` (`app`, `environment`, `component`, `managed_by`) are present.

---

## Rollback Plan

- **Rollback Strategy**: `git revert <merge-sha>` <!-- or CAS Undo Token: `before_version -> after_version` / `make plan && make apply` -->
