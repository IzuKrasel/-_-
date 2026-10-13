# Техническая документация

## Разработка информационной системы «Конференции.РФ»

**Демонстрационный экзамен 2027, специальность 09.02.07, квалификация «Разработчик веб и мультимедийных приложений»**

---

## Оглавление

1. Общие сведения
2. Архитектура решения
3. Требования к окружению
4. Последовательность сборки проекта
5. Структура проекта
6. Проектирование базы данных
7. Серверная часть на Python (Flask)
8. Клиентская часть: HTML5
9. Клиентская часть: CSS3
10. Клиентская часть: JavaScript
11. Атрибуты `data-testid` для автопроверки
12. Модуль 4: REST API
13. Развёртывание, запуск и упаковка
14. Контрольный список перед сдачей

---

## 1. Общие сведения

Настоящая документация описывает процесс разработки информационной системы «Конференции.РФ» — веб-портала бронирования помещений для проведения всероссийских конференций. Документ содержит описание архитектуры, схемы базы данных, примеры программного кода на языках Python, HTML, CSS, JavaScript и SQL, а также порядок выполнения работ по всем четырём модулям демонстрационного экзамена.

Функциональные возможности системы:

- регистрация и авторизация пользователей;
- создание заявок на бронирование помещений;
- просмотр собственных заявок с возможностью оставить отзыв;
- панель администратора с управлением статусами заявок;
- REST API для выдачи данных из внешней базы данных (Модуль 4).

---

## 2. Архитектура решения

Система построена по принципу «полного стека» (full stack) и включает три слоя:

1. **Слой представления (frontend)** — HTML5-шаблоны, каскадные таблицы стилей, JavaScript.
2. **Слой бизнес-логики (backend)** — Flask-приложение на Python.
3. **Слой хранения данных (database)** — реляционная СУБД SQLite (в Модуле 4 — внешняя БД `shop_db.sql`).

```
Браузер (HTML/CSS/JS)
        │  HTTP-запрос
        ▼
Flask (Python) — маршруты, валидация, сессии
        │  SQL-запрос
        ▼
SQLite (database.db)  /  PostgreSQL  /  MySQL
```

Принятый стек: **Python 3.12 + Flask 3.0 + SQLite + HTML5 + CSS3 + JavaScript ES2017**. Замена SQLite на PostgreSQL выполняется изменением строки подключения без переработки логики.

---

## 3. Требования к окружению

### 3.1. Программное обеспечение

- Python 3.12 или выше;
- pip (менеджер пакетов Python);
- веб-браузер Google Chrome / Chromium или Mozilla Firefox;
- текстовый редактор Visual Studio Code или PyCharm;
- программа «Автотест» (для проверки заданий);
- 7-Zip (для упаковки архивов);
- Git (для контроля версий).

### 3.2. Разрешённые технологии (по КИМ)

Языки и среды выполнения: Node.js 22, PHP 8.3, Python 3.12+, .NET SDK 8, Go 1.22.

СУБД: PostgreSQL, MySQL, SQLite, MS SQL Server.

Фреймворки: React, Next.js, Vue.js, Angular, jQuery, Bootstrap, Tailwind CSS, Express, Laravel, Django, Flask, FastAPI, ASP.NET Core, Sequelize, Prisma, Drizzle ORM.

### 3.3. Установка зависимостей

Файл `requirements.txt`:

```text
Flask==3.0.0
```

Команда установки:

```bash
pip install -r requirements.txt
```

---

## 4. Последовательность сборки проекта

Работы выполняются в следующем порядке.

### Шаг 1. Создание рабочей папки и виртуального окружения

```bash
mkdir project
cd project
python -m venv venv
```

Активация окружения:

```bash
# Windows:
venv\Scripts\activate

# Linux / macOS:
source venv/bin/activate
```

### Шаг 2. Установка Flask

```bash
pip install Flask==3.0.0
pip freeze > requirements.txt
```

### Шаг 3. Создание структуры папок

```bash
mkdir templates
mkdir static
mkdir static/css
mkdir static/js
mkdir static/fonts
mkdir static/img
```

### Шаг 4. Размещение ресурсов

- Скопировать в `static/fonts/` три файла шрифта PT Sans: `PTSans-Regular.ttf`, `PTSans-Bold.ttf`, `PTSans-Italic.ttf`.
- Скопировать в `static/img/` файл `logo.jpg` (из комплекта `Прил_1_ОЗ_КИМ_09.02.07-3-2027.zip`).

### Шаг 5. Создание `app.py`

Файл собирается из разделов 7.2–7.11 в указанном порядке:

1. Импорты и инициализация приложения.
2. Вспомогательные функции `db()` и `hash_pwd()`.
3. Функция `init_db()`.
4. Маршруты: `/`, `/register`, `/login`, `/logout`, `/requests`, `/requests/create`, `/requests/<id>/review`, `/admin`, `/admin/<id>/status`.
5. Блок `if __name__ == '__main__'`.

### Шаг 6. Создание HTML-шаблонов

В папке `templates/` создаются пять файлов из раздела 8.

### Шаг 7. Создание CSS

В `static/css/style.css` помещается полный код из раздела 9.

### Шаг 8. Создание JavaScript

В `static/js/validation.js` помещается полный код из раздела 10.

### Шаг 9. Первый запуск

```bash
python app.py
```

При старте выполнится `init_db()`, создастся файл `database.db`, добавятся статусы, помещения и учётная запись администратора `Conf2027 / Demo77`. Открыть в браузере `http://127.0.0.1:8080/register`.

### Шаг 10. Инициализация Git

```bash
git init
git add .
git commit -m "Начальная структура проекта Конференции.РФ"
```

### Шаг 11. Упаковка проекта

Правой кнопкой мыши по папке → «Отправить» → «Сжатая ZIP-папка». Переименовать архив в `project_1.zip` (для Модуля 1) или `project_3.zip` (для Модуля 3).

---

## 5. Структура проекта

```
project/
├── app.py                    # Flask-приложение (точка входа)
├── database.db               # Файл БД SQLite (создаётся автоматически)
├── shop_db.sql               # Дамп БД для Модуля 4
├── requirements.txt          # Зависимости Python
├── templates/                # HTML-шаблоны Jinja2
│   ├── register.html
│   ├── login.html
│   ├── requests.html
│   ├── request_create.html
│   └── admin.html
└── static/
    ├── css/
    │   └── style.css
    ├── js/
    │   └── validation.js
    ├── fonts/
    │   ├── PTSans-Regular.ttf
    │   ├── PTSans-Bold.ttf
    │   └── PTSans-Italic.ttf
    └── img/
        └── logo.jpg
```

---

## 6. Проектирование базы данных

### 6.1. Перечень сущностей

1. `users` — пользователи (обычные и администратор).
2. `rooms` — перечень помещений.
3. `statuses` — справочник статусов заявок.
4. `requests` — заявки на бронирование.
5. `reviews` — отзывы о проведённых мероприятиях.

### 6.2. Описание таблиц

**Таблица `users`**

| Поле | Тип | Ограничение | Назначение |
|------|-----|-------------|------------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | Идентификатор |
| login | TEXT | UNIQUE NOT NULL | Уникальный логин |
| password | TEXT | NOT NULL | Хеш пароля (SHA-256) |
| fullname | TEXT | NOT NULL | ФИО |
| phone | TEXT | NOT NULL | Телефон в формате 8(XXX)XXX-XX-XX |
| email | TEXT | NOT NULL | Электронная почта |
| role | TEXT | NOT NULL DEFAULT 'user' | Роль: user или admin |

**Таблица `rooms`**

| Поле | Тип | Ограничение | Назначение |
|------|-----|-------------|------------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | Идентификатор |
| name | TEXT | NOT NULL | Название помещения |
| type | TEXT | NOT NULL | Тип: аудитория, коворкинг, кинозал |

**Таблица `statuses`**

| Поле | Тип | Ограничение | Назначение |
|------|-----|-------------|------------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | Идентификатор |
| name | TEXT | NOT NULL | Название статуса |

Значения: «Новая», «Мероприятие назначено», «Завершено».

**Таблица `requests`**

| Поле | Тип | Ограничение | Назначение |
|------|-----|-------------|------------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | Идентификатор |
| user_id | INTEGER | NOT NULL | Ссылка на users.id |
| room_id | INTEGER | NOT NULL | Ссылка на rooms.id |
| status_id | INTEGER | NOT NULL | Ссылка на statuses.id |
| start_date | TEXT | NOT NULL | Дата начала (ISO 8601) |
| payment | TEXT | NOT NULL | Способ оплаты |

**Таблица `reviews`**

| Поле | Тип | Ограничение | Назначение |
|------|-----|-------------|------------|
| id | INTEGER | PRIMARY KEY AUTOINCREMENT | Идентификатор |
| request_id | INTEGER | UNIQUE NOT NULL | Ссылка на requests.id |
| text | TEXT | NOT NULL | Текст отзыва |
| rating | INTEGER | NOT NULL | Оценка (1–5) |

### 6.3. Связи

- `users` 1 → ∞ `requests`
- `rooms` 1 → ∞ `requests`
- `statuses` 1 → ∞ `requests`
- `requests` 1 → 1 `reviews`

### 6.4. ER-диаграмма

Строится в draw.io, Penpot или StarUML, сохраняется в папке проекта (формат `.png`, `.pdf` или файл редактора) и прикладывается к заданию 3.7.

### 6.5. SQL-скрипт создания схемы

```sql
CREATE TABLE IF NOT EXISTS users(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    login TEXT UNIQUE NOT NULL,
    password TEXT NOT NULL,
    fullname TEXT NOT NULL,
    phone TEXT NOT NULL,
    email TEXT NOT NULL,
    role TEXT NOT NULL DEFAULT 'user'
);

CREATE TABLE IF NOT EXISTS rooms(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    type TEXT NOT NULL
);

CREATE TABLE IF NOT EXISTS statuses(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL
);

CREATE TABLE IF NOT EXISTS requests(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    user_id INTEGER NOT NULL,
    room_id INTEGER NOT NULL,
    status_id INTEGER NOT NULL,
    start_date TEXT NOT NULL,
    payment TEXT NOT NULL
);

CREATE TABLE IF NOT EXISTS reviews(
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    request_id INTEGER UNIQUE NOT NULL,
    text TEXT NOT NULL,
    rating INTEGER NOT NULL
);
```

Начальное заполнение справочников и создание администратора выполняются в `init_db()` (см. раздел 7.5).

---

## 7. Серверная часть на Python (Flask)

### 7.1. Назначение

Модуль `app.py` реализует бизнес-логику: маршрутизацию HTTP-запросов, валидацию данных, работу с сессиями, взаимодействие с базой данных.

### 7.2. Инициализация приложения

```python
from flask import Flask, render_template, request, redirect, url_for, session
import sqlite3, re, hashlib, os

app = Flask(__name__)
app.secret_key = 'conf2027'
DB = os.path.join(os.path.dirname(__file__), 'database.db')
```

`app.secret_key` используется для подписи cookie-сессий. Константа `DB` задаёт абсолютный путь к файлу базы данных.

### 7.3. Подключение к базе данных

```python
def db():
    c = sqlite3.connect(DB)
    c.row_factory = sqlite3.Row
    return c
```

`row_factory = sqlite3.Row` позволяет обращаться к полям выборки по имени.

### 7.4. Хеширование паролей

```python
def hash_pwd(p):
    return hashlib.sha256(p.encode()).hexdigest()
```

Пароль хранится в виде SHA-256-хеша.

### 7.5. Инициализация структуры БД

```python
def init_db():
    c = db()

    c.execute("""CREATE TABLE IF NOT EXISTS users(
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        login TEXT UNIQUE NOT NULL,
        password TEXT NOT NULL,
        fullname TEXT NOT NULL,
        phone TEXT NOT NULL,
        email TEXT NOT NULL,
        role TEXT NOT NULL DEFAULT 'user')""")

    c.execute("""CREATE TABLE IF NOT EXISTS rooms(
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        type TEXT NOT NULL)""")

    c.execute("""CREATE TABLE IF NOT EXISTS statuses(
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL)""")

    c.execute("""CREATE TABLE IF NOT EXISTS requests(
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        user_id INTEGER NOT NULL,
        room_id INTEGER NOT NULL,
        status_id INTEGER NOT NULL,
        start_date TEXT NOT NULL,
        payment TEXT NOT NULL)""")

    c.execute("""CREATE TABLE IF NOT EXISTS reviews(
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        request_id INTEGER UNIQUE NOT NULL,
        text TEXT NOT NULL,
        rating INTEGER NOT NULL)""")

    if c.execute("SELECT COUNT(*) FROM statuses").fetchone()[0] == 0:
        c.executemany("INSERT INTO statuses(name) VALUES(?)",
                      [('Новая',), ('Мероприятие назначено',), ('Завершено',)])

    if c.execute("SELECT COUNT(*) FROM rooms").fetchone()[0] == 0:
        c.executemany("INSERT INTO rooms(name,type) VALUES(?,?)", [
            ('Аудитория №1', 'аудитория'),
            ('Аудитория №2', 'аудитория'),
            ('Коворкинг «Точка кипения»', 'коворкинг'),
            ('Кинозал', 'кинозал'),
        ])

    if c.execute("SELECT COUNT(*) FROM users WHERE login='Conf2027'").fetchone()[0] == 0:
        c.execute("INSERT INTO users(login,password,fullname,phone,email,role)"
                  " VALUES(?,?,?,?,?,?)",
                  ('Conf2027', hash_pwd('Demo77'), 'Администратор Портал',
                   '8(000)000-00-00', 'admin@conf.rf', 'admin'))

    c.commit()
    c.close()
```

### 7.6. Маршрут регистрации

Правила валидации:

| Поле | Регулярное выражение | Сообщение об ошибке |
|------|---------------------|---------------------|
| Логин | `[A-Za-z0-9]{6,}` | Логин: только латиница и цифры, минимум 6 символов |
| Пароль | длина ≥ 8 | Пароль: минимум 8 символов |
| ФИО | `[А-Яа-яЁё\s]+` | ФИО: только кириллица и пробелы |
| Телефон | `8\(\d{3}\)\d{3}-\d{2}-\d{2}` | Телефон: формат 8(XXX)XXX-XX-XX |
| Email | `[^@\s]+@[^@\s]+\.[^@\s]+` | Email: некорректный формат |

```python
@app.route('/register', methods=['GET', 'POST'])
def register():
    errors = []
    if request.method == 'POST':
        login    = request.form.get('login', '').strip()
        password = request.form.get('password', '')
        fullname = request.form.get('fullname', '').strip()
        phone    = request.form.get('phone', '').strip()
        email    = request.form.get('email', '').strip()

        if not re.fullmatch(r'[A-Za-z0-9]{6,}', login):
            errors.append('Логин: только латиница и цифры, минимум 6 символов')
        if len(password) < 8:
            errors.append('Пароль: минимум 8 символов')
        if not re.fullmatch(r'[А-Яа-яЁё\s]+', fullname):
            errors.append('ФИО: только кириллица и пробелы')
        if not re.fullmatch(r'8\(\d{3}\)\d{3}-\d{2}-\d{2}', phone):
            errors.append('Телефон: формат 8(XXX)XXX-XX-XX')
        if not re.fullmatch(r'[^@\s]+@[^@\s]+\.[^@\s]+', email):
            errors.append('Email: некорректный формат')

        if not errors:
            c = db()
            try:
                c.execute("INSERT INTO users(login,password,fullname,phone,email)"
                          " VALUES(?,?,?,?,?)",
                          (login, hash_pwd(password), fullname, phone, email))
                c.commit()
                c.close()
                return redirect('/login')
            except sqlite3.IntegrityError:
                errors.append('Логин уже занят')
                c.close()

    return render_template('register.html', errors=errors)
```

### 7.7. Маршрут авторизации

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    errors = []
    if request.method == 'POST':
        login    = request.form.get('login', '').strip()
        password = request.form.get('password', '')

        c = db()
        user = c.execute("SELECT * FROM users WHERE login=?", (login,)).fetchone()
        c.close()

        if user and user['password'] == hash_pwd(password):
            session['user_id'] = user['id']
            session['login']   = user['login']
            session['role']    = user['role']

            if user['role'] == 'admin':
                return redirect('/admin')
            return redirect('/requests')

        errors.append('Неверный логин или пароль')

    return render_template('login.html', errors=errors)


@app.route('/logout')
def logout():
    session.clear()
    return redirect('/login')
```

После успешной авторизации администратор направляется на `/admin`, обычный пользователь — на `/requests`.

### 7.8. Маршрут просмотра заявок

```python
@app.route('/requests')
def requests_list():
    if 'user_id' not in session:
        return redirect('/login')

    c = db()
    rows = c.execute("""
        SELECT r.id, r.start_date, r.payment,
               rm.name AS room_name,
               st.name AS status_name,
               rv.text AS review_text,
               rv.rating AS review_rating
        FROM requests r
        JOIN rooms rm ON rm.id = r.room_id
        JOIN statuses st ON st.id = r.status_id
        LEFT JOIN reviews rv ON rv.request_id = r.id
        WHERE r.user_id = ?
        ORDER BY r.id DESC
    """, (session['user_id'],)).fetchall()
    c.close()

    return render_template('requests.html', requests=[dict(x) for x in rows])
```

Конструкция `LEFT JOIN reviews` позволяет получить отзыв, если он оставлен; иначе поля остаются пустыми.

### 7.9. Маршрут создания заявки

```python
@app.route('/requests/create', methods=['GET', 'POST'])
def request_create():
    if 'user_id' not in session:
        return redirect('/login')

    errors = []
    c = db()

    if request.method == 'POST':
        room  = request.form.get('room', '').strip()
        date  = request.form.get('start_date', '').strip()
        pay   = request.form.get('payment', '').strip()

        if not room: errors.append('Введите название помещения')
        if not date: errors.append('Введите дату начала')
        if pay not in ('очное', 'СБП'): errors.append('Выберите способ оплаты')

        if not errors:
            r = c.execute("SELECT id FROM rooms WHERE name=?", (room,)).fetchone()
            if not r:
                errors.append('Помещение не найдено')
            else:
                s = c.execute("SELECT id FROM statuses WHERE name='Новая'").fetchone()
                c.execute("INSERT INTO requests(user_id,room_id,status_id,start_date,payment)"
                          " VALUES(?,?,?,?,?)",
                          (session['user_id'], r['id'], s['id'], date, pay))
                c.commit()
                c.close()
                return redirect('/requests')

    rooms = c.execute("SELECT name FROM rooms ORDER BY name").fetchall()
    c.close()

    return render_template('request_create.html', errors=errors,
                           rooms=[x['name'] for x in rooms])
```

### 7.10. Маршрут добавления отзыва

Отзыв доступен только для заявок со статусом «Завершено».

```python
@app.route('/requests/<int:rid>/review', methods=['POST'])
def add_review(rid):
    if 'user_id' not in session:
        return redirect('/login')

    text = request.form.get('text', '').strip()
    rat  = request.form.get('rating', '').strip()

    c = db()
    row = c.execute("""SELECT st.name AS s FROM requests r
                       JOIN statuses st ON st.id=r.status_id
                       WHERE r.id=? AND r.user_id=?""",
                    (rid, session['user_id'])).fetchone()

    if row and row['s'] == 'Завершено' and text and rat.isdigit() and 1 <= int(rat) <= 5:
        try:
            c.execute("INSERT INTO reviews(request_id,text,rating) VALUES(?,?,?)",
                      (rid, text, int(rat)))
        except sqlite3.IntegrityError:
            c.execute("UPDATE reviews SET text=?, rating=? WHERE request_id=?",
                      (text, int(rat), rid))
        c.commit()
    c.close()
    return redirect('/requests')
```

### 7.11. Панель администратора

```python
@app.route('/admin')
def admin_panel():
    if session.get('role') != 'admin':
        return redirect('/login')

    c = db()
    rows = c.execute("""
        SELECT r.id, r.start_date, r.payment,
               u.login     AS user_login,
               u.fullname  AS user_name,
               rm.name     AS room_name,
               st.id       AS status_id,
               st.name     AS status_name
        FROM requests r
        JOIN users u    ON u.id = r.user_id
        JOIN rooms rm   ON rm.id = r.room_id
        JOIN statuses st ON st.id = r.status_id
        ORDER BY r.id DESC
    """).fetchall()

    statuses = c.execute("SELECT id, name FROM statuses ORDER BY id").fetchall()
    c.close()

    return render_template('admin.html',
                           requests=[dict(x) for x in rows],
                           statuses=[dict(x) for x in statuses])


@app.route('/admin/<int:rid>/status', methods=['POST'])
def admin_change_status(rid):
    if session.get('role') != 'admin':
        return redirect('/login')

    new_status = request.form.get('status_id', '').strip()

    if new_status.isdigit():
        c = db()
        c.execute("UPDATE requests SET status_id=? WHERE id=?",
                  (int(new_status), rid))
        c.commit()
        c.close()

    return redirect('/admin')
```

### 7.12. Точка входа

```python
if __name__ == '__main__':
    init_db()
    app.run(host='127.0.0.1', port=8080, debug=True)
```

Приложение слушает порт 8080.

---

## 8. Клиентская часть: HTML5

### 8.1. Общие требования

- Документ начинается с `<!DOCTYPE html>`.
- Атрибут `lang="ru"` у тега `<html>`.
- Используется семантическая разметка: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`.
- Все поля форм сопровождаются атрибутами `data-testid` (раздел 11).
- Кодировка UTF-8.

### 8.2. Шаблон регистрации `templates/register.html`

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Регистрация — Конференции.РФ</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <main class="auth-container">
        <img src="/static/img/logo.jpg" alt="Конференции.РФ" class="auth-logo">
        <h1>Регистрация</h1>

        {% if errors %}
            <div class="errors" data-testid="reg-errors">
                <ul>
                    {% for error in errors %}
                        <li>{{ error }}</li>
                    {% endfor %}
                </ul>
            </div>
        {% endif %}

        <form method="POST" action="/register" id="register-form" novalidate>
            <label for="login">Логин</label>
            <input type="text" id="login" name="login"
                   data-testid="reg-login"
                   placeholder="Латинские буквы и цифры, минимум 6"
                   required>

            <label for="password">Пароль</label>
            <input type="password" id="password" name="password"
                   data-testid="reg-password"
                   placeholder="Минимум 8 символов"
                   required>

            <label for="fullname">ФИО</label>
            <input type="text" id="fullname" name="fullname"
                   data-testid="reg-fullname"
                   placeholder="Иванов Иван Иванович"
                   required>

            <label for="phone">Телефон</label>
            <input type="tel" id="phone" name="phone"
                   data-testid="reg-phone"
                   placeholder="8(999)123-45-67"
                   required>

            <label for="email">E-mail</label>
            <input type="email" id="email" name="email"
                   data-testid="reg-email"
                   placeholder="user@example.com"
                   required>

            <button type="submit" data-testid="reg-submit">Создать пользователя</button>
        </form>

        <p class="switch-link">
            Уже зарегистрированы? <a href="/login">Войти</a>
        </p>
    </main>

    <script src="/static/js/validation.js"></script>
</body>
</html>
```

### 8.3. Шаблон авторизации `templates/login.html`

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Вход — Конференции.РФ</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <main class="auth-container">
        <img src="/static/img/logo.jpg" alt="Конференции.РФ" class="auth-logo">
        <h1>Вход</h1>

        {% if errors %}
            <div class="errors" data-testid="login-errors">
                <ul>
                    {% for error in errors %}
                        <li>{{ error }}</li>
                    {% endfor %}
                </ul>
            </div>
        {% endif %}

        <form method="POST" action="/login" id="login-form" novalidate>
            <label for="login">Логин</label>
            <input type="text" id="login" name="login"
                   data-testid="login-login" required>

            <label for="password">Пароль</label>
            <input type="password" id="password" name="password"
                   data-testid="login-password" required>

            <button type="submit" data-testid="login-submit">Войти</button>
        </form>

        <p class="switch-link">
            Еще не зарегистрированы? <a href="/register">Регистрация</a>
        </p>
    </main>

    <script src="/static/js/validation.js"></script>
</body>
</html>
```

### 8.4. Шаблон создания заявки `templates/request_create.html`

Поле «Помещение» — текстовое поле с подсказками через `<datalist>`, что удовлетворяет требованию задания 3.2.

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Создание заявки — Конференции.РФ</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <main class="auth-container">
        <img src="/static/img/logo.jpg" alt="Конференции.РФ" class="auth-logo">
        <h1>Создание заявки</h1>

        {% if errors %}
            <div class="errors" data-testid="req-errors">
                <ul>
                    {% for e in errors %}<li>{{ e }}</li>{% endfor %}
                </ul>
            </div>
        {% endif %}

        <form method="POST" action="/requests/create" id="request-form" novalidate>
            <label for="room">Помещение</label>
            <input type="text" id="room" name="room"
                   list="rooms-list"
                   data-testid="req-room"
                   placeholder="Начните вводить название"
                   required>
            <datalist id="rooms-list">
                {% for room in rooms %}
                    <option value="{{ room }}"></option>
                {% endfor %}
            </datalist>

            <label for="start_date">Дата начала</label>
            <input type="date" id="start_date" name="start_date"
                   data-testid="req-date" required>

            <label for="payment">Способ оплаты</label>
            <select id="payment" name="payment"
                    data-testid="req-payment" required>
                <option value="">- выберите -</option>
                <option value="очное">Очное посещение</option>
                <option value="СБП">Перевод по СБП</option>
            </select>

            <button type="submit" data-testid="req-submit">Отправить</button>
        </form>

        <p class="switch-link">
            <a href="/requests">К моим заявкам</a>
        </p>
    </main>
</body>
</html>
```

### 8.5. Шаблон списка заявок `templates/requests.html`

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Мои заявки — Конференции.РФ</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <main class="auth-container wide">
        <img src="/static/img/logo.jpg" alt="Конференции.РФ" class="auth-logo">
        <h1>Мои заявки</h1>

        <p class="switch-link">
            <a href="/requests/create">+ Создать заявку</a> |
            <a href="/logout">Выйти</a>
        </p>

        {% if not requests %}
            <p class="empty">У вас пока нет заявок.</p>
        {% else %}
            <div data-testid="req-list" class="requests-list">
                {% for r in requests %}
                    <article class="request-card">
                        <h3>{{ r.room_name }}</h3>
                        <p><strong>Дата начала:</strong> {{ r.start_date }}</p>
                        <p><strong>Оплата:</strong> {{ r.payment }}</p>
                        <p><strong>Статус:</strong> {{ r.status_name }}</p>

                        {% if r.status_name == 'Завершено' %}
                            <form method="POST"
                                  action="/requests/{{ r.id }}/review"
                                  class="review-form">
                                <label for="review-{{ r.id }}">Отзыв:</label>
                                <textarea id="review-{{ r.id }}"
                                          name="text"
                                          data-testid="req-review"
                                          placeholder="Оставьте отзыв..."
                                          required>{{ r.review_text or '' }}</textarea>

                                <label for="rating-{{ r.id }}">Оценка:</label>
                                <select id="rating-{{ r.id }}" name="rating" required>
                                    <option value="">—</option>
                                    {% for i in range(1, 6) %}
                                        <option value="{{ i }}"
                                            {% if r.review_rating == i %}selected{% endif %}>
                                            {{ i }}
                                        </option>
                                    {% endfor %}
                                </select>

                                <button type="submit">Отправить отзыв</button>
                            </form>
                        {% endif %}
                    </article>
                {% endfor %}
            </div>
        {% endif %}
    </main>
</body>
</html>
```

### 8.6. Шаблон панели администратора `templates/admin.html`

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Панель администратора — Конференции.РФ</title>
    <link rel="stylesheet" href="/static/css/style.css">
</head>
<body>
    <main class="auth-container wide">
        <img src="/static/img/logo.jpg" alt="Конференции.РФ" class="auth-logo">
