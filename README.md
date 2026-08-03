# Документація проєкту asu_mkp

Репозиторій підготовлено для публікації документації через MkDocs Material і GitHub Pages.

## Мови документації

- **Українська** — основна мова, публікується в корені сайту.
- **Російська** — додаткова мова, публікується в каталозі `/ru/`.

## Структура

```text
docs/
├── assets/
├── uk/
│   ├── installation/
│   ├── configuration/
│   ├── web-server/
│   └── operations/
└── ru/
    ├── installation/
    ├── configuration/
    ├── web-server/
    └── operations/
```

## Локальний запуск

Установіть залежності:

```bash
pip install -r requirements-docs.txt
```

Запустіть сайт:

```bash
mkdocs serve
```

Відкрийте:

```text
http://127.0.0.1:8000
```

## Публікація

Workflow `.github/workflows/docs.yml` автоматично публікує сайт у гілку `gh-pages` після надсилання змін до гілки `main`.

У налаштуваннях GitHub Pages виберіть:

```text
Settings → Pages → Deploy from a branch → gh-pages → /(root)
```

## Адреса сайту

```text
https://npp-mkr.github.io/asu_mkp/
```
