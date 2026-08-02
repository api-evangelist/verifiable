---
name: Scan datasets for provider/facility matches
description: Run an asynchronous dataset scan for a provider and triage the resulting matches.
api: openapi/verifiable-openapi-original.json
operations: [ListDatasets, GetProviderDatasetScanParameters, StartDatasetScan, GetDatasetScan, ListDatasetMatches, PatchDatasetMatch]
auth: bearer-token
base_url: https://discovery.verifiable.com/api
---

# Scan datasets for provider/facility matches

Use this skill to screen a provider (or facility) against a monitored dataset
(e.g. sanctions/exclusions lists) and to review the matches produced.

## Rules
- Bearer access token in `Authorization` on every call.
- A dataset scan is **asynchronous**, though most scans complete near-immediately.
- List endpoints are cursor-paginated; errors use the RFC 7807 `ProblemDetails` shape.

## Steps
1. **Discover datasets** — `ListDatasets` (`GET /datasets`) to see the supported
   dataset types (the catalog grows over time).
2. **Resolve scan parameters** — `GetProviderDatasetScanParameters`
   (`GET /datasets/{datasetType}/parameters/providers/{providerId}`) to build the
   correct scan input for a given provider and dataset.
3. **Start the scan** — `StartDatasetScan` (`POST /datasets/scans`) with the
   dataset type and parameters. Capture the returned scan id.
4. **Check status** — `GetDatasetScan` (`GET /datasets/scans/{scanId}`) until the
   scan reports complete.
5. **Read matches** — `ListDatasetMatches` (`GET /datasets/matches`) filtered to
   the scan to review potential hits.
6. **Triage** — `PatchDatasetMatch` (`PATCH /datasets/matches/{matchId}`) to
   confirm, dismiss, or annotate a match.

## Tips
- Use `RefreshDatasetScan` (`POST /datasets/scans/{scanId}/refresh`) to re-run a
  prior scan, and `GetDatasetMatchesAggregations` for match counts across a roster.
