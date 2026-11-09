# InfraOps Lab

**Enterprise Infrastructure Monitoring & Automation — Phase 1: Monitoring Foundation**

InfraOps Lab is a local-first, reproducible infrastructure monitoring project designed to demonstrate practical DevOps foundations: containerized workloads, monitoring signals, incident detection, a read-only integration API, and a topology dashboard. The project emphasizes explaining system behavior and verifying it with logs, metrics, tests, and incident timelines rather than relying on screenshots alone.

## Scope and architecture

Phase 1 targets a Docker Compose lab with:

- **Monitoring engine:** Zabbix 7.0 LTS, backed by PostgreSQL.
- **Monitored workload:** a FastAPI demo service behind Nginx, monitored through a Zabbix HTTP web scenario.
- **Agent target:** an official Zabbix Agent 2 container using passive checks; its metrics describe the container target, not an independent Linux VM.
- **Integration API:** a separate FastAPI service using restricted, read-only Zabbix API credentials; topology inventory is declared in YAML.
- **Dashboard:** React, TypeScript, and React Flow, polling the integration API to visualize declared dependencies and health states.

Planned monitoring networking uses `monitoring_net` and `database_net`; PostgreSQL is not published to the host, and Zabbix Web is bound to loopback. Topology edges describe **declared dependencies**, not discovered physical connections. This phase does **not** include Kubernetes, cloud deployment, SNMP topology discovery, Terraform, or Ansible.

## Local prerequisites

- macOS on Apple Silicon (primary development target), with Docker Desktop / Docker Compose, Git and a terminal.
- A Windows/WSL2 machine is reserved for later fresh-deployment validation.
- Local secrets kept out of Git; see `.env.example` when environment variables are introduced.

## Repository structure

| Path | Purpose |
| --- | --- |
| `apps/api/` | Zabbix integration and topology API (future) |
| `apps/dashboard/` | React Flow operations dashboard (future) |
| `workloads/demo-service/` | Nginx and Demo API workload (future) |
| `infrastructure/compose/` | Docker Compose configuration (future) |
| `infrastructure/zabbix/` | Verified Zabbix configuration exports (future) |
| `infrastructure/topology/` | Declarative topology inventory (future) |
| `scripts/` | Smoke tests and fault injection (future) |
| `tests/` | Contract and integration tests (future) |
| `docs/decisions/` | Architecture Decision Records |
| `docs/learning/` | Learning journals and evidence references |
| `docs/runbooks/` | Operational procedures (future) |
| `docs/evidence/` | Sanitized verification artifacts (future) |

## Current status

- **Day 01:** Docker fundamentals and machine baseline verified.
- **Day 02:** repository structure and initial architecture decision prepared; Git/GitHub commit verification pending.
- **Deployment:** not yet implemented. The directory skeleton is **not** a runnable monitoring system.

## Development principles

1. Verify before marking work complete. Log commands, outputs and observed failures.
2. Never commit API tokens, passwords, environment secrets, database dumps, or unredacted logs.
3. Prefer small, reproducible changes and document architectural trade-offs.
4. Treat missing or stale telemetry as unknown/stale, never automatically healthy.
5. Keep Zabbix as the monitoring source of truth; UI is a consumer of normalized backend data.

## Learning plan

The Phase 1 roadmap contains **40 sessions across 8 weeks**, plus 6 optional buffer sessions, each capped at **120 minutes**. Individual sessions are approved only after evidence review. See the separately maintained `InfraOps_Lab_Master_Roadmap.html` for authoritative daily acceptance criteria and progress tracking.

## Getting started (Day 02 only)

The repository skeleton is safe to commit. No services are configured to run yet.

```bash
git status
git add .
git diff --cached --stat
git commit -m "chore: initialize InfraOps Lab"
```

The initial remote push should happen only after checking the staged paths for secrets. Read [ADR-0001](docs/decisions/0001-monitoring-engine.md) for the monitoring engine decision.

## Security note

`.gitignore` only helps with untracked files. If a secret has already been committed or pushed, removing it from a later commit does not make it safe; revoke or rotate the credential.
