# QA Context Brief — учебный CSV-импорт задач

**Feature:** [feature_brief.md](../demo_case/feature_brief.md)<br>
**Sources used:** согласованный учебный brief<br>
**Status:** `Ready for plan`

## Behavior contract ledger

| Contract | Атомарное ожидаемое поведение | Поверхности | Независимый oracle |
| --- | --- | --- | --- |
| BC-001 | Preview классифицирует каждую строку и не меняет сохранённые задачи. | UI, API, данные | Снимок задач до/после preview |
| BC-002 | Только строго валидный CSV может перейти к Confirm. | Парсер, UI, API | Контролируемый CSV-набор и причины rejected |
| BC-003 | Невалидная строка блокирует весь Confirm; данных не меняет ни одна строка файла. | UI, API, данные | Полный снимок до/после |
| BC-004 | Новая задача создаётся, существующая обновляется по нормализованному `task_id`. | API, данные, UI | Ожидаемая таблица по ID |
| BC-005 | Повторная загрузка валидного файла с новым preview идемпотентна. | API, данные, Export | Сравнение снимков после двух fresh Confirm |
| BC-006 | Использованный или устаревший preview не может записать данные. | UI, API, данные | Снимок, checksum и безопасный отказ |
| BC-007 | Не-manager не может подтвердить импорт, включая обход UI. | UI, API, данные | Ответ операции и неизменный снимок |
| BC-008 | Export совпадает с текущими сохранёнными задачами, имеет фиксированные колонки и сортировку. | Export, данные | Независимый CSV parser + снимок |
| BC-009 | Поток и сообщения работают в EN/RU без смены данных. | UI, локализация | Скриншоты/accessible text + снимки |

## Карта состояний и побочных эффектов

| Операция | Задачи | Preview | Export |
| --- | --- | --- | --- |
| Upload / Preview | `must remain unchanged` | `must change` | `must remain unchanged` |
| Confirm valid file as manager | `must change` | становится использованным | `must change` |
| Confirm invalid or expired preview | `must remain unchanged` | показывает объяснение | `must remain unchanged` |
| Confirm as non-manager | `must remain unchanged` | показывает отказ | `must remain unchanged` |
| Export | `must remain unchanged` | `not applicable` | формируется из текущего snapshot |

## Риски и готовность

| Риск | Уровень | Как проверяем |
| --- | --- | --- |
| Частичное сохранение ошибочного файла | P0 | TS-005: полный before/after snapshot |
| Дубли, повторная загрузка или replay used preview | P0 | TS-006–TS-008: snapshot и проверка идентичностей |
| Обход роли manager | P0 | TS-010: UI и прямой запрос |
| Preview/data/export рассинхронизированы | P0 | TS-001, TS-011: row reconciliation |
| Ошибка даты на границе | P1 | TS-004: UTC D, D+365, D+366 |
| Неполная локализация | P1 | TS-012: EN/RU workflow |

Материальных открытых вопросов нет: учебный brief задаёт атомарность, нормализацию, экспорт и жизненный цикл preview.
