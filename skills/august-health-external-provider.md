---
name: Retrieve clinical data and write visit notes as an external provider
description: As an external provider group, match patients to residents, pull clinical and demographic data on demand, and write visit notes back to August Health.
api: developer.augusthealth.com
operations: [createTokenV1, listCensusV1, getLatestAssessmentV1, listPersonMedicationOrdersV1, createIncidentV1]
---

# External provider integration with August Health

Grounded in the [External Provider Integration guide](https://developer.augusthealth.com/docs/external-provider-integration.md).

## Auth
1. `createTokenV1` — obtain the `idToken`; send `Authorization: Bearer {idToken}`.

## Match and read on demand
2. `listCensusV1` — match your patient to an August Health resident (`personId`) using limited demographics.
3. `getLatestAssessmentV1` — most recent finalized care assessment for the resident.
4. `listPersonMedicationOrdersV1` — active medication orders for the resident.

## Write back
5. `createIncidentV1` — record a visit note / clinical note against the resident. Read it back with `getIncidentV1`; amend with `updateIncidentV1`.

## Rules
- Access requires the mutual customer to authorize data sharing during onboarding.
- 10 req/s ceiling; back off on `429`.
- Prefer webhook events (`person.assessment_finalized`, `person.level_of_care_changed`) to know when new clinical data is available.
