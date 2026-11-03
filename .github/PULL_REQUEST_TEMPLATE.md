## Summary & Architectural Scope

- **Implements / Closes**: `#`
- **Target Layer / Component**: `e.g., L0 (hikari-org-infra) | L1 (hikari-platform) | L2 (hikari-workloads) | AI Agent Service`

---

## Change Summary

| Dimension | Details |
| :--- | :--- |
| **1. What Changed** | <!-- Files, Terraform modules, Crossplane compositions, Kustomize overlays, or service logic modified --> |
| **2. Why / Impact** | <!-- Architectural improvement, self-service capability added, FinOps optimization, or bug resolved --> |
| **3. Blast Radius & Risks** | <!-- Affected environment (`nonprod` / `prd`), cluster, or state layer (`None` if isolated) --> |
| **4. Rollback Plan** | `git revert <merge-sha>` |

---

## Verification & Supply-Chain Security Checklist

- [ ] **Local & CI Checks Pass**: Unit tests, static typing, Kustomize rendering (`kubectl kustomize`), or `terraform fmt -check -recursive && terraform validate` pass cleanly.
- [ ] **CI Supply-Chain Hardening** *(if touching `.github/workflows/`)*:
  - Runner image is explicitly pinned (e.g., `runs-on: ubuntu-24.04` — never `ubuntu-latest`).
  - All `uses:` actions are pinned to full **40-character commit SHAs** (`@<sha> # vX.Y.Z`) with `persist-credentials: false` on `actions/checkout`.
- [ ] **Container & Kubernetes Security** *(if touching `Dockerfile` or `deploy/`)*:
  - No `:latest` image tags used; workloads run as non-root (`runAsNonRoot: true`, UID `65532`) and satisfy all Spoke Kyverno policies.
- [ ] **Cloud & Terraform Governance** *(if touching `hikari-org-infra` or `claims/`)*:
  - Zero static credentials introduced (Workload Identity / OIDC only) and mandatory cost-attribution labels (`app`, `environment`, `component`, `managed_by`) applied.
