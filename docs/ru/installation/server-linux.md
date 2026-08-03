# Подготовка сервера Linux

Версия документа: 1.0

## 1. Назначение

Документ описывает подготовку сервера под управлением Ubuntu Server или Debian для последующей установки портала АСУ МКР.

Подготовка Windows Server допускается, но в данной документации не рассматривается.

## 2. Поддерживаемые системы

| Операционная система | Версия |
|---|---|
| Ubuntu Server | 22.04 LTS и выше |
| Debian | 12 и выше |

## 3. Перед началом

Для установки потребуется:

- учетная запись с правами `sudo`;
- доступ сервера к Интернету;
- выбранное доменное имя;
- доступ к DNS;
- подготовленные базы Firebird либо сведения для подключения к ним.

!!! warning "Важно"
    Команды выполняйте последовательно. Перед переходом к следующему этапу убедитесь, что предыдущая команда завершилась без ошибок.

## 4. Обновление системы

Сначала обновите сведения о доступных пакетах:

```bash
sudo apt update
```

Затем установите доступные обновления:

```bash
sudo apt upgrade -y
```

При обновлении ядра или системных библиотек рекомендуется перезагрузить сервер:

```bash
sudo reboot
```

После повторного подключения проверьте систему:

```bash
uname -a
```

## 5. Установка базовых утилит

Установите инструменты, которые понадобятся при дальнейшей настройке:

```bash
sudo apt install -y     ca-certificates     curl     unzip     gnupg     lsb-release     software-properties-common
```

## 6. Установка PHP

Порталу требуется PHP 8.1 и выше. Рекомендуется PHP 8.3.

Установите PHP и необходимые расширения из репозитория, используемого вашей операционной системой.

Пример для пакетов PHP 8.3:

```bash
sudo apt install -y     php8.3     php8.3-cli     php8.3-bcmath     php8.3-curl     php8.3-gd     php8.3-gettext     php8.3-imap     php8.3-intl     php8.3-mbstring     php8.3-xml     php8.3-zip
```

Также необходимо установить драйвер Firebird для PHP:

```bash
sudo apt install -y php8.3-interbase
```

В некоторых репозиториях пакет может называться иначе. После установки обязательно проверьте наличие модулей `pdo_firebird` и `interbase`.

Проверьте версию PHP:

```bash
php -v
```

Проверьте обязательные расширения:

```bash
php -m
```

В выводе должны присутствовать, в частности:

```text
bcmath
curl
dom
fileinfo
gd
gettext
iconv
imap
intl
mbstring
openssl
PDO
pdo_firebird
SimpleXML
xml
zip
```

## 7. Настройка PHP

Определите расположение используемого файла конфигурации:

```bash
php --ini
```

Для веб-сервера может использоваться отдельный файл `php.ini`.

Рекомендуется проверить параметры:

```ini
memory_limit = 512M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 120
date.timezone = Europe/Kyiv
```

Значения могут быть изменены в соответствии с нагрузкой и требованиями организации.

## 8. Установка Composer

Скачайте установщик Composer с официального сайта и выполните установку согласно официальной инструкции:

- <https://getcomposer.org/download/>

После установки проверьте версию:

```bash
composer --version
```

Требуется Composer 2.6 и выше.

## 9. Установка Git

Установите Git:

```bash
sudo apt install -y git
```

Проверьте версию:

```bash
git --version
```

## 10. Установка Firebird

Установите Firebird 3.0.x либо подготовьте сетевое подключение к уже существующему серверу Firebird.

Подробная инструкция приведена в документе [Настройка Firebird](../configuration/firebird.md).

## 11. Установка веб-сервера

Выберите один веб-сервер.

### Apache

```bash
sudo apt install -y apache2
```

Проверьте состояние службы:

```bash
sudo systemctl status apache2
```

Подробная настройка: [Настройка Apache](../web-server/apache.md).

### Nginx

```bash
sudo apt install -y nginx
```

Проверьте состояние службы:

```bash
sudo systemctl status nginx
```

Подробная настройка: [Настройка Nginx](../web-server/nginx.md).

!!! note "Примечание"
    На этом этапе достаточно установить веб-сервер. Виртуальные хосты настраиваются после получения и инициализации портала.

## 12. Проверка готовности сервера

Проверьте основные компоненты:

```bash
php -v
composer --version
git --version
```

Проверьте работу выбранного веб-сервера:

```bash
sudo systemctl status apache2
```

или:

```bash
sudo systemctl status nginx
```

После успешной проверки переходите к документу [Получение исходного кода](repository.md).

## 13. Официальная документация

- [Ubuntu Server](https://ubuntu.com/server/docs)
- [Debian](https://www.debian.org/doc/)
- [PHP](https://www.php.net/docs.php)
- [Composer](https://getcomposer.org/doc/)
- [Git](https://git-scm.com/doc)
