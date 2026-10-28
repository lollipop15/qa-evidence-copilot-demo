# Risk-Based Test Plan — учебный CSV-импорт задач

**Context:** [qa_context.md](qa_context.md)<br>
**Environment:** изолированные synthetic data; фиксированная UTC-дата `2026-10-28`<br>
**Safety:** никакой production-системы, персональных данных или внешних вызовов.

## Контролируемые данные

- Исходная задача: `TB-100, Before import, medium, 2026-11-03`.
- Валидный [valid_tasks.csv](../demo_case/fixtures/valid_tasks.csv) обновляет `TB-100` и создаёт `TB-200`, `TB-300`.
- Невалидные строки: пустой `task_id`, пустой `title`, `priority=urgent`, дата `D+366`, дублированный нормализованный `task_id`.
- Независимый oracle: заранее подготовленные снимки задач и результат разбора export, а не ответ того же UI/API, который проверяется.

## Сценарии

| Scenario | Контракт | Класс / риск | Действие | Ожидаемый результат + источник | Evidence / cleanup |
| --- | --- | --- | --- | --- | --- |
| TS-001 | BC-001 | Positive | Загрузить валидный CSV, не нажимать Confirm. | Все строки accepted; задачи не меняются. Brief. | Preview + snapshot до/после; cleanup N/A. |
| TS-002 | BC-002 | Malformed | Пустой файл, header-only, отсутствующий/лишний header. | Безопасная ошибка и ноль изменений. Brief. | Error + snapshot. |
| TS-003 | BC-002 | Negative | Пустые/пробельные обязательные поля, unknown priority, несуществующая дата. | Каждая строка rejected с причиной; Confirm недоступен. Brief. | Preview, disabled Confirm, snapshot. |
| TS-004 | BC-002 | Boundary | `D`, `D+365`, `D+366` в UTC. | Первые две даты accepted, `D+366` rejected. Brief. | Зафиксированная дата + preview. |
| TS-005 | BC-003 | Atomicity / P0 | Загрузить [mixed_invalid_tasks.csv](../demo_case/fixtures/mixed_invalid_tasks.csv) и попытаться Confirm. | Confirm блокирован; семантические строки задач (`task_id`, `title`, `priority`, `due_date`), отсортированные по ID и без метаданных снятия, равны baseline. Brief. | Full before/after snapshot. |
| TS-006 | BC-002, BC-003 | Duplicate | Две строки с `tb-200` и `TB-200`. | Файл invalid; данных не меняет ни одна строка. Brief. | Preview + snapshot. |
| TS-007 | BC-004, BC-005 | Create/update/idempotency | Confirm валидный файл; заново загрузить тот же файл, получить новый preview и Confirm. | Новые ID созданы, `TB-100` обновлена; после fresh Confirm второго preview набор идентичен первому. Brief. | Два snapshots + export. Restore baseline. |
| TS-008 | BC-006 | Used preview replay | После успешного Confirm повторить тот же Confirm с тем же checksum. | Безопасный отказ `Preview expired; upload again`; ноль новых или обновлённых задач. Brief. | Response + snapshot. Restore baseline. |
| TS-009 | BC-006 | Stale state | Создать preview, изменить исходное состояние, подтвердить старый preview. | Безопасный отказ `Preview expired; upload again`; ноль изменений от старого preview. Brief. | Checksum/result + snapshot. Restore baseline. |
| TS-010 | BC-007 | Authorization | Не-manager пытается Confirm в UI и напрямую. | Операция отклонена сервером, задач не меняет. Brief. | UI + safe response + snapshot. |
| TS-011 | BC-008 | Cross-layer parity | После TS-007 скачать Export. | Ровно 4 колонки в заданном порядке; нормализованные ID, текущие значения, сортировка по ID; Export read-only. Brief. | Parsed export vs expected table. |
| TS-012 | BC-009 | Localization | Повторить TS-003, TS-010, TS-011 в EN и RU. | Все видимые статусы/ошибки локализованы; итог данных не зависит от языка. Brief. | EN/RU evidence + snapshots. |

## Матрица покрытия

| Область | Сценарии | Статус плана |
| --- | --- | --- |
| Happy path | TS-001, TS-007, TS-011 | Planned |
| Негативные / malformed | TS-002, TS-003 | Planned |
| Границы | TS-004 | Planned |
| Дубли / идемпотентность | TS-006, TS-007, TS-008 | Planned |
| Целостность / rollback | TS-005, TS-008, TS-009 | Planned |
| Права | TS-010 | Planned |
| Export parity | TS-011 | Planned |
| Локализация | TS-012 | Planned |

## Release gates

- Никакой `Pass`, пока P0 TS-005, TS-007–TS-011 не имеют evidence.
- Любое изменение данных после invalid/expired/unauthorized Confirm — release blocker.
- Любая разница между expected table и Export — `Fail`, пока не доказано согласованное исключение.
