# Security Policy & Engineering Guardrails

At **HikariTechLab** (`hikaritech.co`), security is enforced deterministically at the harness, AST, and cloud-organization layers rather than delegated to probabilistic model behavior.

---

## Reporting a Vulnerability

If you discover a security vulnerability, guardrail bypass, prompt-injection vector, or cloud misconfiguration in any `HikariTechLab` repository:

1. **Do NOT open a public GitHub issue or pull request.**
2. **Report privately via GitHub Security Advisories** on the affected repository (`Security` tab → `Report a vulnerability`), or email **`security@hikaritech.co`**.
3. Include:
   - The affected repository and commit SHA / branch.
   - A minimal, deterministic reproduction (input payload, Gherkin spec, SQL string, or Terraform configuration).
   - Impact assessment (e.g., blast-radius escape, sentinel leakage, data mutation, or credential exposure).

We acknowledge reports within **48 hours** and coordinate remediation and disclosure once a verified fix is landed.

---

## HikariTechLab Defense-in-Depth Baseline

All repositories and agents across `HikariTechLab` must uphold the following mandatory security invariants:

### 1. Agentic Blast-Radius Isolation & Sentinel Blindness
- **Scoped File I/O (`before_tool_callback`)**: Coding and automation agents must enforce strict path-level allowlists (`allowed_scope`) and blocklists (`protected_paths`) defined in `skill.yaml` or policy manifests.
- **Sentinel Blindness**: Out-of-sample verification suites (`sentinels/**`) must never be readable by proposer agents. Failed sentinel outputs must be sanitized (`redact_sentinel_output`) so file paths and assertion bodies never leak into the LLM context.
- **SHA-256 Spec Locking**: Behavioral specifications (`tests/specs/**`) are hashed with SHA-256 prior to worker execution; any modification to a locked spec test during an agent run immediately aborts and rejects the candidate.

### 2. Zero-Mutation & Anti-Prompt-Injection Guarantees
- **Read-Only Cloud & AST-Validated Database Access**: FinOps engines and analytical agents (such as [`greyhound-gcp`](https://github.com/HikariTechLab/greyhound-gcp) on GCP / BigQuery) must enforce strict non-mutation guarantees:
  1. Static IaC and SQL/SQLX scans operate 100% offline with zero credentials and zero network calls.
  2. Live GCP telemetry requests only read-only OAuth scopes (`cloud-platform.read-only`, `monitoring.read`, `cloud-billing.readonly`) and is gated at runtime by `GcpAuthClient::ensure_readonly_request` (rejecting any `DELETE`, `PUT`, `PATCH`, or mutating `POST`).
  3. BigQuery SQL verification parses queries into a read-only AST (`SELECT`, `UNION`, `INTERSECT`, `EXCEPT`), strips SQL comments, rejects mutating/DDL statements, and uses `$0` dry-run jobs (`dryRun: true`) or bounded read-only metadata queries.
- **Untrusted Input Isolation**: External telemetry (`INFORMATION_SCHEMA` logs, ATS job postings, GitHub issue bodies, test outputs) must be sanitized, scanned for prompt-injection signatures, and enclosed in explicit XML boundary tags (e.g., `<untrusted_job_description>`) before entering model prompts.
- **Subprocess Hermeticity**: Subprocesses executed by harnesses must run with `shell=False`, validated command vectors, and secret-scrubbed environment variables.

### 3. Google Cloud Organization Security (`hikaritech.co`)
Managed declaratively in [`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra):
- **Zero Service Account Keys**: Service account key creation is disabled by org policy. Use **Application Default Credentials (ADC)** locally and **Workload Identity Federation** in CI/CD.
- **Default Service Accounts Disabled**: Every project disables default Compute/App Engine service accounts upon creation; workloads run under dedicated, least-privilege service accounts.
- **Group-Based IAM & Break-Glass Separation**: IAM bindings target Cloud Identity security groups (`hikari-*@hikaritech.co`), never individual user accounts. Daily engineering never uses the Workspace super-admin account.
- **Zero-Trust Network Posture**: Shared VPCs enforce Private Google Access (PGA) and a **default deny-all firewall posture** with per-subnet `compute.networkUser` isolation between `prd` and `nonprod` and between `app` and `platform` lanes.
- **Centralized 365-Day Audit Logging**: Organization-wide Admin Activity, Data Access, System Event, and Access Transparency logs sink to `hikari-logging` in `europe-west1` with 365-day retention.
