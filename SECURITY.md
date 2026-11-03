# Security Policy & Zero-Trust Architecture

At **HikariTech (`hikaritech.co`)**, security is enforced deterministically across our Google Cloud Organization (**L0**), Kubernetes IDP Control Plane (**L1**), Tenant Workloads (**L2**), and CI/CD pipelines.

---

## Reporting a Vulnerability

If you discover a security vulnerability, policy bypass, or cloud misconfiguration in any `HikariTechLab` repository:

1. **Do NOT open a public GitHub issue or pull request.**
2. **Report privately via GitHub Security Advisories** on the affected repository (`Security` tab → `Report a vulnerability`), or email **`security@hikaritech.co`**.
3. Include:
   - Affected repository and commit SHA / branch.
   - Clear reproduction steps or manifest/configuration snippet.
   - Impact assessment (e.g., privilege escalation, admission policy bypass, credential exposure, or cross-tenant isolation breach).

We acknowledge reports within **48 hours** and coordinate remediation before any disclosure.

---

## Mandatory Security Invariants Across `HikariTechLab`

### 1. CI/CD Supply-Chain Security (GitHub Actions)
- **Immutable 40-Character SHA Pinning**: Mutable tags (`@v4`, `@main`) are strictly prohibited in `.github/workflows/*.yml`. Every third-party or official GitHub Action must be pinned to its verified **40-character commit SHA** (`uses: owner/action@<sha> # vX.Y.Z`).
- **No `:latest` Runner or Container Tags**: Workflows must pin explicit runner images (e.g., `runs-on: ubuntu-24.04`), and container builds must never pull or publish `:latest` tags.
- **Least-Privilege `GITHUB_TOKEN`**: Every workflow declares top-level `permissions: contents: read` and sets `persist-credentials: false` on `actions/checkout`.

### 2. Google Cloud Organization Security (`hikari-org-infra`)
- **100% Keyless Authentication (Zero Static SA Keys)**: Service account key creation is disabled organization-wide (`constraints/iam.disableServiceAccountKeyCreation`). CI/CD pipelines authenticate via **Workload Identity Federation (OIDC)**, and GKE pods authenticate via **GKE Workload Identity**.
- **Default Service Accounts Disabled**: Every GCP project disables default Compute/App Engine service accounts at creation; workloads use dedicated, single-purpose service accounts (`sa-*`).
- **Shared VPC Subnet Isolation & Default-Deny Posture**: `prd` and `nonprod` environments operate in dedicated Shared VPC host projects (`europe-west1`) with per-subnet IAM (`app`, `platform`, `db`), Private Google Access (PGA), and default deny-all firewall baselines.
- **Centralized 365-Day Audit Logging**: Organization-wide Admin Activity, Data Access, and System Event logs sink to `hikari-logging` with 365-day retention.

### 3. Kubernetes IDP & Spoke Fleet Guardrails (`hikari-platform` & `hikari-workloads`)
- **Automated Day-2 Policy Enforcement**: Every Spoke GKE cluster provisioned by Crossplane (`XGkeCluster`) is automatically onboarded by **Project Sveltos** with **Cilium CNI**, **External Secrets Operator**, and **Kyverno** admission policies before tenant workloads are scheduled.
- **Kyverno `ValidationFailureAction: Enforce` Policies**:
  - `disallow-latest-tag`: Blocks any Pod or Deployment using `:latest` or omitting an explicit image tag/digest.
  - `disallow-privileged-containers`: Blocks privileged containers and host namespace sharing (`hostNetwork`, `hostPID`, `hostIPC`).
  - `require-run-as-nonroot`: Enforces `runAsNonRoot: true`, `allowPrivilegeEscalation: false`, and Google Distroless non-root execution (`65532:65532`).
