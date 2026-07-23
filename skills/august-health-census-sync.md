---
name: Keep a community's resident roster in sync
description: Authenticate, discover the facilities you have access to, pull the census, and reconcile resident roster changes into your system.
api: developer.augusthealth.com
operations: [createTokenV1, listOrganizationsV1, listFacilitiesV1, listCensusV1, listPeopleV1, getPersonV1, getAdmissionHistoryV1]
---

# Census sync with August Health

Grounded in the [Census Integration guide](https://developer.augusthealth.com/docs/census-integration.md).

## Auth
1. `createTokenV1` — `POST /v1/token` with `{ "credentials": { "username", "password" } }`. Store the `idToken` and `refreshToken`.
2. Send `Authorization: Bearer {idToken}` on every request. Refresh at 80-90% of `expiresIn`; refresh tokens live 24 hours.

## Discover scope
3. `listOrganizationsV1` — organizations your API user can see.
4. `listFacilitiesV1` — facilities within each organization.

## Pull and reconcile
5. `listCensusV1` — limited demographics for everyone in a facility (the roster). Use `getCensusPersonV1` or `getPersonV1` for detail.
6. `getAdmissionHistoryV1` — resolve admits/discharges/transfers for a resident.
7. For ongoing sync, subscribe to webhooks (`person.admission_created`, `person.discharge_created`, `person.room_transfer`, `person.return`, `person.away`) rather than polling.

## Rules
- Rate limit: 10 req/s across your whole integration. Pace to ~6-7 rps; on `429` back off exponentially 1s to 16s, max 5 retries.
- Prefer webhook-driven updates + `updatedAfter` incremental pulls over full re-scans.
