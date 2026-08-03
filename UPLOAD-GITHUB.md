# Додавання файлів через браузер GitHub

## 1. Відкрийте репозиторій

```text
https://github.com/NPP-MKR/asu_mkp
```

## 2. Завантажте кореневі файли

На вкладці **Code** виберіть:

```text
Add file → Upload files
```

Завантажте:

- `mkdocs.yml`;
- `requirements-docs.txt`;
- `README.md`;
- `.gitignore`.

## 3. Додайте workflow

Виберіть **Add file → Create new file** та вкажіть:

```text
.github/workflows/docs.yml
```

Вставте вміст файла `docs.yml` із комплекту та збережіть.

## 4. Завантажте документацію

Каталоги:

```text
docs/assets/
docs/uk/installation/
docs/uk/configuration/
docs/uk/web-server/
docs/uk/operations/
docs/ru/installation/
docs/ru/configuration/
docs/ru/web-server/
docs/ru/operations/
```

Українська версія є основною. Російська — додатковою.

## 5. Увімкніть GitHub Pages

```text
Settings → Pages → Deploy from a branch
Branch: gh-pages
Folder: /(root)
```

## 6. Перевірте сайт

Українська версія:

```text
https://npp-mkr.github.io/asu_mkp/
```

Російська версія:

```text
https://npp-mkr.github.io/asu_mkp/ru/
```
