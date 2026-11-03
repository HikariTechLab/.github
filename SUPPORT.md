# Support & Repository Routing

Need help working with a **HikariTech (`hikaritech.co`)** platform layer, microservice, or AI agent system? Use the routing matrix below so your issue or Pull Request reaches the right repository.

---

## Where to Open Issues & Pull Requests

| Platform Layer / System | Target Repository | What to Include |
| :--- | :--- | :--- |
| **L0 — GCP Organization Foundation & Client Stacks** | [`HikariTechLab/hikari-org-infra`](https://github.com/HikariTechLab/hikari-org-infra/issues) | Target state layer (`bootstrap/`, `platform/<env>/`, `apps/<app>/<env>/`, or `clients/<client>/<env>/`) and `terraform plan` output. |
| **L1 — IDP Control Plane (Argo CD, Crossplane, Sveltos, Kyverno)** | [`HikariTechLab/hikari-platform`](https://github.com/HikariTechLab/hikari-platform/issues) | Affected Argo CD `Application`, Crossplane `Composition`/`XRD`, or Sveltos `ClusterProfile` / Kyverno `ClusterPolicy`. |
| **L2 — Self-Service Claims & Tenant Workloads** | [`HikariTechLab/hikari-workloads`](https://github.com/HikariTechLab/hikari-workloads/issues) | Claim manifest (`claims/<env>/*`), Kustomize cluster overlay (`deploy/clusters/*`), or service name (`services/*`). |
| **Multi-Agent Codebase Orchestration (`cerebro`)** | [`HikariTechLab/cerebro`](https://github.com/HikariTechLab/cerebro/issues) | Orchestrator run ID, specialist agent role (`frontend`, `backend`, `security`, `quality`, `tester`, `ops`), and reproduction steps. |
| **Durable Unattended Coding Control Plane** | [`HikariTechLab/agentic-harness`](https://github.com/HikariTechLab/agentic-harness/issues) | Temporal workflow ID, sandbox execution phase, and verification gate output. |
| **Opportunity Intelligence Agent (ADK & BigQuery)** | [`HikariTechLab/job-search-agent`](https://github.com/HikariTechLab/job-search-agent/issues) | Connector name, search configuration, and Vertex AI / BigQuery job telemetry. |

---

## Pre-Flight Checklist Before Opening an Issue

1. **Check the Repository Architecture Docs**:
   - Review the target repository's `README.md` and `docs/` directory (including [`docs/PARTNER_WORKSHOP_BLUEPRINT.md`](https://github.com/HikariTechLab/hikari-platform/blob/main/docs/PARTNER_WORKSHOP_BLUEPRINT.md) in `hikari-platform` for end-to-end IDP operations).
2. **Run Local Validation**:
   - **Terraform (`L0`)**: `terraform fmt -check -recursive && terraform validate`
   - **GitOps & Kustomize (`L1` / `L2`)**: `kubectl kustomize <overlay-path>`
   - **Services & Agents**: `npm test` or `uv run pytest`
3. **Zero Secrets in Issues**:
   - Never paste raw tokens, `.env` contents, or unredacted customer data into issues. For security disclosures, follow [`SECURITY.md`](./SECURITY.md).
