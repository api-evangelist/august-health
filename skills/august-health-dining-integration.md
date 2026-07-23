---
name: Build a dining / point-of-sale integration
description: Authenticate, onboard a community, and retrieve resident dietary information and photos to power a dining or POS system.
api: developer.augusthealth.com
operations: [createTokenV1, listCensusV1, getCensusPersonV1, getPersonProfilePhotoV1]
---

# Dining / POS integration with August Health

Grounded in the [Dining Integration guide](https://developer.augusthealth.com/docs/dining-integration.md).

## Auth
1. `createTokenV1` — obtain the `idToken`; send it as `Authorization: Bearer {idToken}`.

## Build the dining roster
2. `listCensusV1` — residents in the facility with limited demographics and dietary information.
3. `getCensusPersonV1` — detail for a single resident.
4. `getPersonProfilePhotoV1` — 168x168 profile photo for POS display. Cache with `If-None-Match` (ETag) to avoid refetching unchanged images.

## Keep in sync
5. Subscribe to `person.diet_changed` webhooks to update dietary flags immediately; use `person.admission_created` / `person.discharge_created` to add/remove diners.

## Rules
- 10 req/s ceiling; pace and back off on `429`.
- Photos are binary with ETag caching — always send `If-None-Match`.
