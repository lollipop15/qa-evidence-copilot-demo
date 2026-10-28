# Synthetic feature: CSV task import

## Goal

A manager quickly uploads a task list to a fictional Task Board, reviews the preview, confirms the import, and downloads the current export.

## User flow

1. A user with the `manager` role selects a UTF-8 CSV with the headers `task_id,title,priority,due_date`.
2. The system shows a preview for every row: `accepted` or `rejected`, with a clear reason.
3. Stored tasks do not change until the user selects `Confirm`.
4. After a successful `Confirm`, both the task list and `Export` reflect the new state.

## Agreed business rules

| Rule | Expected behavior |
| --- | --- |
| File format | Exactly four headers: `task_id`, `title`, `priority`, `due_date`. An empty file or a file with a missing or extra header is rejected. |
| ID and title | `task_id` and `title` are required; leading and trailing whitespace is trimmed. A value blank after trimming is invalid. |
| Priority | The field is required and accepts only `low`, `medium`, or `high`. |
| Due date | The field is required and uses `YYYY-MM-DD`; the allowed range is from the current UTC date through `UTC today + 365` inclusive. |
| ID normalization | `task_id` is normalized to uppercase. Two identical normalized IDs in one file invalidate the entire file. |
| Atomicity | If preview contains at least one `rejected` row, `Confirm` is unavailable. No row from that file is created or updated. |
| Create and update | A valid row with a new `task_id` creates a task; a row with an existing ID replaces `title`, `priority`, and `due_date`. |
| Re-upload | Re-uploading an identical valid file creates a fresh preview. Confirming that fresh preview persists the same final task set without duplicates. |
| Preview lifecycle | A successful `Confirm` consumes the preview. Replaying the same confirmation, confirming a stale preview, or confirming after the source state changed is rejected with `Preview expired; upload again` and must not mutate data. |
| Authorization | Only a `manager` can confirm an import. The server rejects another role’s attempt without changing data. |
| Export | A UTF-8 CSV with four columns in `task_id,title,priority,due_date` order; rows are sorted by `task_id`; export does not mutate data. |
| Localization | All visible labels, validation reasons, and authorization errors are available in English and Russian. The CSV data format is independent of the UI language. |

## Out of scope

Collaborative task editing, bulk deletion, file-size limits, performance SLAs, and integrations with external services.

## Synthetic input files

- [valid_tasks.csv](fixtures/valid_tasks.csv) — valid import.
- [mixed_invalid_tasks.csv](fixtures/mixed_invalid_tasks.csv) — demonstrates the atomic `Confirm` block.
