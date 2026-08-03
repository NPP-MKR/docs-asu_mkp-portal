<div align="center">
  <img src="../assets/mkp.png" alt="АСУ МКР" width="180">
</div>

# Документация АСУ МКР

Документация по установке, настройке и эксплуатации портала **АСУ МКР**.

## Разделы документации

### Установка

| Документ | Назначение |
|---|---|
| [Установка портала](installation/install.md) | Основная последовательность установки |
| [Подготовка сервера Linux](installation/server-linux.md) | Установка PHP, Composer, Git и веб-сервера |
| [Получение исходного кода](installation/repository.md) | SSH-доступ, выбор версии и клонирование репозитория |

### Настройка портала

| Документ | Назначение |
|---|---|
| [Настройка Firebird](configuration/firebird.md) | Подключение баз `BD` и `BDD` |
| [Файловое хранилище](configuration/storage.md) | Локальное и внешнее хранение файлов |

### Веб-сервер

| Документ | Назначение |
|---|---|
| [Apache](web-server/apache.md) | Настройка виртуальных хостов Apache |
| [Nginx](web-server/nginx.md) | Настройка серверных блоков Nginx |
| [HTTPS](web-server/ssl.md) | SSL-сертификаты и перенаправление на HTTPS |

### Эксплуатация

| Документ | Назначение |
|---|---|
| [Рекомендации](operations/recommendations.md) | Обновление, резервное копирование и безопасность |
| [Частые вопросы](operations/faq.md) | Типичные ошибки и способы устранения |
| [История изменений](operations/changelog.md) | Изменения документации |

## Версии портала

| Версия | Статус |
|---|---|
| **v2** | Актуальная стабильная версия, используемая в эксплуатации |
| **v3** | Новая версия портала, рекомендуемая для новых установок |

Подробная информация приведена в разделе [Получение исходного кода](installation/repository.md).

## Поддерживаемые платформы

| Операционная система | Статус |
|---|---|
| Ubuntu Server 22.04 LTS и выше | Рекомендуется |
| Debian 12 и выше | Рекомендуется |
| Windows Server | Поддерживается без отдельной инструкции по подготовке |

## Локализация

Русский язык является основным языком документации.

Украинская версия подключена и публикуется по адресу `/uk/`. Если отдельная страница еще не переведена, MkDocs автоматически отображает русскую версию.

Инструкция для переводчиков: [Локализация документации](localization.md).

## Официальная документация

| Компонент | Документация |
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
