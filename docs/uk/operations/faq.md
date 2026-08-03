# Часті запитання та типові помилки

Версія документа: 1.0

## PHP не знайдено

```text
php: command not found
```

Перевірте:

```bash
php -v
```

## Composer не знайдено

```bash
composer --version
```

Переконайтеся, що Composer установлено глобально або використовуйте повний шлях.

## Не знайдено драйвер Firebird

```text
could not find driver
```

Перевірте:

```bash
php -m | grep -i firebird
```

## Permission denied (publickey)

Перевірте реєстрацію відкритого ключа, `~/.ssh/config`, `IdentityFile` і доступ до репозиторію.

## Could not resolve hostname github-mkp

Перевірте запис:

```text
Host github-mkp
    HostName github.com
```

## Помилка 403

Перевірте кореневий каталог віртуального хоста, права, `<Directory>` в Apache або `root` у Nginx.

## Помилка 404 на внутрішніх сторінках

Для Apache перевірте `mod_rewrite` та `AllowOverride`. Для Nginx — `try_files`.

## Помилка 500

Перевірте журнали застосунку, вебсервера й PHP, `main-local.php`, бази даних і розширення PHP.

## Помилка 502 у Nginx

Перевірте PHP-FPM, сокет і права доступу до нього.

## Не завантажуються файли

Перевірте сховище, права, `upload_max_filesize`, `post_max_size` і вільне місце.

## Після оновлення відображається стара версія

```bash
php yii cache/flush-all
```

Потім очистіть кеш браузера.

## Де шукати журнали

- `frontend/runtime/logs`;
- `backend/runtime/logs`;
- журнали API;
- `/var/log/apache2`;
- `/var/log/nginx`;
- системний журнал.
