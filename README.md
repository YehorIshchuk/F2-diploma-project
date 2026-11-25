# F2-diploma-project

Кваліфікаційний проєкт бакалавра за спеціальністю **F2 Інженерія програмного забезпечення**.

**Тема:** Платформа податкового обліку для ФОП із версіонуванням податкових правил
**Topic:** Tax Accounting Platform for Individual Entrepreneurs with Versioned Tax Rules

| | |
|---|---|
| Виконавець | Іщук Єгор Тімурович, група 443 А |
| Науковий керівник | Валь Олександр Данилович, к.ф.-м.н., доцент кафедри ПЗКС |
| Кафедра | Програмного забезпечення комп'ютерних систем (ПЗКС) |
| Заклад | Чернівецький національний університет імені Юрія Федьковича |

## Структура репозиторію

```text
.
├── .github/
│   ├── workflows/docs-linter.yml  # Docs Quality Gate: markdownlint + Vale на кожен Pull Request
│   └── vale/styles/Diploma/       # Правила Vale: сленг, транслітерація, маркетинг
├── .markdownlint-cli2.jsonc       # Правила форматування Markdown
├── .vale.ini                      # Конфігурація Vale
├── docs/                          # Пояснювальна записка за принципом Documentation as Code
│   ├── 0_intro.md … 4_conclusions.md
│   ├── srs.md                     # Специфікація вимог (ISO/IEC/IEEE 29148)
│   ├── adr/                       # Architecture Decision Records
│   ├── api/openapi.yaml           # API-контракт OpenAPI 3.0.3
│   ├── models/                    # Моделі CASE-засобу StarUML
│   ├── assets/                    # Експортовані зображення діаграм
│   └── ai-disclosure.md           # Протокол використання генеративного ШІ
└── src/                           # Вихідний код (Розділ 3)
```

Зведений документ Розділів 1–2 (Google Docs): [F2_Diploma_Chapters1-2_Ishchuk](https://docs.google.com/document/d/1JQUeSONICKH3otokZ6KLzqHIqSzsY2PH/edit?usp=sharing).

## Угоди

- Повідомлення комітів відповідають специфікації [Conventional Commits](https://www.conventionalcommits.org/).
- Уся документація зберігається у форматі Markdown поруч із кодом і проходить ту саму процедуру рецензування.
