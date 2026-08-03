# Налаштування Firebird

Версія документа: 1.0

## 1. Призначення

Портал використовує дві бази Firebird:

| Підключення | Призначення |
|---|---|
| `BD` | Основна база даних АСУ МКР |
| `BDD` | Додаткова база даних |

## 2. Вимоги

Рекомендована версія — **Firebird 3.0.x**.

Firebird може працювати на сервері порталу або на окремому сервері баз даних.

## 3. Установлення та служба

Установіть Firebird 3.0.x засобами операційної системи або офіційним пакетом.

Перевірте службу:

```bash
sudo systemctl status firebird3.0
```

Якщо ім'я відрізняється:

```bash
systemctl list-units --type=service | grep -i firebird
```

## 4. Розміщення баз

Зберігайте файли баз поза публічними каталогами вебсервера, наприклад:

```text
/var/lib/firebird/data/BD.FDB
/var/lib/firebird/data/BDD.FDB
```

!!! danger "Важливо"
    Не розміщуйте бази в `frontend/web`, `backend/web` або `api/web`.

## 5. Права доступу

Користувач служби Firebird повинен мати права читання й запису до файлів баз і каталогу.

```bash
ls -la /var/lib/firebird/data
```

Не використовуйте права `777`.

## 6. Мережевий доступ

Стандартний порт Firebird:

```text
3050/TCP
```

Якщо Firebird розміщено окремо, дозвольте підключення лише з сервера порталу та не публікуйте порт в Інтернеті без необхідності.

## 7. Перевірка підключення

Локальне підключення:

```bash
isql-fb /var/lib/firebird/data/BD.FDB -user SYSDBA -password '<пароль>'
```

Мережеве підключення:

```bash
isql-fb <сервер>:/var/lib/firebird/data/BD.FDB -user <користувач> -password '<пароль>'
```

Перевірте базу:

```sql
SHOW DATABASE;
```

## 8. Налаштування порталу

Підключення налаштовуються у файлі:

```text
common/config/main-local.php
```

Для кожної бази вкажіть адресу або шлях, користувача, пароль і кодування.

Приклад структури:

```php
'db' => [
    'class' => yii\db\Connection::class,
    'dsn' => 'firebird:dbname=<сервер>:<шлях_до_BD>;charset=UTF8',
    'username' => '<користувач>',
    'password' => '<пароль>',
],
```

!!! warning "Важливо"
    Фактичні імена компонентів `BD` і `BDD` мають відповідати конфігурації проєкту.

## 9. Резервне копіювання

Приклад із `gbak`:

```bash
gbak -b -user <користувач> -password '<пароль>' \
    /var/lib/firebird/data/BD.FDB \
    /backup/BD.fbk
```

Регулярно перевіряйте відновлення резервних копій на тестовому сервері.

## 10. Типові помилки

### could not find driver

Перевірте:

```bash
php -m | grep -i firebird
```

### Unable to complete network request

Перевірте адресу, порт 3050, службу Firebird, міжмережевий екран і шлях до бази.

### Permission denied

Перевірте права користувача служби Firebird.

## 11. Офіційна документація

- [Firebird Documentation](https://firebirdsql.org/en/documentation/)
