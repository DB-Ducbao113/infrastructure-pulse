# ADR-0001 — Monitoring Engine for Phase 1

- **Status:** Accepted for Phase 1 (roadmap decision)
- **Decision date:** 2026-10-25 (roadmap baseline)
- **Scope:** Local Docker Compose monitoring foundation

## Context

InfraOps Lab needs to monitor an HTTP workload and a dedicated agent container, observe samples and trigger conditions, investigate incidents, and expose normalized host/item/problem information to a separate read-only API. The first phase is an educational, local-first lab rather than a general-purpose, production monitoring platform. The system must make it practical to distinguish a collected value, an alert condition, an active problem, and missing telemetry. The later React Flow topology must reflect real monitoring data without claiming physical topology discovery.

## Decision

Use **Zabbix 7.0 LTS** as the Phase 1 monitoring engine, with **PostgreSQL** for Zabbix persistence. Use a **Zabbix Web Scenario** for the Demo API health route and an **official Zabbix Agent 2 container** for passive agent checks. Keep Zabbix as the source of truth for monitoring states. A separate FastAPI service will read Zabbix API data using a least-privilege account; it will apply explicit mapping and freshness policies and serve a frontend-safe topology contract.

## Alternatives considered

### Zabbix

- Provides a host/item/trigger/problem model suited to the course's learning outcomes.
- Offers built-in web scenarios, agent-based checks and an API for host, item and problem queries.
- Provides UI workflows for investigating values, trigger expressions, incident events and recoveries.

**Trade-offs:** the server, web interface and database add operational components. Trigger expressions, API permissions and state mapping require deliberate configuration. Zabbix is not automatic physical topology discovery.

### Prometheus

- Strong fit for labeled, time-series metrics, especially instrumented services and exporters.
- PromQL enables flexible queries and alerting; a typical deployment pairs Prometheus with Alertmanager and optionally Grafana.
- Attractive for later cloud-native or Kubernetes-focused observability work.

**Trade-offs for this lab:** moving from HTTP probes and agent-style checks to an equivalent host/problem view would require selecting additional exporters, probing components, alert integrations and inventory conventions. This would shift time from the Phase 1 learning objectives toward assembling a metrics-centered monitoring stack.

Prometheus is not inherently inferior; it fits a different primary learning emphasis and remains a possible Phase 2+ research topic.

## Consequences

### Benefits

1. A consistent workflow: **Host → Item → Trigger → Problem/Event → Recovery**.
2. Web scenarios and passive Agent 2 checks can be demonstrated independently.
3. A clear path to test missing-data handling, incident latency and API-to-UI propagation.
4. A stable source for topology status while UI edges remain declarative.

### Costs and limitations

1. Need to maintain multiple Compose services and persistent PostgreSQL storage.
2. Need to restrict and protect Zabbix API credentials; do not expose tokens to React.
3. An Agent 2 container does **not** represent a separate Linux VM or physical machine.
4. A successful HTTP status is not enough by itself to prove all dependent systems are healthy.
5. The policy for stale data (`lastclock` thresholds) must be documented and tested separately from severity.

## Validation plan

This decision is validated through later roadmap sessions, not by writing this ADR alone:

- Successful Zabbix server/web/PostgreSQL startup and persistence tests.
- HTTP scenario returns repeated samples at the configured 30-second interval.
- A trigger detects three failed samples and subsequently recovers.
- Passive Agent 2 telemetry is collected; missing telemetry is independently detectable.
- FastAPI reads Zabbix data through restricted API credentials; write access is denied.
- The dashboard reflects a real fault and recovery, with measured end-to-end times.

## Revisit conditions

Reassess the monitoring engine if the scope moves to Kubernetes-native metrics, large-scale application instrumentation, or a substantially different operational model. Do not change tools merely because another stack is popular.

## References

- Zabbix 7.0 manual: https://www.zabbix.com/documentation/7.0/en/manual
- Zabbix API: https://www.zabbix.com/documentation/7.0/en/manual/api
- Prometheus overview: https://prometheus.io/docs/introduction/overview/
