# Настройка Nginx

Версия документа: 1.1

## 1. Назначение

Документ описывает настройку Nginx для Frontend, Backend и API портала АСУ МКР, включая защиту от автоматизированного сканирования.

## 2. Предварительные требования

Необходимы:

- установленный Nginx;
- PHP-FPM;
- подготовленный портал;
- три доменных имени с настроенными DNS-записями.

## 3. Структура хостов

| Поддомен | Каталог |
|---|---|
| `portal.example.com` | `<каталог_портала>/frontend/web` |
| `admin.example.com` | `<каталог_портала>/backend/web` |
| `api.example.com` | `<каталог_портала>/api/web` |

В примерах используется `/var/www/mkp`. Замените этот путь на фактический каталог установки портала.

## 4. Определение сокета PHP-FPM

Проверьте доступные сокеты:

```bash
ls /run/php/
```

Например, `php8.3-fpm.sock`. Используйте фактическое имя сокета в конфигурации Nginx.

## 5. Защита от автоматизированного сканирования

Для Frontend, Backend и API рекомендуется использовать один файл правил. Nginx будет блокировать распространённые запросы к `.env`, Git, WordPress, phpMyAdmin, резервным копиям и другим служебным ресурсам **до передачи запроса в Yii2**.

Создайте каталог (если его ещё нет) и файл:

```bash
sudo mkdir -p /etc/nginx/snippets
sudo nano /etc/nginx/snippets/block-scanners.conf
```

Добавьте правила:

```nginx
# Скрытые конфигурационные файлы и служебные каталоги
location ~* (^|/)\.(env([./]|$)|git([/.]|$)|svn([/.]|$)|hg([/.]|$)|idea(/|$)|vscode(/|$)|aws(/|$)|ssh(/|$)) {
    return 403;
}

# Служебные файлы
location ~* (^|/)(\.gitignore|\.gitattributes|\.htaccess|\.htpasswd|web\.config)$ {
    return 403;
}

# WordPress
location ~* (^|/)(wp-admin|wp-content|wp-includes|wordpress)(/|$) {
    return 403;
}
location ~* (^|/)(wp-login|wp-config|wp-cron|wp-settings|wp-load|xmlrpc)\.php$ {
    return 403;
}

# phpMyAdmin и Adminer
location ~* (^|/)(phpmyadmin|pma)(/|$) {
    return 403;
}
location ~* (^|/)adminer(\.php|/|$) {
    return 403;
}

# Поиск уязвимых компонентов и служебных интерфейсов
location ~* (^|/)vendor/phpunit(/|$) {
    return 403;
}
location ~* (^|/)(server-status|server-info|actuator)(/|$) {
    return 403;
}

# Конфигурации и файлы менеджеров зависимостей
location ~* (^|/)(composer\.(json|lock)|package(-lock)?\.json|yarn\.lock|Dockerfile|artisan)$ {
    return 403;
}

# Резервные копии, дампы баз данных и журналы
location ~* \.(bak|backup|old|orig|save|swp|sql|sqlite|ini|log)$ {
    return 403;
}
```

> **Внимание.** Эти блоки `location` подключаются **внутри `server`**, а не внутри другого `location`. Порядок регулярных `location` имеет значение: выбирается первое совпавшее правило. Блоки `location ^~` могут препятствовать проверке регулярных выражений. Перед применением проверьте, не использует ли портал какие-либо из перечисленных путей или расширений.

Дополнительное правило для остальных скрытых файлов приведено в примере ниже. Оно не блокирует `/.well-known/`, который может использоваться для проверки домена и получения TLS-сертификатов.

## 6. Frontend

Создайте `/etc/nginx/sites-available/portal`:

```nginx
server {
    listen 80;
    server_name portal.example.com;

    root /var/www/mkp/frontend/web;
    index index.php;

    # Подключить защиту перед другими регулярными location
    include /etc/nginx/snippets/block-scanners.conf;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # Стандартная публичная точка входа Yii2
    location = /index.php {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
    }

    # Запретить выполнение других PHP-файлов через веб
    location ~ \.php(?:/|$) {
        return 403;
    }

    # Запретить остальные скрытые файлы, кроме .well-known
    location ~ /\.(?!well-known(?:/|$)) {
        return 403;
    }

    access_log /var/log/nginx/portal-access.log;
    error_log /var/log/nginx/portal-error.log;
}
```

> **Важно.** `location = /index.php` подходит для стандартного Yii2, где `index.php` — единственная публичная PHP-точка входа. Если модули используют другие публичные PHP-скрипты, проверьте их и добавьте точечные разрешения. Не добавляйте общий обработчик `location ~ \.php$`, разрешающий выполнение произвольных PHP-файлов.

## 7. Backend

Создайте `/etc/nginx/sites-available/admin` по образцу Frontend, заменив:

```nginx
server_name admin.example.com;
root /var/www/mkp/backend/web;
access_log /var/log/nginx/admin-access.log;
error_log /var/log/nginx/admin-error.log;
```

Сохраните подключение `block-scanners.conf`, маршрутизацию Yii2 и точечную обработку `index.php`.

## 8. API

Создайте `/etc/nginx/sites-available/api` по образцу Frontend, заменив:

```nginx
server_name api.example.com;
root /var/www/mkp/api/web;
access_log /var/log/nginx/api-access.log;
error_log /var/log/nginx/api-error.log;
```

Сохраните подключение `block-scanners.conf`, маршрутизацию Yii2 и точечную обработку `index.php`. Эти правила сами по себе не запрещают рабочие API-маршруты или HTTP-методы `POST`, `PUT`, `PATCH`, `DELETE`.

## 9. Включение конфигураций

Создайте символические ссылки (если их ещё нет):

```bash
sudo ln -s /etc/nginx/sites-available/portal /etc/nginx/sites-enabled/portal
sudo ln -s /etc/nginx/sites-available/admin /etc/nginx/sites-enabled/admin
sudo ln -s /etc/nginx/sites-available/api /etc/nginx/sites-enabled/api
```

Проверьте конфигурацию и примените изменения **только после успешной проверки**:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## 10. HTTPS

После проверки HTTP настройте сертификаты по инструкции [Настройка HTTPS](ssl.md).

Правила защиты нужно подключить также в HTTPS-блоках `server` (порт 443). Если Certbot изменяет конфигурацию, проверьте, что строка `include /etc/nginx/snippets/block-scanners.conf;` сохранилась.

После установки сертификатов используйте HTTPS для всех трёх поддоменов.

## 11. Проверка

Откройте:

```text
https://portal.example.com
https://admin.example.com
https://api.example.com
```

После настройки HTTPS проверьте типичные запросы сканеров:

```bash
curl -i https://portal.example.com/.env
curl -i https://portal.example.com/.git/config
curl -i https://portal.example.com/wp-login.php
curl -i https://portal.example.com/phpmyadmin/
curl -i https://portal.example.com/backup.sql
```

Для этих адресов ожидается ответ **403 Forbidden**. Аналогично проверьте домены Backend и API.

Отдельно проверьте главную страницу, авторизацию, реальные API-маршруты, CSS и JavaScript, а при необходимости — запросы к `/.well-known/`.

## 12. Журналы

Примеры журналов:

```text
/var/log/nginx/portal-access.log
/var/log/nginx/portal-error.log
/var/log/nginx/admin-error.log
/var/log/nginx/api-error.log
```

Просмотр:

```bash
sudo tail -f /var/log/nginx/portal-access.log
sudo tail -f /var/log/nginx/portal-error.log
```

Заблокированные запросы обычно остаются в журнале доступа Nginx, но не попадают в Yii2. При массовом сканировании дополнительно рассмотрите Fail2ban или ограничение частоты запросов (`limit_req`). Эти правила не заменяют обновления ПО и корректные права доступа к файлам.

## 13. Типичные ошибки

### 502 Bad Gateway

Проверьте работу PHP-FPM, путь к сокету и права доступа к нему.

### 404 на внутренних маршрутах

Проверьте `root`, правила маршрутизации Yii2 и директиву:

```nginx
try_files $uri $uri/ /index.php$is_args$args;
```

### 403 Forbidden

Для `.env`, `.git`, WordPress, phpMyAdmin и резервных копий это **ожидаемый результат блокировки**. Если 403 возникает на рабочем маршруте, проверьте порядок блоков `location`, права доступа и исключения для необходимых ресурсов.

### Ошибки после изменения конфигурации

Проверьте `sudo nginx -t` и соответствующий `error_log`. Не перезагружайте Nginx, если проверка конфигурации завершается с ошибкой.

## 14. Официальная документация

- [Nginx Documentation](https://nginx.org/en/docs/)
- [Nginx: How nginx processes a request](https://nginx.org/en/docs/http/request_processing.html)
- [Nginx: location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location)
