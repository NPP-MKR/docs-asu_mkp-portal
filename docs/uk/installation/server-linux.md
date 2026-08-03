# Підготовка сервера Linux

Версія документа: 1.0

## 1. Призначення

Документ описує підготовку Ubuntu Server або Debian для встановлення порталу АСУ МКР.

## 2. Підтримувані системи

| Операційна система | Версія |
|---|---|
| Ubuntu Server | 22.04 LTS і вище |
| Debian | 12 і вище |

## 3. Перед початком

Потрібні:

- обліковий запис із правами `sudo`;
- доступ до Інтернету;
- доменні імена та доступ до DNS;
- відомості для підключення до Firebird.

## 4. Оновлення системи

Оновіть список пакетів:

```bash
sudo apt update
```

Установіть оновлення:

```bash
sudo apt upgrade -y
```

За потреби перезавантажте сервер:

```bash
sudo reboot
```

## 5. Базові утиліти

```bash
sudo apt install -y ca-certificates curl unzip gnupg lsb-release software-properties-common
```

## 6. PHP

Потрібен PHP 8.1 і вище, рекомендовано 8.3.

Приклад установлення PHP 8.3:

```bash
sudo apt install -y \
    php8.3 php8.3-cli php8.3-bcmath php8.3-curl \
    php8.3-gd php8.3-gettext php8.3-imap php8.3-intl \
    php8.3-mbstring php8.3-xml php8.3-zip
```

Установіть драйвер Firebird:

```bash
sudo apt install -y php8.3-interbase
```

Перевірте:

```bash
php -v
php -m
```

У списку модулів має бути `pdo_firebird`.

## 7. Налаштування PHP

Визначте активний `php.ini`:

```bash
php --ini
```

Рекомендовані початкові параметри:

```ini
memory_limit = 512M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 120
date.timezone = Europe/Kyiv
```

## 8. Composer

Установіть Composer за офіційною інструкцією:

- <https://getcomposer.org/download/>

Перевірте:

```bash
composer --version
```

## 9. Git

```bash
sudo apt install -y git
git --version
```

## 10. Firebird

Установіть Firebird 3.0.x або підготуйте мережеве підключення до наявного сервера.

Докладно: [Налаштування Firebird](../configuration/firebird.md).

## 11. Вебсервер

Оберіть один варіант.

### Apache

```bash
sudo apt install -y apache2
sudo systemctl status apache2
```

### Nginx

```bash
sudo apt install -y nginx
sudo systemctl status nginx
```

На цьому етапі достатньо встановити вебсервер. Віртуальні хости налаштовуються після ініціалізації порталу.

## 12. Перевірка

```bash
php -v
composer --version
git --version
```

Після успішної перевірки переходьте до документа [Отримання вихідного коду](repository.md).
