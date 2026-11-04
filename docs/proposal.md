# Thesis Proposal

| Параметр | Значення |
|---|---|
| Виконавець | Іщук Єгор Тімурович, група 443 А |
| Науковий керівник | Валь Олександр Данилович, кандидат фізико-математичних наук, доцент кафедри ПЗКС |
| Спеціальність | F2 Інженерія програмного забезпечення |
| Версія документа | 1.0 |

---

## Частина 1. Українською

### 1. Попередня робоча назва

**Українською:** Платформа податкового обліку для ФОП із версіонуванням податкових правил

**Англійською:** Tax Accounting Platform for Individual Entrepreneurs with Versioned Tax Rules

### 2. Актуальність та проблематика

Податкові правила для фізичних осіб-підприємців (ФОП) регулярно змінюються: переглядаються ставки, бази оподаткування, ліміти доходу для груп єдиного податку та строки сплати. Зміни набувають чинності з різних дат, а окремі норми можуть діяти заднім числом. Облік доходів ФОП здебільшого ведеться вручну або в розрізнених інструментах, що призводить до помилок у звірянні надходжень, неправильного розрахунку зобов'язань і пропуску строків сплати.

Інженерна складність задачі полягає в такому:

- **Темпоральна коректність.** Кожен розрахунок має виконуватися за правилами, чинними в періоді, до якого належить господарська операція, а не на момент обчислення.
- **Відтворюваність.** Будь-який отриманий результат має бути відтворюваним із тими самими версіями правил і вхідними даними, що є вимогою аудиту.
- **Керована еволюція правил.** Публікація нової версії правил має супроводжуватися оцінюванням її впливу на вже сформовані розрахунки без пошкодження історичних даних.
- **Надійність обробки даних.** Імпорт банківських виписок має бути ідемпотентним, а асинхронна обробка — стійкою до збоїв і повторних доставок повідомлень.

### 3. Сформована ідея продукту

Мультитенантна вебплатформа з подієво-орієнтованою (event-driven) мікросервісною архітектурою. Ядром системи є реєстр податкових правил із бітемпоральною моделлю даних: кожна версія правила має період юридичної дії (valid time) та момент реєстрації в системі (transaction time). Сервіси взаємодіють через брокер повідомлень із гарантією доставки за патерном Transactional Outbox. Звітність формується окремою моделлю читання за патерном CQRS.

Предметна область обмежена ФОП 1–3 груп єдиного податку. Архітектура передбачає розширення на інші категорії платників шляхом додавання нових наборів правил без зміни ядра системи.

### 4. Ключові функції (Core Features)

1. **Мультитенантні профілі платників.** Облік групи єдиного податку, ставки та реквізитів. Ізоляція даних між тенантами на рівні СУБД (Row-Level Security). Рольова модель доступу: власник ФОП та бухгалтер із делегованим доступом до кількох клієнтів.
2. **Рахунки-фактури та акти виконаних робіт.** Формування документів, генерація PDF, відстеження статусу оплати (виставлено, частково оплачено, оплачено).
3. **Ідемпотентний імпорт банківських виписок.** Завантаження у форматі CSV та через API банку, нормалізація операцій, дедуплікація за хешем операції.
4. **Автоматичне звіряння надходжень.** Зіставлення платежів із рахунками за точним і нечітким збігом (сума, контрагент, призначення платежу), підтримка часткових оплат, ручне підтвердження неоднозначних збігів.
5. **Рушій податкових розрахунків.** Розрахунок єдиного податку, єдиного соціального внеску та військового збору. Кожен результат зберігає ідентифікатор версії правил і хеш вхідних даних. Контроль граничного обсягу доходу для групи.
6. **Реєстр податкових правил з аналізом впливу змін.** Правила зберігаються як код (Rules as Code) і проходять життєвий цикл Draft → Review → Published. Після публікації нової версії виконується what-if перерахунок у тіньовому режимі та формується diff-звіт «було / стало».
7. **Податковий календар і сповіщення.** Автоматичне визначення строків сплати й подання звітності, асинхронне надсилання сповіщень (email, Telegram).

### 5. Попередній інструментарій та технологічний стек

| Категорія | Технології |
|---|---|
| Мови програмування | Python 3.12 (backend), TypeScript (frontend), SQL |
| Архітектурні стилі та патерни | Event-Driven Microservices, Transactional Outbox, Idempotent Consumer, CQRS, Bitemporal Data Model, Strategy (розрахункові стратегії), Dead Letter Queue, API Gateway |
| Фреймворки та бібліотеки | FastAPI, Pydantic v2, SQLAlchemy 2.0, Alembic, aiokafka, cel-python (безпечні вирази правил), WeasyPrint (PDF), React |
| Бази даних та сховища | PostgreSQL 16 (Row-Level Security, темпоральні діапазони), Redis (кеш, rate limiting) |
| Брокер повідомлень | Apache Kafka |
| Тестування та якість | pytest, Hypothesis (property-based), Testcontainers, golden tests для версій правил, k6 (навантажувальне тестування), Ruff, mypy |
| Безпека (DevSecOps) | SonarQube (SAST), Bandit, Trivy (сканування образів і залежностей), OAuth2 / JWT, шифрування чутливих полів |
| DevOps та розгортання | Docker, Docker Compose, GitHub Actions, Kubernetes, Helm |
| Спостережуваність | OpenTelemetry, Prometheus, Grafana, Loki, Jaeger |
| Документування | C4 Model (Structurizr), Architecture Decision Records, OpenAPI, MkDocs |

### 6. Межі проєкту

Поза межами роботи: юридичні особи, податок на додану вартість, податок на прибуток, подання звітності до Електронного кабінету ДПС із накладанням КЕП, автоматичний розбір текстів нормативних актів. Ці напрями визначено як можливості подальшого розвитку.

---

## Part 2. English

### 1. Working Title

**Ukrainian:** Платформа податкового обліку для ФОП із версіонуванням податкових правил

**English:** Tax Accounting Platform for Individual Entrepreneurs with Versioned Tax Rules

### 2. Relevance and Problem Statement

Tax rules for Ukrainian individual entrepreneurs change regularly: rates, tax bases, income limits for single-tax groups and payment deadlines are revised. Changes take effect from different dates, and some provisions may apply retroactively. Income accounting is mostly performed manually or with disconnected tools, which leads to reconciliation errors, incorrect liability calculation and missed payment deadlines.

The engineering complexity of the problem includes:

- **Temporal correctness.** Each calculation must use the rules in force for the period of the business transaction, not at the moment of computation.
- **Reproducibility.** Any result must be reproducible with the same rule versions and input data, as required for audit.
- **Controlled rule evolution.** Publishing a new rule version must be accompanied by an impact assessment on existing calculations without corrupting historical data.
- **Reliable data processing.** Bank statement import must be idempotent, and asynchronous processing must tolerate failures and redelivered messages.

### 3. Product Concept

A multi-tenant web platform based on an event-driven microservice architecture. The core of the system is a tax rules registry with a bitemporal data model: each rule version has a period of legal validity (valid time) and a registration moment in the system (transaction time). Services communicate through a message broker with delivery guarantees provided by the Transactional Outbox pattern. Reporting is served by a separate read model following the CQRS pattern.

The domain is limited to individual entrepreneurs of single-tax groups 1–3. The architecture allows extension to other taxpayer categories by adding new rule sets without changing the system core.

### 4. Core Features

1. **Multi-tenant taxpayer profiles.** Single-tax group, rate and requisites. Tenant data isolation at the database level (Row-Level Security). Role model: entrepreneur and accountant with delegated access to multiple clients.
2. **Invoices and service acts.** Document generation, PDF rendering, payment status tracking (issued, partially paid, paid).
3. **Idempotent bank statement import.** CSV and bank API ingestion, transaction normalization, deduplication by transaction hash.
4. **Automated payment reconciliation.** Exact and fuzzy matching of payments to invoices (amount, counterparty, payment purpose), partial payment support, manual confirmation of ambiguous matches.
5. **Tax calculation engine.** Calculation of the single tax, the unified social contribution and the military levy. Each result stores the rule version identifier and the input data hash. Monitoring of the income limit for the single-tax group.
6. **Tax rules registry with change impact analysis.** Rules are stored as code (Rules as Code) and follow the Draft → Review → Published lifecycle. Publishing a new version triggers a what-if recalculation in shadow mode and produces a before/after diff report.
7. **Tax calendar and notifications.** Automatic derivation of payment and reporting deadlines, asynchronous notifications (email, Telegram).

### 5. Technology Stack

| Category | Technologies |
|---|---|
| Programming languages | Python 3.12 (backend), TypeScript (frontend), SQL |
| Architectural styles and patterns | Event-Driven Microservices, Transactional Outbox, Idempotent Consumer, CQRS, Bitemporal Data Model, Strategy (calculation strategies), Dead Letter Queue, API Gateway |
| Frameworks and libraries | FastAPI, Pydantic v2, SQLAlchemy 2.0, Alembic, aiokafka, cel-python (safe rule expressions), WeasyPrint (PDF), React |
| Databases and storage | PostgreSQL 16 (Row-Level Security, temporal ranges), Redis (cache, rate limiting) |
| Message broker | Apache Kafka |
| Testing and quality | pytest, Hypothesis (property-based), Testcontainers, golden tests for rule versions, k6 (load testing), Ruff, mypy |
| Security (DevSecOps) | SonarQube (SAST), Bandit, Trivy (image and dependency scanning), OAuth2 / JWT, encryption of sensitive fields |
| DevOps and deployment | Docker, Docker Compose, GitHub Actions, Kubernetes, Helm |
| Observability | OpenTelemetry, Prometheus, Grafana, Loki, Jaeger |
| Documentation | C4 Model (Structurizr), Architecture Decision Records, OpenAPI, MkDocs |

### 6. Scope

Out of scope: legal entities, value-added tax, corporate income tax, report submission to the State Tax Service e-cabinet with a qualified electronic signature, automatic parsing of legislative texts. These areas are identified as future development directions.
