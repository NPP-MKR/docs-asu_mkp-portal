# Налаштування Nginx

Версія документа: 1.0

## 1. Призначення

Створіть окремі серверні блоки для Frontend, Backend і API.

| Піддомен | Каталог |
|---|---|
| `portal.example.com` | `<каталог_порталу>/frontend/web` |
| `admin.example.com` | `<каталог_порталу>/backend/web` |
| `api.example.com` | `<каталог_порталу>/api/web` |

## 2. Сокет PHP-FPM

```bash
ls /run/php/
```

Використовуйте фактичний сокет, наприклад `php8.3-fpm.sock`.

## 3. Frontend

Створіть `/etc/nginx/sites-available/portal`:

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

## 4. Backend і API

Створіть аналогічні конфігурації, змінивши `server_name`, `root` і назви журналів.

## 5. Увімкнення конфігурацій

```bash
sudo ln -s /etc/nginx/sites-available/portal /etc/nginx/sites-enabled/portal
sudo ln -s /etc/nginx/sites-available/admin /etc/nginx/sites-enabled/admin
sudo ln -s /etc/nginx/sites-available/api /etc/nginx/sites-enabled/api
sudo nginx -t
sudo systemctl reload nginx
```

## 6. HTTPS

Після перевірки HTTP налаштуйте [HTTPS](ssl.md).

## 7. Типові помилки

- **502** — перевірте PHP-FPM і шлях до сокета.
- **404 на маршрутах** — перевірте `try_files`.
- **403** — перевірте `root` і права доступу.

## 8. Офіційна документація

- [Nginx Documentation](https://nginx.org/en/docs/)
