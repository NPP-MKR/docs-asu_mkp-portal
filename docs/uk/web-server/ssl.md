# Налаштування HTTPS

Версія документа: 1.0

## 1. Вимоги

HTTPS є обов'язковим для Frontend, Backend і API.

Перед випуском сертифіката:

1. Створіть DNS-записи.
2. Переконайтеся, що домени вказують на сервер.
3. Налаштуйте віртуальні хости.
4. Перевірте HTTP.
5. Відкрийте порти 80 і 443.

## 2. Let's Encrypt

Для Apache:

```bash
sudo certbot --apache \
    -d portal.example.com \
    -d admin.example.com \
    -d api.example.com
```

Для Nginx:

```bash
sudo certbot --nginx \
    -d portal.example.com \
    -d admin.example.com \
    -d api.example.com
```

## 3. Комерційний сертифікат

Потрібні сертифікат, приватний ключ і проміжні сертифікати. Приватний ключ має бути доступний лише адміністратору та вебсерверу.

## 4. Перенаправлення

Після налаштування сертифікатів перенаправляйте HTTP на HTTPS.

Не вимикайте порт 80, якщо він потрібен для автоматичного продовження Let's Encrypt.

## 5. Перевірка

Відкрийте:

```text
https://portal.example.com
https://admin.example.com
https://api.example.com
```

Перевірте домен, строк дії, ланцюжок сертифікатів і перенаправлення.

## 6. Автоматичне продовження

```bash
systemctl status certbot.timer
sudo certbot renew --dry-run
```

## 7. Типові помилки

- сертифікат не відповідає домену — перевірте `ServerName` або `server_name`;
- випуск не виконується — перевірте DNS і порти 80/443;
- неповний ланцюжок — установіть проміжні сертифікати.

## 8. Офіційна документація

- [Let's Encrypt](https://letsencrypt.org/docs/)
- [Certbot](https://certbot.eff.org/)
