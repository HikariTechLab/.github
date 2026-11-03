# Contributing to HikariTechLab

Thank you for contributing to **HikariTech (`hikaritech.co`)**. Whether you are working on our 3-layer **Cloud-Native Internal Developer Platform** ([`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra), [`hikari-platform`](https://github.com/HikariTechLab/hikari-platform), [`hikari-workloads`](https://github.com/HikariTechLab/hikari-workloads)) or our **Autonomous AI & Multi-Agent Systems** ([`cerebro`](https://github.com/HikariTechLab/cerebro), [`agentic-harness`](https://github.com/HikariTechLab/agentic-harness), [`job-search-agent`](https://github.com/HikariTechLab/job-search-agent)), all contributions follow the engineering standards below.

---

## 1. Core Engineering Principles

1. **Declarative & PR-Only Changes**:
   - Every code, GitOps manifest, Crossplane composition, or Terraform change must be proposed via Pull Request.
   - Never apply manual mutations in the GCP Console or run imperative `kubectl edit` against Hub or Spoke clusters.
2. **Hardened CI/CD & Container Supply Chain**:
   - **No `:latest` Tags**: Never use `ubuntu-latest` in GitHub Actions or `:latest` in `Dockerfile` / Kubernetes manifests. Pin GitHub hosted runners explicitly (e.g., `runs-on: ubuntu-24.04`) and use immutable semantic tags or SHA digests for container images.
   - **40-Character Commit SHA Pinning**: Every `uses:` step in `.github/workflows/*.yml` must be pinned to a full 40-character Git commit SHA (`uses: owner/action@<40-char-sha> # vX.Y.Z`) and `actions/checkout` must set `persist-credentials: false`.
   - **Distroless Non-Root Runtimes**: Containerized workloads in [`hikari-workloads`](https://github.com/HikariTechLab/hikari-workloads) must run on hardened non-root base images (e.g., `gcr.io/distroless/nodejs22-debian12:nonroot`, UID/GID `65532`) with `allowPrivilegeEscalation: false` and `capabilities.drop: ["ALL"]`.
3. **Scoped Blast Radius & Clean Rollback**:
   - Keep Pull Requests focused on a single concern or layer (`L0`, `L1`, `L2`, or a single microservice/agent).
   - Every PR must be cleanly revertible via `git revert <merge-sha>`.

---

## 2. Standards by Platform Layer & Stack

### Layer 0 — Cloud Foundation (`hikari-org-infra`)
- **Layered State Isolation**:
  - `bootstrap/`: Org-wide hierarchy (`Common`, `prd`, `nonprod`), Shared VPCs (`europe-west1`), 365-day org audit sink, billing export.
  - `platform/<env>/`: GKE Hub cluster (`gke-{env}-platform-hub`), Artifact Registry, Crossplane & CI Workload Identity Federation (OIDC).
  - `apps/<app>/<env>/` & `clients/<client>/<env>/`: Isolated per-app and per-client state prefixes, service accounts, and budgets.
- **Validation**:
  - Run `terraform fmt -check -recursive` and offline `terraform init -backend=false && terraform validate` across modified layers before opening a PR.
  - Ensure all resources inherit mandatory attribution labels (`app`, `environment`, `component`, `managed_by = "terraform"`).

### Layer 1 — IDP Control Plane (`hikari-platform`)
- **GitOps & Fleet Orchestration**:
  - Argo CD App-of-Apps manifests live in `bootstrap/apps/` with explicit `sync-wave` ordering (`-2` through `4`).
  - Crossplane v2 XRDs and Compositions live in `crossplane/apis/` and `crossplane/compositions/`.
  - Spoke cluster Day-2 add-ons (Cilium, Kyverno, ESO) are orchestrated by Project Sveltos `ClusterProfile` manifests in `fleet/profiles/`.
- **Validation**:
  - Validate YAML syntax and `kubectl kustomize policies/spoke` before pushing.

### Layer 2 — Tenant Workloads & Claims (`hikari-workloads`)
- **Self-Service Claims (`claims/<env>/*`)**:
  - Request GCP buckets (`StorageBucket`) or Spoke GKE clusters (`GkeCluster`) via declarative YAML claims.
- **Workloads (`deploy/clusters/*` & `services/*`)**:
  - Verify Kustomize overlays with `kubectl kustomize deploy/clusters/<cluster>` and run `npm test` / `npm run build` for modified services.

### Python & Multi-Agent AI Repositories (`cerebro`, `agentic-harness`, `job-search-agent`)
- **Environment & Tooling**:
  - Use [`uv`](https://docs.astral.sh/uv/) (`uv sync`, `uv run pytest`, `uv run ruff check`) for Python repositories and `npm ci --ignore-scripts` for TypeScript/Node.js repositories.
- **Security Guardrails**:
  - Sanitize and delimit untrusted external inputs before passing them into LLM prompts, and never store static cloud credentials in repository secrets or `.env` files.

---

## 3. Branching & Conventional Commits

- **Branches**: `feat/<slug>`, `fix/<slug>`, `infra/<layer>-<slug>`, `chore/<slug>`, `security/<slug>`
- **Commit Messages**:
  - `feat(crossplane): add XGkeCluster private spoke composition`
  - `infra(platform/nonprod): enable GKE Hub Workload Identity pool`
  - `security(ci): pin GitHub Actions to commit SHAs and runner to ubuntu-24.04`
  - `fix(status-api): enforce read-only root filesystem in deployment spec`
