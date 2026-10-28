# QA Result — synthetic demonstration

> **Important:** this is a synthetic report based entirely on synthetic evidence. It illustrates the form of evidence-first QA reporting and does not claim a defect in any real product.

**Feature:** synthetic CSV task import<br>
**Test plan:** [risk_based_test_plan.md](risk_based_test_plan.md)<br>
**Environment:** isolated synthetic demo<br>
**Overall verdict:** `Fail`

## Executive summary

The illustrative P0 scenario TS-005 shows a breach of the agreed atomicity rule: a file with one accepted and one rejected row persisted the accepted task. The synthetic brief requires `Confirm` to be unavailable whenever any row is rejected and prohibits all data changes.

Other scenarios in this package were not executed. That is accurately reported as `Not executed` rather than hidden behind an overall “success”.

## Plan coverage matrix

| Scenario | Status | Evidence | Result |
| --- | --- | --- | --- |
| TS-001 | Not executed | — | — |
| TS-002 | Not executed | — | — |
| TS-003 | Not executed | — | — |
| TS-004 | Not executed | — | — |
| TS-005 | Failed | [before](evidence/ts-005-before.json), [preview](evidence/ts-005-preview.json), [after](evidence/ts-005-after.json) | Partial write occurred. |
| TS-006 | Not executed | — | — |
| TS-007 | Not executed | — | — |
| TS-008 | Not executed | — | — |
| TS-009 | Not executed | — | — |
| TS-010 | Not executed | — | — |
| TS-011 | Not executed | — | — |
| TS-012 | Not executed | — | — |

## Defect draft

**Severity:** High<br>
**Title:** Mixed CSV partially persists tasks despite atomic import policy<br>
**Linked scenario:** TS-005 / BC-003

### Reproduction

1. Start from the [baseline snapshot](evidence/ts-005-before.json): only `TB-100` exists.
2. Upload [mixed_invalid_tasks.csv](../demo_case/fixtures/mixed_invalid_tasks.csv), represented by [preview evidence](evidence/ts-005-preview.json): `TB-200` is accepted and the update for `TB-100` is rejected.
3. Attempt Confirm.
4. Inspect the [after snapshot](evidence/ts-005-after.json).

### Expected result and source

`Confirm` is blocked when any row is rejected; no row from that file changes stored tasks. Source: [feature brief — Atomicity](../demo_case/feature_brief.md).

### Actual result

`TB-200` appears in the after snapshot although the preview contains a rejected row. The final set changed from one task to two.

### Scope and next action

In the synthetic case, this could create a partial import that the user did not approve. Keep release status `Fail` until the server enforces the atomicity rule and TS-005 is rerun with a clean snapshot.

## Residual risk

TS-001–TS-004 and TS-006–TS-012 remain unverified in this demonstration. The report deliberately makes that gap visible.
