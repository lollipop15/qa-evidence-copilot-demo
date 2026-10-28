# Risk-Based Test Plan — synthetic CSV task import

**Context:** [qa_context.md](qa_context.md)<br>
**Environment:** isolated synthetic data; fixed UTC date `2026-10-28`<br>
**Safety:** no production system, personal data, or external calls.

## Controlled data

- Baseline task: `TB-100, Before import, medium, 2026-11-03`.
- Valid [valid_tasks.csv](../demo_case/fixtures/valid_tasks.csv) updates `TB-100` and creates `TB-200` and `TB-300`.
- Invalid inputs: blank `task_id`, blank `title`, `priority=urgent`, a `D+366` date, and duplicate normalized `task_id`.
- Independent test oracle: prepared task snapshots and parsed export output, not the response from the UI/API being tested.

## Scenarios

| Scenario | Contract | Class / risk | Action | Expected result + source | Evidence / cleanup |
| --- | --- | --- | --- | --- | --- |
| TS-001 | BC-001 | Positive | Upload a valid CSV; do not confirm it. | All rows are accepted; tasks remain unchanged. Brief. | Preview and before/after snapshot; cleanup N/A. |
| TS-002 | BC-002 | Malformed | Empty file, header-only file, missing or extra header. | Safe error and zero data changes. Brief. | Error and snapshot. |
| TS-003 | BC-002 | Negative | Blank or whitespace-only required fields, unsupported priority, invalid calendar date. | Every row is rejected with a reason; Confirm is unavailable. Brief. | Preview, disabled Confirm, snapshot. |
| TS-004 | BC-002 | Boundary | `D`, `D+365`, `D+366` in UTC. | The first two dates are accepted; `D+366` is rejected. Brief. | Fixed date and preview. |
| TS-005 | BC-003 | Atomicity / P0 | Upload [mixed_invalid_tasks.csv](../demo_case/fixtures/mixed_invalid_tasks.csv) and attempt Confirm. | Confirm is unavailable; semantic task records (`task_id`, `title`, `priority`, `due_date`), sorted by ID and excluding capture metadata, equal the baseline. Brief. | Full before/after snapshot. |
| TS-006 | BC-002, BC-003 | Duplicate | Two rows with `tb-200` and `TB-200`. | The file is invalid; no row changes data. Brief. | Preview and snapshot. |
| TS-007 | BC-004, BC-005 | Create/update/idempotency | Confirm a valid file; re-upload the same file, obtain a fresh preview, and confirm it. | New IDs are created and `TB-100` is updated; after the second fresh preview is confirmed, the task set is identical to the first. Brief. | Two snapshots and export. Restore baseline. |
| TS-008 | BC-006 | Used preview replay | After a successful Confirm, repeat the same Confirm with the same checksum. | Safe rejection `Preview expired; upload again`; no task is newly created or updated. Brief. | Response and snapshot. Restore baseline. |
| TS-009 | BC-006 | Stale state | Create a preview, change the source state, then confirm the old preview. | Safe rejection `Preview expired; upload again`; no change from the old preview. Brief. | Checksum/result and snapshot. Restore baseline. |
| TS-010 | BC-007 | Authorization | A non-manager attempts Confirm through the UI and directly. | The server rejects the operation and does not change tasks. Brief. | UI, safe response, and snapshot. |
| TS-011 | BC-008 | Cross-layer parity | Download Export after TS-007. | Exactly four columns in the required order; normalized IDs, current values, ID sorting; Export is read-only. Brief. | Parsed export versus expected table. |
| TS-012 | BC-009 | Localization | Repeat TS-003, TS-010, and TS-011 in EN and RU. | All visible statuses and errors are localized; final data is independent of language. Brief. | EN/RU evidence and snapshots. |

## Coverage matrix

| Area | Scenarios | Plan status |
| --- | --- | --- |
| Happy path | TS-001, TS-007, TS-011 | Planned |
| Negative / malformed inputs | TS-002, TS-003 | Planned |
| Boundaries | TS-004 | Planned |
| Duplicates / idempotency | TS-006, TS-007, TS-008 | Planned |
| Integrity / rollback | TS-005, TS-008, TS-009 | Planned |
| Authorization | TS-010 | Planned |
| Export parity | TS-011 | Planned |
| Localization | TS-012 | Planned |

## Release gates

- No `Pass` until P0 scenarios TS-005 and TS-007–TS-011 have evidence.
- Any data mutation after an invalid, expired, or unauthorised Confirm is a release blocker.
- Any difference between the expected table and Export is a `Fail` until an agreed exception is proven.
