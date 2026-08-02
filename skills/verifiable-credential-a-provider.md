---
name: Credential a healthcare provider
description: Create a provider, attach a license, and run primary-source verification with the Verifiable API.
api: openapi/verifiable-openapi-original.json
operations: [PasswordAuth, CreateProvider, ListSimplifiedLicenseTypes, AttachLicense, TriggerLicenseVerification, ListLicenseVerifications, GetLicenseVerification]
auth: bearer-token
base_url: https://discovery.verifiable.com/api
---

# Credential a healthcare provider

Use this skill to onboard a provider into Verifiable and run a primary-source
license verification. Build against staging (`https://discovery-staging.verifiable.com/api`)
first; move to production only once tested.

## Rules
- Authenticate with a Bearer access token in the `Authorization` header.
- Reads are `GET`; creates are `POST` and return `201` with a `Location` header
  pointing at the new resource. There is **no idempotency-key** contract, so do
  not blindly retry a `POST` that may have succeeded — check via a list/get first.
- List endpoints are cursor-paginated (`cursor` in, `nextCursor` out).
- Errors follow the RFC 7807 `ProblemDetails` shape (`type/title/status/detail`).

## Steps
1. **Authenticate** — `PasswordAuth` (`POST /auth/token/password`) with the user
   email + password to receive an access token. Send it as `Authorization: Bearer <token>`
   on every subsequent call. (Google Sign-In, OAuth client-credentials, and SSO
   are alternatives.)
2. **Create the provider** — `CreateProvider` (`POST /providers`) with the
   provider's identity fields. Capture the new provider id from the `Location`
   response header.
3. **Look up license types** — `ListSimplifiedLicenseTypes` (`GET /licensetypes`)
   to resolve the correct license-type code for the state/profession.
4. **Attach the license** — `AttachLicense` (`POST /providers/{providerId}/licenses`)
   with the license number and type.
5. **Verify against the primary source** — `TriggerLicenseVerification`
   (`POST /providers/{providerId}/licenses/{licenseId}/verify`) to start an
   asynchronous primary-source verification.
6. **Poll the result** — `ListLicenseVerifications`
   (`GET /providers/{providerId}/licenses/{licenseId}/verifications`) then
   `GetLicenseVerification` (`.../verifications/{verificationId}`) to read the
   verified status and any flagged problems.

## Tips
- Prefer registering a webhook (see the webhook skill) over polling, so you are
  notified when verification completes.
- Enrich the provider with additional info records (NPI, DEA, board
  certifications, education) under `/providers/{providerId}/info/*` before
  submitting for credentialing.
