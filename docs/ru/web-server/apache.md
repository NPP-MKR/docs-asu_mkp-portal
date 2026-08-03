# Настройка Apache

Версия документа: 1.0

## 1. Назначение

Документ описывает общую настройку Apache для трех частей портала АСУ МКР:

- Frontend;
- Backend;
- API.

## 2. Предварительные требования

Перед настройкой должны быть готовы:

- каталог портала;
- три доменных имени или поддомена;
- DNS-записи;
- PHP;
- Apache;
- проект, инициализированный командой `php init --env=Production`.

## 3. Структура виртуальных хостов

| Поддомен | Каталог |
|---|---|
| `portal.example.com` | `<каталог_портала>/frontend/web` |
| `admin.example.com` | `<каталог_портала>/backend/web` |
| `api.example.com` | `<каталог_портала>/api/web` |

Замените `example.com` доменом организации.

## 4. Необходимые модули

Включите типовые модули Apache:

```bash
sudo a2enmod rewrite
sudo a2enmod headers
sudo a2enmod ssl
```

После изменения модулей перезапустите Apache:

```bash
sudo systemctl restart apache2
```

## 5. Виртуальный хост Frontend

Создайте файл:

```text
/etc/apache2/sites-available/portal.conf
```

Пример:

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

## 6. Виртуальный хост Backend

Создайте файл:

```text
/etc/apache2/sites-available/admin.conf
```

Пример:

```apache
<VirtualHost *:80>
    ServerName admin.example.com
    DocumentRoot /var/www/mkp/backend/web

    <Directory /var/www/mkp/backend/web>
        AllowOverride All
        Options FollowSymLinks
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/admin-error.log
    CustomLog ${APACHE_LOG_DIR}/admin-access.log combined
</VirtualHost>
```

## 7. Виртуальный хост API

Создайте файл:

```text
/etc/apache2/sites-available/api.conf
```

Пример:

```apache
<VirtualHost *:80>
    ServerName api.example.com
    DocumentRoot /var/www/mkp/api/web

    <Directory /var/www/mkp/api/web>
        AllowOverride All
        Options FollowSymLinks
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/api-error.log
    CustomLog ${APACHE_LOG_DIR}/api-access.log combined
</VirtualHost>
```

## 8. Включение сайтов

Включите конфигурации:

```bash
sudo a2ensite portal.conf
sudo a2ensite admin.conf
sudo a2ensite api.conf
```

Проверьте синтаксис:

```bash
sudo apachectl configtest
```

Если ошибок нет, перезагрузите конфигурацию:

```bash
sudo systemctl reload apache2
```

## 9. HTTPS

После проверки работы HTTP настройте HTTPS согласно документу [Настройка HTTPS](ssl.md).

После выпуска сертификатов HTTP должен использоваться только для перенаправления на HTTPS.

## 10. Проверка

Проверьте:

```text
http://portal.example.com
http://admin.example.com
http://api.example.com
```

После настройки SSL используйте только `https://`.

## 11. Журналы

Основные журналы:

```text
/var/log/apache2/portal-error.log
/var/log/apache2/admin-error.log
/var/log/apache2/api-error.log
```

Для просмотра последних записей:

```bash
sudo tail -f /var/log/apache2/portal-error.log
```

## 12. Типичные ошибки

### 403 Forbidden

Проверьте `DocumentRoot`, блок `<Directory>` и права доступа.

### 404 на внутренних маршрутах

Проверьте модуль `rewrite` и значение `AllowOverride All`.

### 500 Internal Server Error

Проверьте журнал Apache и журналы приложения.

## 13. Официальная документация

- [Apache HTTP Server](https://httpd.apache.org/docs/)
