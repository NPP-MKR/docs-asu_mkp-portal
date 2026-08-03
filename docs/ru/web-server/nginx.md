# Настройка Nginx

Версия документа: 1.0

## 1. Назначение

Документ описывает общую настройку Nginx для Frontend, Backend и API портала АСУ МКР.

## 2. Предварительные требования

Необходимы:

- установленный Nginx;
- PHP-FPM;
- подготовленный портал;
- три доменных имени;
- DNS-записи.

## 3. Структура хостов

| Поддомен | Каталог |
|---|---|
| `portal.example.com` | `<каталог_портала>/frontend/web` |
| `admin.example.com` | `<каталог_портала>/backend/web` |
| `api.example.com` | `<каталог_портала>/api/web` |

## 4. Определение сокета PHP-FPM

Проверьте доступные сокеты:

```bash
ls /run/php/
```

Пример:

```text
php8.3-fpm.sock
```

Используйте фактическое имя сокета в конфигурации Nginx.

## 5. Frontend

Создайте файл:

```text
/etc/nginx/sites-available/portal
```

Пример:

```nginx
server {
    listen 80;
    server_name portal.example.com;

    root /var/www/mkp/frontend/web;
    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
    }

    location ~ /\. {
        deny all;
    }

    access_log /var/log/nginx/portal-access.log;
    error_log /var/log/nginx/portal-error.log;
}
```

## 6. Backend

Создайте файл:

```text
/etc/nginx/sites-available/admin
```

Используйте ту же структуру, заменив:

```nginx
server_name admin.example.com;
root /var/www/mkp/backend/web;
```

## 7. API

Создайте файл:

```text
/etc/nginx/sites-available/api
```

Используйте ту же структуру, заменив:

```nginx
server_name api.example.com;
root /var/www/mkp/api/web;
```

## 8. Включение конфигураций

Создайте символические ссылки:

```bash
sudo ln -s /etc/nginx/sites-available/portal /etc/nginx/sites-enabled/portal
sudo ln -s /etc/nginx/sites-available/admin /etc/nginx/sites-enabled/admin
sudo ln -s /etc/nginx/sites-available/api /etc/nginx/sites-enabled/api
```

Проверьте конфигурацию:

```bash
sudo nginx -t
```

Если ошибок нет, перезагрузите Nginx:

```bash
sudo systemctl reload nginx
```

## 9. HTTPS

После проверки HTTP настройте SSL согласно документу [Настройка HTTPS](ssl.md).

## 10. Проверка

Откройте:

```text
http://portal.example.com
http://admin.example.com
http://api.example.com
```

После установки сертификатов используйте только HTTPS.

## 11. Журналы

Примеры журналов:

```text
/var/log/nginx/portal-error.log
/var/log/nginx/admin-error.log
/var/log/nginx/api-error.log
```

Просмотр:

```bash
sudo tail -f /var/log/nginx/portal-error.log
```

## 12. Типичные ошибки

### 502 Bad Gateway

Проверьте:

- работу PHP-FPM;
- путь к сокету;
- права доступа к сокету.

### 404 на внутренних маршрутах

Проверьте директиву:

```nginx
try_files $uri $uri/ /index.php?$args;
```

### 403 Forbidden

Проверьте корневой каталог и права доступа.

## 13. Официальная документация

- [Nginx Documentation](https://nginx.org/en/docs/)
