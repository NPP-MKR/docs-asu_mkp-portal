# Налаштування Nginx

Версія документа: 1.1

## 1. Призначення

Створіть окремі серверні блоки для Frontend, Backend і API.

| Піддомен | Каталог |
|---|---|
| `portal.example.com` | `<каталог_порталу>/frontend/web` |
| `admin.example.com` | `<каталог_порталу>/backend/web` |
| `api.example.com` | `<каталог_порталу>/api/web` |

У прикладах використовується каталог `/var/www/mkp`. За потреби замініть його на фактичний шлях встановлення порталу.

## 2. Сокет PHP-FPM

```bash
ls /run/php/
```

Використовуйте фактичний сокет, наприклад `php8.3-fpm.sock`.

## 3. Захист від автоматизованого сканування

Для Frontend, Backend та API рекомендується використовувати один файл правил захисту. Він блокує поширені запити до `.env`, Git, WordPress, phpMyAdmin, резервних копій та інших службових ресурсів **до передачі запиту в Yii2**.

Створіть каталог (якщо його ще немає) та файл:

```bash
sudo mkdir -p /etc/nginx/snippets
sudo nano /etc/nginx/snippets/block-scanners.conf
```

Додайте правила:

```nginx
# Приховані конфігурації та службові каталоги
location ~* (^|/)\.(env([./]|$)|git([/.]|$)|svn([/.]|$)|hg([/.]|$)|idea(/|$)|vscode(/|$)|aws(/|$)|ssh(/|$)) {
    return 403;
}

# Службові файли
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

# phpMyAdmin та Adminer
location ~* (^|/)(phpmyadmin|pma)(/|$) {
    return 403;
}
location ~* (^|/)adminer(\.php|/|$) {
    return 403;
}

# Пошук уразливих компонентів та службових інтерфейсів
location ~* (^|/)vendor/phpunit(/|$) {
    return 403;
}
location ~* (^|/)(server-status|server-info|actuator)(/|$) {
    return 403;
}

# Файли конфігурації та менеджерів залежностей
location ~* (^|/)(composer\.(json|lock)|package(-lock)?\.json|yarn\.lock|Dockerfile|artisan)$ {
    return 403;
}

# Резервні копії, дампи баз даних та журнали
location ~* \.(bak|backup|old|orig|save|swp|sql|sqlite|ini|log)$ {
    return 403;
}
```

> **Увага.** Правила `location` включаються **всередині** блоку `server`, а не всередині іншого `location`. Якщо в конфігурації вже є регулярні `location`, перевірте їх порядок: перший відповідний регулярний блок має пріоритет. Блоки з модифікатором `^~` можуть обходити ці правила. Перед використанням перевірте, чи не потрібні порталу файли з переліченими назвами або розширеннями.

Після підключення цього файлу додаткове загальне правило заборони прихованих файлів залишається корисним. У прикладах нижче воно не блокує `/.well-known/`, який може використовуватися для підтвердження домену та випуску сертифікатів.

## 4. Frontend

Створіть `/etc/nginx/sites-available/portal`:

```nginx
server {
    listen 80;
    server_name portal.example.com;

    root /var/www/mkp/frontend/web;
    index index.php;

    # Підключити захист перед іншими регулярними location
    include /etc/nginx/snippets/block-scanners.conf;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # Єдина стандартна публічна точка входу Yii2
    location = /index.php {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
    }

    # Заборонити виконання інших PHP-файлів через веб
    location ~ \.php(?:/|$) {
        return 403;
    }

    # Заборонити інші приховані файли; дозволити .well-known
    location ~ /\.(?!well-known(?:/|$)) {
        return 403;
    }

    access_log /var/log/nginx/portal-access.log;
    error_log /var/log/nginx/portal-error.log;
}
```

> **Важливо.** Обробник `location = /index.php` призначений для стандартного Yii2, де `index.php` — єдина публічна PHP-точка входу. Якщо встановлені модулі використовують інші публічні PHP-скрипти, їх потрібно окремо перевірити та дозволити. Не копіюйте додатковий загальний обробник `location ~ \.php$`, який дозволяє запуск довільних PHP-файлів.

## 5. Backend і API

Створіть `/etc/nginx/sites-available/admin` та `/etc/nginx/sites-available/api` за зразком Frontend, змінивши:

| Параметр | Backend | API |
|---|---|---|
| `server_name` | `admin.example.com` | `api.example.com` |
| `root` | `/var/www/mkp/backend/web` | `/var/www/mkp/api/web` |
| `access_log` | `/var/log/nginx/admin-access.log` | `/var/log/nginx/api-access.log` |
| `error_log` | `/var/log/nginx/admin-error.log` | `/var/log/nginx/api-error.log` |

В **обох** блоках `server` залиште:

```nginx
include /etc/nginx/snippets/block-scanners.conf;
```

Також збережіть маршрутизацію Yii2 через `try_files`, точковий PHP-обробник `index.php` та заборону виконання інших PHP-файлів.

Ці правила не забороняють HTTP-методи `POST`, `PUT`, `PATCH` і `DELETE` та не блокують робочі маршрути API самі по собі.

## 6. Увімкнення конфігурацій

```bash
sudo ln -s /etc/nginx/sites-available/portal /etc/nginx/sites-enabled/portal
sudo ln -s /etc/nginx/sites-available/admin /etc/nginx/sites-enabled/admin
sudo ln -s /etc/nginx/sites-available/api /etc/nginx/sites-enabled/api
sudo nginx -t
sudo systemctl reload nginx
```

Команди створення символічних посилань виконуйте лише якщо посилань ще немає. Перед перезавантаженням Nginx переконайтеся, що перевірка `nginx -t` завершилася успішно.

## 7. HTTPS

Після перевірки HTTP налаштуйте [HTTPS](ssl.md).

Правила захисту необхідно застосувати й до HTTPS-блоків `server` (порт 443). Якщо Certbot змінює конфігурацію, після його роботи перевірте, що підключення `block-scanners.conf` збереглося.

## 8. Перевірка захисту

Перевірте конфігурацію та застосуйте зміни:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Після налаштування HTTPS перевірте відомі шляхи сканування:

```bash
curl -i https://portal.example.com/.env
curl -i https://portal.example.com/.git/config
curl -i https://portal.example.com/wp-login.php
curl -i https://portal.example.com/phpmyadmin/
curl -i https://portal.example.com/backup.sql
```

Для цих прикладів очікується відповідь **403 Forbidden**. Аналогічно перевірте `admin.example.com` і `api.example.com`.

Окремо перевірте головну сторінку, авторизацію, фактичні маршрути API, CSS/JavaScript, а також HTTPS-перевірку домену, якщо використовується `/.well-known/`.

Заблоковані запити зазвичай залишаються в журналі доступу Nginx, але не надходять до Yii2:

```bash
sudo tail -f /var/log/nginx/portal-access.log
sudo tail -f /var/log/nginx/portal-error.log
```

За великої кількості сканувань розгляньте використання Fail2ban або обмеження частоти запитів (`limit_req`). Самі правила заборони URL не замінюють оновлення системи, обмеження доступу до конфігурацій та належні права файлової системи.

## 9. Типові помилки

- **502 Bad Gateway** — перевірте PHP-FPM та шлях до сокета.
- **404 на маршрутах Yii2** — перевірте `try_files`, `root` і налаштування URL-маршрутів.
- **403 на `.env`, `.git`, WordPress, phpMyAdmin** — очікуване блокування правилами захисту.
- **403 на робочій сторінці або API** — перевірте порядок `location`, налаштування доступу та винятки для необхідних ресурсів.
- **500 після зміни конфігурації** — перевірте `error_log` і результат `nginx -t`.

## 10. Офіційна документація

- [Nginx Documentation](https://nginx.org/en/docs/)
- [Nginx: How nginx processes a request](https://nginx.org/en/docs/http/request_processing.html)
- [Nginx: location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location)
