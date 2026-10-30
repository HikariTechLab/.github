# Support & Issue Routing

Need help working with a **HikariTechLab** project or infrastructure layer? Use the routing guide below so your request reaches the right repository and workflow.

---

## Where to Open Issues

| Topic / System | Target Repository | What to Include |
| :--- | :--- | :--- |
| **Bansho Engine, CLI (`./bansho`), or Control Tower UI** | [`HikariTechLab/bansho`](https://github.com/HikariTechLab/bansho/issues) | Ticket ID, selected model, `skill.yaml` scope, and `.bansho/telemetry.jsonl` / Four Findings output. |
| **GCP IaC FinOps & BigQuery SQL / Slot Optimization** | [`HikariTechLab/greyhound-gcp`](https://github.com/HikariTechLab/greyhound-gcp/issues) | Subcommand (`breakdown`, `diff`, `sql`, `slots`), sanitized `.tf` / `.sql` snippet, and `--dry-run` / `--verify` output. |
| **Temporal Unattended Coding Control Plane** | [`HikariTechLab/agentic-harness`](https://github.com/HikariTechLab/agentic-harness/issues) | Workflow ID, phase, gate route (`fast_track` vs `senior_review`), and policy rule. |
| **GCP Organization Foundation & Client Stacks** | [`HikariTechLab/hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra/issues) | Target layer (`bootstrap`, `apps/<app>/<env>`, or `clients/<client>/<env>`) and `make plan` summary. |
| **Remote Opportunity Discovery & Scoring Agent** | [`HikariTechLab/job-search-agent`](https://github.com/HikariTechLab/job-search-agent/issues) | Search criteria JSON, source connector (`JSearch`, `Greenhouse`, `Lever`), and scoring rubric output. |

---

## Before Opening an Issue

1. **Check the Repository Documentation**:
   - Read the repository's `README.md`, `docs/ARCHITECTURE.md`, and `AGENTS.md` / `GEMINI.md` / `skills/<domain>/SKILL.md`.
2. **Run Local Diagnostics**:
   - For Python/ADK projects: run `uv run pytest tests/unit` and `uv run ruff check`.
   - For Bansho CLI: run `./bansho status` to verify target repo discovery and model API credentials.
   - For GCP Terraform (`hikari-org-infra`): verify your ADC identity and quota project (`gcloud auth application-default set-quota-project <cs-host-project-id>`).
3. **Security & Credentials**:
   - Never paste raw API keys, service account tokens, `.env` contents, or unredacted customer SQL literals into issues. For security vulnerabilities, follow [`SECURITY.md`](SECURITY.md).
