# QA Context Brief — synthetic CSV task import

**Feature:** [feature_brief.md](../demo_case/feature_brief.md)<br>
**Sources used:** agreed synthetic brief<br>
**Status:** `Ready for plan`

## Behavior contract ledger

| Contract | Atomic expected behavior | Surfaces | Independent test oracle |
| --- | --- | --- | --- |
| BC-001 | Preview classifies every row and does not change stored tasks. | UI, API, data | Task snapshot before/after preview |
| BC-002 | Only a strictly valid CSV may proceed to Confirm. | Parser, UI, API | Controlled CSV dataset and rejected reasons |
| BC-003 | An invalid row blocks the entire Confirm; no row from the file changes data. | UI, API, data | Full before/after snapshot |
| BC-004 | A new task is created and an existing one is updated by normalized `task_id`. | API, data, UI | Expected task table by ID |
| BC-005 | Re-uploading a valid file with a fresh preview is idempotent. | API, data, Export | Snapshot comparison after two fresh Confirms |
| BC-006 | A used or stale preview cannot persist data. | UI, API, data | Snapshot, checksum, and safe rejection |
| BC-007 | A non-manager cannot confirm an import, including by bypassing the UI. | UI, API, data | Operation response and unchanged snapshot |
| BC-008 | Export matches current stored tasks and has fixed columns and sorting. | Export, data | Independent CSV parser and snapshot |
| BC-009 | Workflow and messages work in EN/RU without changing data. | UI, localization | Screenshots/accessible text and snapshots |

## State and side-effect map

| Operation | Tasks | Preview | Export |
| --- | --- | --- | --- |
| Upload / Preview | `must remain unchanged` | `must change` | `must remain unchanged` |
| Confirm valid file as manager | `must change` | becomes consumed | `must change` |
| Confirm invalid or expired preview | `must remain unchanged` | shows an explanation | `must remain unchanged` |
| Confirm as non-manager | `must remain unchanged` | shows rejection | `must remain unchanged` |
| Export | `must remain unchanged` | `not applicable` | is generated from the current snapshot |

## Risks and readiness

| Risk | Level | Verification |
| --- | --- | --- |
| Partial persistence of an invalid file | P0 | TS-005: full before/after snapshot |
| Duplicates, re-upload, used-preview replay, or stale preview | P0 | TS-006–TS-009: snapshot and identity checks |
| Bypass of the manager role | P0 | TS-010: UI and direct request |
| Preview, data, and export are out of sync | P0 | TS-001, TS-011: row reconciliation |
| Date boundary defect | P1 | TS-004: UTC D, D+365, D+366 |
| Incomplete localization | P1 | TS-012: EN/RU workflow |

There are no material open questions: the synthetic brief defines atomicity, normalisation, export, and preview lifecycle.
