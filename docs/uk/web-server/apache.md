# Налаштування Apache

Версія документа: 1.0

## 1. Призначення

Необхідно створити окремі віртуальні хости для Frontend, Backend і API.

| Піддомен | Каталог |
|---|---|
| `portal.example.com` | `<каталог_порталу>/frontend/web` |
| `admin.example.com` | `<каталог_порталу>/backend/web` |
| `api.example.com` | `<каталог_порталу>/api/web` |

## 2. Модулі Apache

```bash
sudo a2enmod rewrite
sudo a2enmod headers
sudo a2enmod ssl
sudo systemctl restart apache2
```

## 3. Frontend

Створіть `/etc/apache2/sites-available/portal.conf`:

```apache
<VirtualHost *:80>
    ServerName portal.example.com
    DocumentRoot /var/www/mkp/frontend/web

    <Directory /var/www/mkp/frontend/web>
        AllowOverride All
        Options FollowSymLinks
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/portal-error.log
    CustomLog ${APACHE_LOG_DIR}/portal-access.log combined
</VirtualHost>
```

## 4. Backend і API

Створіть аналогічні файли `admin.conf` та `api.conf`, змінивши `ServerName`, `DocumentRoot` і назви журналів.

## 5. Увімкнення сайтів

```bash
sudo a2ensite portal.conf
sudo a2ensite admin.conf
sudo a2ensite api.conf
sudo apachectl configtest
sudo systemctl reload apache2
```

## 6. HTTPS

Після перевірки HTTP налаштуйте [HTTPS](ssl.md). HTTP має залишатися лише для перенаправлення.

## 7. Журнали

```text
/var/log/apache2/portal-error.log
/var/log/apache2/admin-error.log
/var/log/apache2/api-error.log
```

## 8. Типові помилки

- **403** — перевірте `DocumentRoot`, `<Directory>` і права.
- **404 на маршрутах** — перевірте `rewrite` та `AllowOverride All`.
- **500** — перевірте журнал Apache і журнали застосунку.

## 9. Офіційна документація

- [Apache HTTP Server](https://httpd.apache.org/docs/)
