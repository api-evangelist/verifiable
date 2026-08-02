---
name: Continuously monitor a provider for sanctions and exclusions
description: Set up continuous sanctions-and-exclusions monitoring and triage the alerts it produces.
api: openapi/verifiable-openapi-original.json
operations: [CreateProvider, BulkCreateSanctionsAndExclusionsMonitors, CreateMonitor, ListMonitors, ListAlerts, GetAlert, DismissAlert, ListProviderMonitoringSanctionsAndExclusionsSummary]
auth: bearer-token
base_url: https://discovery.verifiable.com/api
---

# Continuously monitor a provider for sanctions and exclusions

Use this skill to place providers under continuous monitoring and to work the
alerts that monitoring generates.

## Rules
- Bearer access token in `Authorization` on every call.
- Monitors run continuously after creation; alerts are surfaced as they are found.
- List endpoints are cursor-paginated; errors use the RFC 7807 `ProblemDetails` shape.

## Steps
1. **Have providers on file** — create providers with `CreateProvider`
   (`POST /providers`) or reference existing provider ids.
2. **Create monitors** — for a batch, use `BulkCreateSanctionsAndExclusionsMonitors`
   (`POST /monitors/bulk/sanctions-and-exclusions`); for a single monitor use
   `CreateMonitor` (`POST /monitors`).
3. **Confirm monitors** — `ListMonitors` (`GET /monitors`) to verify what is
   active.
4. **Read the roll-up** — `ListProviderMonitoringSanctionsAndExclusionsSummary`
   (`GET /providers/monitoring/sanctions-and-exclusions`) for a provider-level
   summary of current sanctions/exclusions status.
5. **Work the alerts** — `ListAlerts` (`GET /alerts`) to pull open alerts, then
   `GetAlert` (`GET /alerts/{alertId}`) for detail.
6. **Resolve** — `DismissAlert` (`POST /alerts/{alertId}/dismiss`) once an alert
   has been reviewed and actioned.

## Tips
- Register a webhook so new alerts are pushed to you instead of polling.
- Use `GetAlertAggregations` (`GET /alerts/aggregations`) for counts across the
  roster.
