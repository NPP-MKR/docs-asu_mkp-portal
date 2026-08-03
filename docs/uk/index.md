<div align="center">
  <img src="../assets/mkp.png" alt="АСУ МКР" width="180">
</div>

# Документація АСУ МКР

Документація з установлення, налаштування та експлуатації порталу **АСУ МКР**.

## Розділи документації

### Установлення

| Документ | Призначення |
|---|---|
| [Установлення порталу](installation/install.md) | Основна послідовність установлення |
| [Підготовка сервера Linux](installation/server-linux.md) | Установлення PHP, Composer, Git і вебсервера |
| [Отримання вихідного коду](installation/repository.md) | SSH-доступ, вибір версії та клонування репозиторію |

### Налаштування порталу

| Документ | Призначення |
|---|---|
| [Налаштування Firebird](configuration/firebird.md) | Підключення баз `BD` і `BDD` |
| [Файлове сховище](configuration/storage.md) | Локальне та зовнішнє зберігання файлів |

### Вебсервер

| Документ | Призначення |
|---|---|
| [Apache](web-server/apache.md) | Налаштування віртуальних хостів Apache |
| [Nginx](web-server/nginx.md) | Налаштування серверних блоків Nginx |
| [HTTPS](web-server/ssl.md) | SSL-сертифікати та перенаправлення на HTTPS |

### Експлуатація

| Документ | Призначення |
|---|---|
| [Рекомендації](operations/recommendations.md) | Оновлення, резервне копіювання та безпека |
| [Часті запитання](operations/faq.md) | Типові помилки та способи усунення |
| [Історія змін](operations/changelog.md) | Зміни документації |

## Версії порталу

| Версія | Статус |
|---|---|
| **v2** | Актуальна стабільна версія, що використовується в експлуатації |
| **v3** | Нова версія порталу, рекомендована для нових установлень |

Докладна інформація наведена в розділі [Отримання вихідного коду](installation/repository.md).

## Підтримувані платформи

| Операційна система | Статус |
|---|---|
| Ubuntu Server 22.04 LTS і вище | Рекомендовано |
| Debian 12 і вище | Рекомендовано |
| Windows Server | Підтримується без окремої інструкції з підготовки |

## Локалізація

Українська мова є основною мовою документації.

Російська версія доступна через перемикач мови та публікується за адресою `/ru/`.

Інструкція для перекладачів: [Локалізація документації](localization.md).

## Офіційна документація

| Компонент | Документація |
|---|---|
| PHP | [php.net](https://www.php.net/docs.php) |
| Composer | [getcomposer.org](https://getcomposer.org/doc/) |
| Git | [git-scm.com](https://git-scm.com/doc) |
| GitHub | [docs.github.com](https://docs.github.com/) |
| Firebird | [firebirdsql.org](https://firebirdsql.org/en/documentation/) |
| Apache HTTP Server | [httpd.apache.org](https://httpd.apache.org/docs/) |
| Nginx | [nginx.org](https://nginx.org/en/docs/) |
| Let's Encrypt | [letsencrypt.org](https://letsencrypt.org/docs/) |
| Certbot | [certbot.eff.org](https://certbot.eff.org/) |
