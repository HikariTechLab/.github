<div align="center">
  <img src="./assets/hikari-banner.svg" alt="HikariTechLab — Deterministic Agentic Engineering, Autonomous In-VPC FinOps & Zero-Trust GCP Cloud Foundations" width="100%" />
</div>

<br />

<div align="center">

[![Organization](https://img.shields.io/badge/Organization-HikariTechLab-0F172A?style=flat-square&logo=github&logoColor=38BDF8)](https://github.com/HikariTechLab)
[![Domain](https://img.shields.io/badge/Domain-hikaritech.co-0F172A?style=flat-square&logo=googlecloud&logoColor=34D399)](https://github.com/HikariTechLab/hikari-org-infra)
[![Agent Platform](https://img.shields.io/badge/Google_Cloud-ADK_2.0_%26_Vertex_AI-0F172A?style=flat-square&logo=googlecloud&logoColor=818CF8)](https://github.com/HikariTechLab/bansho)
[![Infrastructure](https://img.shields.io/badge/IaC-Terraform_PR--Only-0F172A?style=flat-square&logo=terraform&logoColor=C084FC)](https://github.com/HikariTechLab/hikari-org-infra)

</div>

---

## Who We Are

**HikariTechLab (光テックラボ)** is an AI systems and cloud platform engineering organization focused on **Deterministic Agentic Engineering**, **Autonomous In-VPC FinOps**, and **Zero-Trust Google Cloud Foundations**.

We build systems around a single architectural conviction:

> **Probabilistic models belong inside deterministic engineering harnesses.**
>
> Giving an unconstrained LLM write access to a codebase or cloud environment invites four systemic failure modes: *ambiguous specifications*, *reward hacking (Goodhart's Law)*, *unbounded blast radius*, and *irreversible side effects*. At **HikariTechLab**, deterministic code—not an LLM—owns the state machine, locks behavioral contracts with SHA-256 digests, enforces AST and path-level guardrails, verifies candidates against blind holdout sentinels, and mints atomic rollback tokens before a human ever promotes a change.

---

## Flagship Systems & Repositories

| Repository | Domain | Architecture & Key Guarantees | Stack |
| :--- | :--- | :--- | :--- |
| **[`bansho`](https://github.com/HikariTechLab/bansho)**<br>*(番所 — The Watchtower)* | **Spec-Driven Agentic Coding Engine** | 3-Loop Self-Improving Flywheel (`bansho-spec` → `bansho-worker` → `bansho-evolve` / AEOS 3), 3-Tier Verification Pyramid (Unit + SHA-256 Locked Gherkin + Blind `@sentinel` suite), Multi-Model FinOps Router, and stateless Compare-and-Swap (CAS) Undo tokens. | Python, Google ADK 2.0, FastAPI, TypeScript CLI, React 19, OpenTelemetry |
| **[`greyhound-gcp`](https://github.com/HikariTechLab/greyhound-gcp)** | **GCP IaC & BigQuery FinOps CLI** | Fast, offline-first Rust FinOps engine for Terraform, Terragrunt & `plan.json` (automatic SUDs, Spot, live Cloud Monitoring P95 rightsizing) and BigQuery workloads (SQL/SQLX AST scanner & rewriter, `$0` `dryRun` byte proofs, `EXCEPT DISTINCT` equivalence SQL, logical vs. physical storage compression audit, and Edition slot autoscaling simulator). | Rust, Terraform / HCL, BigQuery, Cloud Monitoring, Docker |
| **[`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra)** | **Zero-Trust GCP Organization Foundation** | Declarative Terraform source of truth for `hikaritech.co`. Layered GCS state (`bootstrap/`, `apps/`, `clients/`), Shared VPC per-subnet isolation (`europe-west1`), default-deny firewall posture, 365-day org audit sink, and strict multi-tenant client isolation. | Terraform, Google Cloud Platform, Cloud Identity, BigQuery Billing |
| **[`agentic-harness`](https://github.com/HikariTechLab/agentic-harness)** | **Durable Unattended Coding Control Plane** | Orchestrates unattended issue-to-PR coding runs inside ephemeral Docker sandboxes using Temporal workflows, calibrated decision-model diff gating (`fast_track` vs `senior_review`), and durable human-in-the-loop merge gates. | Python 3.13, Temporal, Docker, OpenRouter / Jev, Aider |
| **[`job-search-agent`](https://github.com/HikariTechLab/job-search-agent)** | **Autonomous Opportunity Intelligence Agent** | Discovers, deduplicates, sanitizes (`<untrusted_job_description>`), and scores worldwide remote Cloud/SRE/Platform/AI Infrastructure roles on a `0–100` rubric into BigQuery & Looker Studio with hard API quota guardrails. | Python, Google ADK, Vertex AI (`gemini-3.8-flash`), BigQuery |

---

## Our Engineering Architecture & Invariants

```mermaid
flowchart LR
    subgraph Spec ["1. Behavioral Contract"]
        Gherkin["Gherkin Spec (@scope)\nSHA-256 Digest Lock"]
    end

    subgraph Harness ["2. Deterministic Harness & Guardrails"]
        Sandbox["Hermetic Git / Docker Sandbox\nbefore_tool_callback & AST Guards"]
        Pyramid["3-Tier Verification Pyramid\nUnit • Locked Spec • Blind @sentinel"]
    end

    subgraph Memory ["3. Institutional Memory & Governance"]
        AEOS["Loop 3 Auto Evolution\nskills/<domain>/SKILL.md"]
        HITL["Human Control Tower Gate\nFour Findings + CAS Undo Token"]
    end

    Gherkin --> Sandbox --> Pyramid --> AEOS --> HITL
```

### 1. The 3-Tier Verification Pyramid & Sentinel Blindness
We never trust an agent to grade its own homework. Repositories managed by our harnesses separate verification into three distinct tiers:
- **Tier 1 — Component & Unit (`test_scope`)**: Fast deterministic unit tests, static typing, and syntax validation (`pytest`, `vitest`, `cargo test`, `terraform validate`).
- **Tier 2 — Locked Behavioral Spec (`tests/specs/**`)**: Formal `Given / When / Then` Gherkin specifications compiled and locked with a **SHA-256 digest** before the worker agent runs.
- **Tier 3 — Blind Holdout Sentinels (`sentinels/**`)**: Out-of-sample security, billing, SLA, and architectural invariants. Our tool guardrails (`before_tool_callback`) physically block agents from reading `sentinels/**` and redact stack traces on failure—defeating Goodhart's Law by construction.

### 2. Zero-Mutation & Anti-Prompt-Injection Defense
Every FinOps engine and autonomous agent handling external telemetry or database queries runs behind deterministic security guardrails:
- **Read-Only HTTP & SQL AST Whitelisting**: In [`greyhound-gcp`](https://github.com/HikariTechLab/greyhound-gcp), static IaC and SQL/SQLX scans run 100% offline with zero credentials, while live telemetry and `$0` BigQuery dry-run proofs are enforced by a runtime HTTP mutation guard (`GcpAuthClient::ensure_readonly_request`), read-only OAuth scopes, and strict SQL AST validation.
- **Untrusted Input Delimiting & Session-Poisoning Handshakes**: External inputs (job descriptions, query logs, test outputs) are sanitized, wrapped in explicit XML boundary tags, and checked by `on_user_message_callback` → `before_run_callback` guards before entering model context.

### 3. Self-Improving Domain Memory (AEOS 3) & Stateless Rollback
- **Evolvable `SKILL.md` Cartridges**: When a candidate passes verification, Loop 3 (`bansho-evolve`) updates *only* `skills/<domain>/SKILL.md` on a dedicated `skill/<ticket_id>` branch—encoding repository-specific lessons so future tasks succeed on the first attempt.
- **Compare-and-Swap (CAS) Undo**: Every agentic execution mints a stateless `CASUndoToken` (`before_version` → `after_version`) alongside **The Four Findings** (`Changed`, `Improved`, `Degraded`, `Recommended Action`), enabling 1-click atomic rollback if `HEAD == after_version`.

### 4. Declarative, PR-Only Cloud Infrastructure
In [`hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra), the repository is the sole source of truth for the `hikaritech.co` GCP organization:
- **Never fix the org in the console**: Drift dies in Git or lives forever. Rollback is `git revert` + `make plan` + `make apply`.
- **Layered Blast-Radius Isolation**: Layer 0 (`bootstrap/`) governs org folders, Shared VPCs, and centralized 365-day audit logging; Layer 1 (`apps/<app>/<env>` and `clients/<client>/<env>`) isolates every application and client tenant into its own remote state prefix, dedicated service accounts, budgets, and mandatory cost-attribution labels.

---

## Technology Stack

| Layer | Technologies & Standards |
| :--- | :--- |
| **Agentic & AI Frameworks** | Google Agent Development Kit (**ADK 2.0**), Vertex AI (`gemini-3.8-flash`, `gemini-2.5-pro`), LiteLLM (Claude Sonnet 4, GPT-4.1, OpenRouter), Temporal Workflows |
| **Cloud & Infrastructure** | Google Cloud Platform (`europe-west1`), Terraform / Terragrunt (Layered GCS State), Shared VPC, Cloud Run, Vertex AI Agent Engine, Secret Manager, Cloud Identity |
| **Data, FinOps & Observability** | BigQuery (`INFORMATION_SCHEMA`, `$0` Dry-Run & Equivalence Proofs, Edition Slot Simulation), Cloud Monitoring, Looker Studio, OpenTelemetry, JSONL FinOps Ledgers |
| **Languages & Tooling** | Rust 1.85+ (`cargo`, `clippy`, static `musl` binaries), Python 3.12 / 3.13 (`uv`, `ruff`, `pyright`, `pytest`, `pydantic`), TypeScript (`npm`, Node.js, `@clack/prompts`, `biome`, `vitest`), React 19, Tailwind CSS |

---

<div align="center">
  <sub>Built with discipline by <strong>HikariTechLab (hikaritech.co)</strong> • <a href="https://github.com/HikariTechLab/.github/blob/main/CONTRIBUTING.md">Contributing</a> • <a href="https://github.com/HikariTechLab/.github/blob/main/SECURITY.md">Security Policy</a></sub>
</div>
