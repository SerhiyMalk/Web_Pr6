# Smart Grid Security API

![Node.js](https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white)
![Express.js](https://img.shields.io/badge/Express.js-404D59?style=for-the-badge)
![MySQL](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)
![Sequelize](https://img.shields.io/badge/Sequelize-52B0E7?style=for-the-badge&logo=Sequelize&logoColor=white)
![Security](https://img.shields.io/badge/Security-Passport.js-red?style=for-the-badge)

REST API сервер для порталу управління розумною енергомережею міста (Smart Grid) з реалізацією Role-Based Access Control (RBAC) та розширеними механізмами кібербезпеки. 

Проєкт розроблено в рамках практичної роботи з дисципліни **«Основи веб-програмування»**.

## Функціонал та рольова модель (RBAC)

У системі передбачено три типи користувачів із чітким розподілом обов'язків:
- **Оператор (`operator`)** — має доступ до реального моніторингу балансу розподіленої генерації та має право коригувати параметри навантаження мережі.
- **Аналітик (`analyst`)** — має доступ до перегляду балансу та може будувати прогнози на основі історичних даних.
- **Адміністратор (`administrator`)** — керує глобальною конфігурацією системи. Доступ суворо обмежений.

## Реалізовані механізми безпеки

Проєкт розроблено з урахуванням сучасних стандартів веббезпеки:
* **Session-based Authentication** — реалізована через `Passport.js` (Local Strategy). Сесійні куки захищені прапорцем `httpOnly` від XSS-атак.
* **Password Hashing** — використання стійкого алгоритму `bcrypt` (12 salt rounds).
* **SQL Injection Prevention** — використання ORM `Sequelize` та параметризованих запитів.
* **Data Validation & Sanitization** — вхідні дані проходять очищення (escape, trim) та перевірку через `express-validator`.
* **Rate Limiting (Anti Brute-force)** — обмеження спроб авторизації (максимум 5 спроб за 15 хвилин з однієї IP-адреси).
* **IP Whitelisting** — критично важливі ендпоінти перевіряють IP-адресу клієнта та блокують запити з неавторизованих мереж.
* **Mandatory Password Rotation** — контроль терміну дії пароля (вимога оновлення кожні 90 днів).

## API Endpoints

| Метод | URL | Ролі | Опис | Захист |
| :--- | :--- | :--- | :--- | :--- |
| `POST` | `/auth/register` | Усі | Реєстрація користувача | Validation, XSS Protect |
| `POST` | `/auth/login` | Усі | Вхід у систему | Rate limit, Session |
| `POST` | `/auth/logout` | Авторизовані | Вихід із системи | Auth check |
| `GET` | `/api/grid/balance` | `operator`, `analyst` | Моніторинг балансу | Auth, RBAC, Pass Rotation |
| `POST` | `/api/grid/adjust` | `operator` | Коригування навантаження| Auth, RBAC, Pass Rotation |
| `GET` | `/api/forecasts` | `analyst` | Отримання прогнозів | Auth, RBAC, Pass Rotation |
| `POST` | `/api/system/config` | `administrator` | Зміна конфігурації | Auth, RBAC, IP Whitelist |

