# Мой вклад в проект

- Автор: **Родион Шабалин** (`Night565`)
- Командный репозиторий: [Vanady39/cluer](https://github.com/Vanady39/cluer)
- Стек моей части: Go, Gin, PostgreSQL, pgx, SQL, Docker Compose, unit- и integration-тесты

## Что я реализовал

### Code review и исследование решений

Помимо написания кода, я участвовал в проработке технических решений команды:

- проводил review pull request'ов и проверял соответствие кода принятой архитектуре;
- находил потенциальные ошибки в логике и формулировал замечания для авторов pull request'ов;
- исследовал варианты реализации поиска в PostgreSQL;
- сравнил полнотекстовый поиск, `pg_trgm` и простой поиск через `ILIKE`;
- для MVP предложил и реализовал простой параметризованный поиск через `ILIKE` без дополнительных сервисов и расширений PostgreSQL.

### Получение текущего пользователя

- доменная сущность пользователя;
- интерфейсы repository и service;
- моковый repository текущего пользователя;
- HTTP-handler `GET /v1/users/me` на Gin;
- response-модель;
- unit-тесты service и handler.

Исходные коммиты:

- [`ec602f0`](https://github.com/Vanady39/cluer/commit/ec602f0) — первая модель, mock и handler;
- [`9b1fb41`](https://github.com/Vanady39/cluer/commit/9b1fb41) — полный flow получения пользователя с Gin;
- [`162418b`](https://github.com/Vanady39/cluer/commit/162418b) — перенос контрактов service в domain;
- [`b432572`](https://github.com/Vanady39/cluer/commit/b432572) — выравнивание названий модулей;
- [`8818560`](https://github.com/Vanady39/cluer/commit/8818560) — unit-тесты.

### Получение объявлений

- сущность объявления;
- repository и service-контракты;
- моковый repository объявлений;
- HTTP-handler `GET /v1/listings`;
- response-модель;
- unit-тесты service и handler.

Исходные коммиты:

- [`4504893`](https://github.com/Vanady39/cluer/commit/4504893) — получение списка объявлений;
- [`6f3dee0`](https://github.com/Vanady39/cluer/commit/6f3dee0) — unit-тесты.

### PostgreSQL-поиск объявлений

- перенос объявлений из in-memory mock в PostgreSQL;
- миграция таблицы `listings`;
- PostgreSQL repository на `pgxpool`;
- регистронезависимый поиск по `title` и `description` через `ILIKE`;
- фильтр `q`, пагинация через `limit` и `offset`;
- валидация query-параметров;
- заполнение БД демонстрационными объявлениями;
- подключение demo-сервиса к PostgreSQL;
- интеграционный тест repository;
- обновление Swagger-контракта.

Исходный коммит:

- [`1a33390`](https://github.com/Vanady39/cluer/commit/1a33390dba049c7b07d33650a39cf48e5d61a0c0) — PostgreSQL listings search.

Основной SQL-запрос:

```sql
SELECT id, title, description, price, image_url
FROM listings
WHERE $1 = ''
   OR title ILIKE '%' || $1 || '%'
   OR description ILIKE '%' || $1 || '%'
ORDER BY id DESC
LIMIT $2 OFFSET $3;
```

API-контракт реализован в командном проекте в следующем виде:

```http
GET /v1/listings?q=iphone&limit=20&offset=0
```

- пустой `q` возвращает список объявлений без текстового фильтра;
- поиск не зависит от регистра;
- `limit` по умолчанию равен 20 и ограничен значением 50;
- `offset` по умолчанию равен 0;
- SQL использует параметры `$1`, `$2`, `$3`, а не конкатенацию пользовательского ввода в Go.

## Структура репозитория

```text
contributions/
├── current-user/                 # итоговые исходники модуля пользователя
├── listings-retrieval/           # исходники первоначального получения объявлений
└── postgresql-listings-search/   # новые файлы миграции, seed и integration-теста
```

Папка `contributions` позволяет просмотреть написанный мной код по отдельным функциям. Ссылки на оригинальные коммиты в командном репозитории приведены в соответствующих разделах README.

## Ограничения MVP

- поиск реализован через простое вхождение строки с `ILIKE`;
- полнотекстовый поиск, исправление опечаток и автодополнение не входят в мою реализацию;
- пользователь в моей части проекта является моковым;
- этот репозиторий предназначен для демонстрации личного вклада и не заменяет командный проект.
