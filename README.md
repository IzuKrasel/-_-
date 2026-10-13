# Справочник по официальной документации Python, Flask и Jinja2

## Разделы, используемые в проекте «Конференции.РФ»

Документ содержит выдержки из официальной документации с переводом на русский язык, соответствующие функциональным блокам разрабатываемой информационной системы.

---

## 1. Модуль `sqlite3` — интерфейс DB-API 2.0 для баз данных SQLite

**Официальный источник:** https://docs.python.org/3/library/sqlite3.html

### 1.1. Модуль: `sqlite3.connect(database, timeout=5.0, ...)`

Открывает соединение с базой данных SQLite .

**Параметры:**

- `database` (path-like object) — путь к файлу базы данных. Для создания базы данных, существующей только в оперативной памяти, можно передать `":memory:"`.
- `timeout` (float) — количество секунд ожидания перед вызовом `OperationalError`, если таблица заблокирована. Значение по умолчанию — пять секунд.
- `detect_types` (int) — определяет, следует ли искать преобразователи типов, зарегистрированные через `register_converter()`.
- `check_same_thread` (bool) — если `True` (значение по умолчанию), соединение может использоваться только в создавшем его потоке.
- `factory` (Connection) — пользовательский класс для создания соединения.

**Пример использования в проекте:**

```python
import sqlite3
con = sqlite3.connect("database.db")
```

### 1.2. Объект `Cursor` и методы выполнения

**`cursor.execute(sql, parameters)`** — выполняет одну SQL-инструкцию .

**`cursor.executemany(sql, seq_of_parameters)`** — выполняет SQL-инструкцию для всех последовательностей параметров.

**`cursor.fetchone()`** — возвращает следующую строку результата запроса в виде кортежа или `None`, если строк больше нет.

**`cursor.fetchall()`** — возвращает все (оставшиеся) строки результата запроса в виде списка .

**Пример использования в проекте:**

```python
cur = con.cursor()
cur.execute("INSERT INTO users (login, password) VALUES (?, ?)", ("admin", "hash"))
con.commit()

res = cur.execute("SELECT login FROM users")
rows = res.fetchall()
```

### 1.3. Использование заполнителей (placeholders)

Для безопасной подстановки значений в SQL-запросы используются заполнители `?` вместо форматирования строк :

```python
# Правильно:
cur.execute("SELECT * FROM users WHERE login=?", (login,))

# Неправильно (уязвимо для SQL-инъекций):
cur.execute(f"SELECT * FROM users WHERE login='{login}'")
```

### 1.4. Управление транзакциями

**`connection.commit()`** — фиксирует текущую транзакцию .

**`connection.rollback()`** — откатывает изменения, сделанные с момента последнего вызова `commit()`.

**`connection.close()`** — закрывает соединение с базой данных.

**Пример:**

```python
con = sqlite3.connect("database.db")
con.execute("INSERT INTO users VALUES (?, ?, ?)", data)
con.commit()
con.close()
```

### 1.5. Объект `Row`

**`connection.row_factory = sqlite3.Row`** — устанавливает фабрику строк, позволяющую обращаться к столбцам результата по имени, как к словарю:

```python
con.row_factory = sqlite3.Row
cur = con.cursor()
row = cur.execute("SELECT id, login FROM users").fetchone()
print(row["login"])  # доступ по имени столбца
```

---

## 2. Модуль `re` — операции с регулярными выражениями

**Официальный источник:** https://docs.python.org/3/library/re.html

### 2.1. Функция `re.fullmatch(pattern, string, flags=0)`

Проверяет, соответствует ли **вся** строка `string` регулярному выражению `pattern`. Возвращает объект `Match` или `None` .

**Пример использования в проекте:**

```python
import re

if re.fullmatch(r'[A-Za-z0-9]{6,}', login):
    # логин корректен
    pass

if re.fullmatch(r'8\(\d{3}\)\d{3}-\d{2}-\d{2}', phone):
    # телефон корректен
    pass
```

### 2.2. Синтаксис регулярных выражений

**Классы символов:**

| Выражение | Значение |
|-----------|----------|
| `[abc]` | любой из символов `a`, `b` или `c` |
| `[^abc]` | любой символ, кроме `a`, `b`, `c` |
| `[a-z]` | любой символ в диапазоне от `a` до `z` |
| `\d` | любая десятичная цифра; эквивалентно `[0-9]` |
| `\w` | любой алфавитно-цифровой символ и знак подчёркивания |
| `\s` | любой пробельный символ |

**Квантификаторы:**

| Выражение | Значение |
|-----------|----------|
| `*` | ноль или более повторений |
| `+` | одно или более повторений |
| `?` | ноль или одно повторение |
| `{n}` | ровно `n` повторений |
| `{n,}` | не менее `n` повторений |
| `{n,m}` | от `n` до `m` повторений |

**Группы и альтернативы:**

| Выражение | Значение |
|-----------|----------|
| `(...)` | группа; содержимое может быть извлечено после совпадения |
| `A\|B` | либо `A`, либо `B` |
| `\.` | экранированная точка (литеральный символ `.`) |

---

## 3. Модуль `hashlib` — безопасные хеши и дайджесты сообщений

**Официальный источник:** https://docs.python.org/3/library/hashlib.html

### 3.1. Функция `hashlib.sha256(data=b'')`

Создаёт объект хеша, использующий алгоритм SHA-256. Возвращает объект, поддерживающий методы `update()`, `digest()`, `hexdigest()` .

**Пример использования в проекте:**

```python
import hashlib

def hash_pwd(password):
    return hashlib.sha256(password.encode()).hexdigest()
```

### 3.2. Метод `hash.hexdigest()`

Возвращает дайджест в виде строки шестнадцатеричных цифр (по две цифры на каждый байт) .

**Пример:**

```python
h = hashlib.sha256(b"password")
h.hexdigest()
# '5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8'
```

### 3.3. Доступные алгоритмы

Модуль гарантированно предоставляет конструкторы для алгоритмов: `md5`, `sha1`, `sha224`, `sha256`, `sha384`, `sha512` .

---

## 4. Модуль `os` — интерфейс операционной системы

**Официальный источник:** https://docs.python.org/3/library/os.html

### 4.1. Функция `os.path.join(path, *paths)`

Соединяет один или несколько компонентов пути, используя разделитель, принятый в операционной системе (`/` в Linux, `\` в Windows) .

**Пример использования в проекте:**

```python
import os
DB = os.path.join(os.path.dirname(__file__), 'database.db')
```

### 4.2. Функция `os.path.dirname(path)`

Возвращает имя каталога, содержащего указанный путь.

**Пример:**

```python
os.path.dirname("/home/user/project/app.py")
# '/home/user/project'
```

### 4.3. Функция `os.getcwd()`

Возвращает текущий рабочий каталог .

**Пример:**

```python
os.getcwd()
# '/home/user/project'
```

---

## 5. Модуль `datetime` — базовые типы даты и времени

**Официальный источник:** https://docs.python.org/3/library/datetime.html

### 5.1. Класс `datetime.date`

Объект `date` представляет дату (год, месяц и день) в идеализированном календаре, текущем григорианском календаре, неограниченно расширенном в обоих направлениях .

**Конструктор:**

```python
datetime.date(year, month, day)
```

Все аргументы обязательны. Допустимые диапазоны: `MINYEAR <= year <= MAXYEAR`, `1 <= month <= 12`, `1 <= day <= количество дней в данном месяце и году`.

### 5.2. Метод `date.isoformat()`

Возвращает строку, представляющую дату в формате ISO 8601, `ГГГГ-ММ-ДД` .

**Пример:**

```python
import datetime as dt
dt.date(2002, 12, 4).isoformat()
# '2002-12-04'
```

### 5.3. Класс `datetime.timedelta`

Объект `timedelta` представляет длительность — разницу между двумя датами или временами .

**Конструктор:**

```python
datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)
```

Все аргументы необязательны и по умолчанию равны нулю.

---

## 6. Установка Python-модулей: pip и venv

**Официальный источник:** https://docs.python.org/3/installing/index.html

### 6.1. Ключевые термины

**pip** — предпочтительная программа установки. По умолчанию включена в бинарные установщики Python .

**Виртуальное окружение** — полуизолированное окружение Python, позволяющее устанавливать пакеты для конкретного приложения, а не для всей системы.

**venv** — стандартный инструмент для создания виртуальных окружений. По умолчанию устанавливает pip в каждое создаваемое окружение .

### 6.2. Создание виртуального окружения

```bash
python -m venv .venv
```

**Активация (Linux/macOS):**

```bash
source .venv/bin/activate
```

**Активация (Windows):**

```bash
.venv\Scripts\activate
```

### 6.3. Установка пакетов

```bash
python -m pip install SomePackage
```

Установка конкретной версии:

```bash
python -m pip install SomePackage==1.0.4
```

Установка минимальной версии:

```bash
python -m pip install "SomePackage>=1.0.4"
```

### 6.4. Сохранение зависимостей

```bash
pip freeze > requirements.txt
```

Установка из файла зависимостей:

```bash
pip install -r requirements.txt
```

### 6.5. Создание `requirements.txt`

Файл `requirements.txt` содержит перечень зависимостей проекта :

```text
Flask==3.0.0
```

---

## 7. Flask API — справочник

**Официальный источник:** https://flask.palletsprojects.com/en/stable/api/

### 7.1. Класс `Flask` — основной объект приложения

```python
from flask import Flask
app = Flask(__name__)
```

### 7.2. Метод `Flask.run(host=None, port=None, debug=None, ...)`

Запускает локальный сервер разработки .

**Параметры:**

- `host` (str | None) — имя хоста для прослушивания. Для доступа извне установите `'0.0.0.0'`. По умолчанию `'127.0.0.1'`.
- `port` (int | None) — порт веб-сервера. По умолчанию `5000`.
- `debug` (bool | None) — включает или отключает режим отладки.
- `load_dotenv` (bool) — загружает ближайшие файлы `.env` и `.flaskenv` для установки переменных окружения.

**Пример использования в проекте:**

```python
app.run(host='127.0.0.1', port=8080, debug=True)
```

### 7.3. Декоратор `@app.route(rule, methods=...)`

Регистрирует функцию-обработчик для указанного URL-правила .

**Пример:**

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    return render_template('login.html')
```

### 7.4. Функция `render_template(template_name_or_list, **context)`

Отрисовывает шаблон из папки `templates` с переданным контекстом.

**Пример:**

```python
return render_template('register.html', errors=errors)
```

### 7.5. Объект `request`

Содержит данные входящего HTTP-запроса.

**`request.method`** — метод запроса (`'GET'`, `'POST'` и т. д.).

**`request.form`** — данные формы, отправленные методом POST.

**`request.args`** — параметры строки запроса.

### 7.6. Объект `session`

Словареподобный объект для хранения данных сессии между запросами.

```python
session['user_id'] = user['id']
session['role'] = user['role']
```

**`session.clear()`** — очищает сессию.

### 7.7. Функция `redirect(location, code=302)`

Возвращает ответ-перенаправление на указанный URL.

**Пример:**

```python
return redirect('/login')
```

### 7.8. Функция `url_for(endpoint, **values)`

Создаёт URL для указанной конечной точки, используя её имя.

**Пример:**

```python
url_for('static', filename='css/style.css')
# '/static/css/style.css'
```

---

## 8. Jinja2 — документация для проектировщика шаблонов

**Официальный источник:** https://jinja.palletsprojects.com/en/stable/templates/

### 8.1. Синтаксис вывода переменных

```jinja2
{{ variable }}
```

Выводит значение переменной в HTML с автоматическим экранированием .

### 8.2. Условные конструкции `{% if %}`

```jinja2
{% if user.is_admin %}
    <p>Администратор</p>
{% elif user.is_moderator %}
    <p>Модератор</p>
{% else %}
    <p>Пользователь</p>
{% endif %}
```

`if` также может использоваться как встроенное выражение и для фильтрации циклов .

### 8.3. Циклы `{% for %}`

```jinja2
{% for user in users %}
    <li>{{ user.name }}</li>
{% endfor %}
```

Внутри цикла доступна специальная переменная `loop`, содержащая информацию о текущей итерации .

**Пример с `loop.index`:**

```jinja2
{% for item in items %}
    {{ loop.index }}. {{ item }}
{% endfor %}
```

### 8.4. Макросы `{% macro %}`

Макросы аналогичны функциям в обычных языках программирования. Они используются для вынесения часто используемых идиом в переиспользуемые функции .

**Пример:**

```jinja2
{% macro input(name, value='', type='text') -%}
    <input type="{{ type }}" name="{{ name }}" value="{{ value|e }}">
{%- endmacro %}

<p>{{ input('username') }}</p>
<p>{{ input('password', type='password') }}</p>
```

### 8.5. Наследование шаблонов

Базовый шаблон:

```jinja2
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}Мой сайт{% endblock %}</title>
</head>
<body>
    <main>
        {% block content %}{% endblock %}
    </main>
</body>
</html>
```

Дочерний шаблон:

```jinja2
{% extends "base.html" %}

{% block title %}Главная{% endblock %}

{% block content %}
    <h2>Добро пожаловать</h2>
{% endblock %}
```

### 8.6. Фильтры

Фильтры применяются к переменным с использованием символа `|` :

| Фильтр | Описание |
|--------|----------|
| `length` | возвращает количество элементов |
| `join(", ")` | объединяет элементы списка в строку с разделителем |
| `default("значение")` | подставляет значение по умолчанию |
| `safe` | отключает автоматическое экранирование |
| `truncate(120)` | обрезает строку до указанной длины |

---

## 9. Полный перечень используемых разделов документации

| № | Модуль/Библиотека | Раздел | Ссылка |
|---|------------------|--------|--------|
| 1 | `sqlite3` | Module functions, Connection objects, Cursor objects | docs.python.org/3/library/sqlite3.html |
| 2 | `re` | Regular expression syntax, `re.fullmatch()` | docs.python.org/3/library/re.html |
| 3 | `hashlib` | `hashlib.sha256()`, `hash.hexdigest()` | docs.python.org/3/library/hashlib.html |
| 4 | `os` | `os.path.join()`, `os.path.dirname()` | docs.python.org/3/library/os.html |
| 5 | `datetime` | `date`, `timedelta`, `isoformat()` | docs.python.org/3/library/datetime.html |
| 6 | `pip` / `venv` | Installing Python Modules | docs.python.org/3/installing/index.html |
| 7 | Flask | API — `Flask.run()`, `route()`, `render_template()` | flask.palletsprojects.com/en/stable/api/ |
| 8 | Jinja2 | Template Designer Documentation | jinja.palletsprojects.com/en/stable/templates/ |

---

Документ подготовлен на основе официальной документации Python Software Foundation, Flask (Pallets Projects) и Jinja2 (Pallets Projects). Все приведённые выдержки соответствуют актуальным версиям: Python 3.12+, Flask 3.0+, Jinja2 3.1+.

# Техническая документация (адаптированная для РОСА Линукс)

## Разработка информационной системы «Конференции.РФ»

**Демонстрационный экзамен 2027, специальность 09.02.07, квалификация «Разработчик веб и мультимедийных приложений»**

---

## Оглавление

1. Общие сведения
2. Архитектура решения
3. Требования к окружению (РОСА Линукс)
4. Последовательность сборки проекта
5. Структура проекта
6. Проектирование базы данных
7. Серверная часть на Python (Flask)
8. Клиентская часть: HTML5
9. Клиентская часть: CSS3
10. Клиентская часть: JavaScript
11. Атрибуты `data-testid` для автопроверки
12. Модуль 4: REST API
13. **Запуск сервера (РОСА Линукс)**
14. Развёртывание, упаковка и контрольный список

---

## 1. Общие сведения

Настоящая документация описывает процесс разработки информационной системы «Конференции.РФ» — веб-портала бронирования помещений для проведения всероссийских конференций. Документ адаптирован для работы в операционной системе **РОСА Линукс** (ROSA Linux) и содержит описание архитектуры, схемы базы данных, примеры программного кода на языках Python, HTML, CSS, JavaScript и SQL, а также порядок выполнения работ по всем четырём модулям демонстрационного экзамена.

---

## 2. Архитектура решения

Система построена по принципу «полного стека» и включает три слоя:

1. **Слой представления** — HTML5-шаблоны, CSS, JavaScript.
2. **Слой бизнес-логики** — Flask-приложение на Python.
3. **Слой хранения данных** — SQLite (в Модуле 4 — внешняя БД `shop_db.sql`).

```
Браузер (HTML/CSS/JS)
        │  HTTP-запрос
        ▼
Flask (Python) — маршруты, валидация, сессии
        │  SQL-запрос
        ▼
SQLite (database.db)
```

Принятый стек: **Python 3.12 + Flask 3.0 + SQLite + HTML5 + CSS3 + JavaScript ES2017**.

---

## 3. Требования к окружению (РОСА Линукс)

### 3.1. Программное обеспечение для РОСА Линукс

- Python 3.12 или выше;
- pip (менеджер пакетов Python);
- веб-браузер Chromium или Firefox (устанавливаются из репозитория РОСА);
- текстовый редактор: **Visual Studio Code** (устанавливается из менеджера приложений или через `dnf`) ;
- **PyCharm Community** (доступен в магазине приложений МОС-12 или через `dnf install pycharm-community`) ;
- программа «Автотест» (предоставляется организаторами);
- архиватор: **7-Zip** (команда `7zz`) ;
- **Git** — система контроля версий (устанавливается командой `sudo dnf install git`) .

### 3.2. Установка Python и pip в РОСА Линукс

Проверка установленной версии Python:

```bash
python3 --version
pip3 --version
```

Если Python 3 не установлен:

```bash
sudo dnf install python3 python3-pip
```

### 3.3. Установка Visual Studio Code в РОСА Линукс

**Вариант 1.** Через менеджер приложений (графический интерфейс).

**Вариант 2.** Через командную строку (если пакет доступен в репозитории):

```bash
sudo dnf install code
```

**Вариант 3.** Через Flatpak:

```bash
sudo dnf install flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak install flathub com.visualstudio.code
```

Запуск: `flatpak run com.visualstudio.code` .

### 3.4. Установка PyCharm Community в РОСА Линукс

Из магазина приложений или командной строки:

```bash
sudo dnf install pycharm-community
```

Либо из архива `.tar.gz` с сайта JetBrains (распаковка в `/opt`) .

### 3.5. Установка Git в РОСА Линукс

```bash
sudo dnf install git
```

Проверка:

```bash
git --version
```

### 3.6. Установка 7-Zip в РОСА Линукс

Скачать официальный бинарник с сайта 7-Zip:

```bash
wget https://www.7-zip.org/a/7z2101-linux-x64.tar.xz
tar xf 7z2101-linux-x64.tar.xz
sudo mv 7zz /usr/local/bin/
sudo chmod 755 /usr/local/bin/7zz
```

Проверка: `7zz --version` .

### 3.7. Установка зависимостей

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

### Шаг 1. Создание рабочей папки и виртуального окружения

```bash
mkdir project
cd project
python3 -m venv venv
```

Активация виртуального окружения (команда для Linux):

```bash
source venv/bin/activate
```

После активации приглашение командной строки изменится — появится префикс `(venv)`.

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

Файл собирается из разделов 7.2–7.12 в указанном порядке:

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
python3 app.py
```

При старте выполнится `init_db()`, создастся файл `database.db`, добавятся статусы, помещения и учётная запись администратора `Conf2027 / Demo77`. Открыть в браузере `http://127.0.0.1:8080/register`.

### Шаг 10. Инициализация Git

```bash
git init
git add .
git commit -m "Начальная структура проекта Конференции.РФ"
```

### Шаг 11. Упаковка проекта

```bash
7zz a project_1.zip project/
```

Или через файловый менеджер: правой кнопкой мыши по папке → «Сжать» → формат ZIP.

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

### 6.4. SQL-скрипт создания схемы

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

### 7.3. Подключение к базе данных

```python
def db():
    c = sqlite3.connect(DB)
    c.row_factory = sqlite3.Row
    return c
```

### 7.4. Хеширование паролей

```python
def hash_pwd(p):
    return hashlib.sha256(p.encode()).hexdigest()
```

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

---

## 8. Клиентская часть: HTML5

### 8.1. Общие требования

- Документ начинается с `<!DOCTYPE html>`.
- Атрибут `lang="ru"` у тега `<html>`.
- Семантическая разметка: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`.
- Атрибуты `data-testid` для проверяемых элементов.
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
        <h1>Панель администратора</h1>

        <p class="switch-link">
            Вы вошли как <strong>{{ session.login }}</strong> |
            <a href="/logout">Выйти</a>
        </p><br>

        {% if not requests %}
            <p class="empty">Заявок пока нет.</p>
        {% else %}
            <div class="admin-list">
                {% for r in requests %}
                    <div class="admin-row" data-testid="admin-row">
                        <div class="admin-row-info">
                            <h3>{{ r.room_name }}</h3>
                            <p><strong>Пользователь:</strong> {{ r.user_name }} ({{ r.user_login }})</p>
                            <p><strong>Дата начала:</strong> {{ r.start_date }}</p>
                            <p><strong>Оплата:</strong> {{ r.payment }}</p>
                        </div>

                        <form method="POST"
                              action="/admin/{{ r.id }}/status"
                              class="admin-status-form">
                            <select name="status_id"
                                    data-testid="admin-status"
                                    onchange="this.form.submit()">
                                {% for s in statuses %}
                                    <option value="{{ s.id }}"
                                        {% if s.id == r.status_id %}selected{% endif %}>
                                        {{ s.name }}
                                    </option>
                                {% endfor %}
                            </select>

                            <noscript>
                                <button type="submit">Сохранить</button>
                            </noscript>
                        </form>
                    </div>
                {% endfor %}
            </div>
        {% endif %}
    </main>
</body>
</html>
```

---

## 9. Клиентская часть: CSS3

### 9.1. Полный код `static/css/style.css`

```css
/* ============================================================
   ШРИФТ PT SANS
   ============================================================ */
@font-face {
    font-family: 'PT Sans';
    src: url('/static/fonts/PTSans-Regular.ttf') format('truetype');
    font-weight: 400;
}
@font-face {
    font-family: 'PT Sans';
    src: url('/static/fonts/PTSans-Bold.ttf') format('truetype');
    font-weight: 700;
}
@font-face {
    font-family: 'PT Sans';
    src: url('/static/fonts/PTSans-Italic.ttf') format('truetype');
    font-weight: 400;
    font-style: italic;
}

/* ============================================================
   ЦВЕТА
   ============================================================ */
:root {
    --blue:  #007bff;
    --dark:  #0d47a1;
    --gray:  #6c757d;
    --bg:    #f8f9fa;
    --white: #ffffff;
    --red:   #721c24;
    --radius: 10px;
}

/* ============================================================
   БАЗА
   ============================================================ */
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: 'PT Sans', Arial, sans-serif;
    background: var(--bg);
    color: var(--dark);
    font-size: 16px;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

/* ============================================================
   КАРТОЧКА
   ============================================================ */
.auth-container {
    background: var(--white);
    padding: 36px 30px;
    border-radius: var(--radius);
    box-shadow: 0 4px 20px rgba(13, 71, 161, 0.08);
    width: 100%;
    max-width: 440px;
    border-top: 4px solid var(--blue);
    animation: fadeIn 0.4s ease-out;
}

.auth-container.wide {
    max-width: 720px;
}

.auth-logo {
    display: block;
    width: 64px;
    height: 64px;
    margin: 0 auto 16px;
    border-radius: 50%;
    object-fit: cover;
}

h1 {
    font-size: 32px;
    text-align: center;
    margin-bottom: 22px;
}

h3 {
    font-size: 18px;
    margin-bottom: 8px;
}

/* ============================================================
   ПОЛЯ И КНОПКИ
   ============================================================ */
label {
    display: block;
    font-size: 14px;
    font-weight: 700;
    margin-bottom: 6px;
}

input, textarea, select {
    width: 100%;
    padding: 12px 14px;
    margin-bottom: 16px;
    border: 1px solid #ced4da;
    border-radius: var(--radius);
    font-family: inherit;
    font-size: 15px;
    color: var(--dark);
    background: var(--white);
    transition: border-color 0.2s, box-shadow 0.2s;
}

input::placeholder,
textarea::placeholder {
    color: var(--gray);
    font-style: italic;
}

input:focus,
textarea:focus,
select:focus {
    outline: none;
    border-color: var(--blue);
    box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.15);
}

textarea {
    min-height: 80px;
    resize: vertical;
}

button {
    width: 100%;
    padding: 14px;
    background: var(--blue);
    color: var(--white);
    border: none;
    border-radius: var(--radius);
    font-family: inherit;
    font-size: 16px;
    font-weight: 700;
    cursor: pointer;
    transition: background 0.2s, transform 0.1s;
}

button:hover {
    background: var(--dark);
}

button:active {
    transform: scale(0.98);
}

/* ============================================================
   ОШИБКИ
   ============================================================ */
.errors {
    background: #fdecea;
    border: 1px solid #f5c6cb;
    color: var(--red);
    padding: 12px 16px;
    border-radius: var(--radius);
    margin-bottom: 18px;
    font-size: 14px;
}

.errors ul { list-style: none; }

/* ============================================================
   ССЫЛКИ
   ============================================================ */
.switch-link {
    text-align: center;
    margin-top: 16px;
    font-size: 14px;
    color: var(--gray);
    font-style: italic;
}

.switch-link a {
    color: var(--blue);
    text-decoration: none;
    font-weight: 700;
    font-style: normal;
}

.switch-link a:hover {
    text-decoration: underline;
}

/* ============================================================
   СПИСОК ЗАЯВОК
   ============================================================ */
.empty {
    text-align: center;
    color: var(--gray);
    font-style: italic;
    padding: 20px 0;
}

.request-card {
    background: var(--bg);
    border: 1px solid #dee2e6;
    border-radius: var(--radius);
    padding: 16px 18px;
    margin-bottom: 14px;
    transition: box-shadow 0.2s, transform 0.15s;
}

.request-card:hover {
    box-shadow: 0 4px 16px rgba(13, 71, 161, 0.10);
    transform: translateY(-2px);
}

.request-card p {
    margin-bottom: 6px;
    font-size: 15px;
}

.review-form {
    margin-top: 12px;
    padding-top: 12px;
    border-top: 1px dashed #ced4da;
}

.review-form textarea {
    min-height: 70px;
    margin-bottom: 8px;
    font-size: 14px;
}

.review-form select {
    margin-bottom: 8px;
    font-size: 14px;
}

.review-form button {
    width: auto;
    padding: 10px 20px;
    font-size: 14px;
}

/* ============================================================
   ПАНЕЛЬ АДМИНИСТРАТОРА
   ============================================================ */
.admin-list {
    display: flex;
    flex-direction: column;
    gap: 14px;
}

.admin-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 16px;
    background: var(--bg);
    border: 1px solid #dee2e6;
    border-radius: var(--radius);
    padding: 16px 18px;
    transition: box-shadow 0.2s, transform 0.15s;
}

.admin-row-info {
    flex: 1;
}

.admin-row-info h3 {
    margin-bottom: 8px;
    color: var(--dark);
}

.admin-row-info p {
    margin-bottom: 4px;
    font-size: 14px;
}

.admin-status-form select {
    width: auto;
    min-width: 180px;
    margin-bottom: 0;
    font-size: 14px;
    cursor: pointer;
}

.admin-status-form button {
    width: auto;
    padding: 8px 14px;
    font-size: 14px;
}

/* ============================================================
   АНИМАЦИИ
   ============================================================ */
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(8px); }
    to   { opacity: 1; transform: translateY(0); }
}

/* ============================================================
   АДАПТИВ ПОД СМАРТФОН 390×844
   ============================================================ */
@media (max-width: 480px) {
    .auth-container {
        padding: 24px 18px;
        border-radius: 8px;
    }

    h1 {
        font-size: 26px;
    }

    input, button, textarea, select {
        font-size: 16px;
    }

    .admin-row {
        flex-direction: column;
        align-items: stretch;
    }

    .admin-status-form select {
        width: 100%;
    }
}
```

---

## 10. Клиентская часть: JavaScript

### 10.1. Полный код `static/js/validation.js`

```javascript
document.addEventListener('DOMContentLoaded', function () {

    /* ============================================================
       ВАЛИДАЦИЯ ФОРМЫ РЕГИСТРАЦИИ
       ============================================================ */
    var regForm = document.getElementById('register-form');

    if (regForm) {
        regForm.addEventListener('submit', function (e) {

            var errors = [];

            var login    = document.getElementById('login').value.trim();
            var password = document.getElementById('password').value;
            var fullname = document.getElementById('fullname').value.trim();
            var phone    = document.getElementById('phone').value.trim();
            var email    = document.getElementById('email').value.trim();

            if (!/^[A-Za-z0-9]{6,}$/.test(login)) {
                errors.push('Логин: только латиница и цифры, минимум 6 символов');
            }

            if (password.length < 8) {
                errors.push('Пароль: минимум 8 символов');
            }

            if (!/^[А-Яа-яЁё\s]+$/.test(fullname)) {
                errors.push('ФИО: только кириллица и пробелы');
            }

            if (!/^8\(\d{3}\)\d{3}-\d{2}-\d{2}$/.test(phone)) {
                errors.push('Телефон: формат 8(XXX)XXX-XX-XX');
            }

            if (!/^[^@\s]+@[^@\s]+\.[^@\s]+$/.test(email)) {
                errors.push('Email: некорректный формат');
            }

            if (errors.length > 0) {
                e.preventDefault();
                showErrors(regForm, errors, 'reg-errors');
            }
        });
    }

    /* ============================================================
       ВАЛИДАЦИЯ ФОРМЫ ВХОДА
       ============================================================ */
    var loginForm = document.getElementById('login-form');

    if (loginForm) {
        loginForm.addEventListener('submit', function (e) {

            var errors = [];

            var login    = document.getElementById('login').value.trim();
            var password = document.getElementById('password').value;

            if (!login) {
                errors.push('Введите логин');
            }
            if (!password) {
                errors.push('Введите пароль');
            }

            if (errors.length > 0) {
                e.preventDefault();
                showErrors(loginForm, errors, 'login-errors');
            }
        });
    }

    /* ============================================================
       ФУНКЦИЯ ПОКАЗА ОШИБОК
       ============================================================ */
    function showErrors(form, errors, containerId) {

        var old = document.querySelector('[data-testid="' + containerId + '"]');
        if (old) {
            old.remove();
        }

        var div = document.createElement('div');
        div.className = 'errors';
        div.setAttribute('data-testid', containerId);

        div.innerHTML = '<ul>' + errors.map(function (err) {
            return '<li>' + err + '</li>';
        }).join('') + '</ul>';

        form.parentNode.insertBefore(div, form);
    }

});
```

---

## 11. Атрибуты `data-testid` для автопроверки

| Страница | Элемент | Значение |
|----------|---------|----------|
| Регистрация | Поле «Логин» | `reg-login` |
| Регистрация | Поле «Пароль» | `reg-password` |
| Регистрация | Поле «ФИО» | `reg-fullname` |
| Регистрация | Поле «Телефон» | `reg-phone` |
| Регистрация | Поле «E-mail» | `reg-email` |
| Регистрация | Кнопка «Создать пользователя» | `reg-submit` |
| Регистрация | Блок ошибок | `reg-errors` |
| Вход | Поле «Логин» | `login-login` |
| Вход | Поле «Пароль» | `login-password` |
| Вход | Кнопка «Войти» | `login-submit` |
| Вход | Блок ошибок | `login-errors` |
| Создание заявки | Поле «Помещение» | `req-room` |
| Создание заявки | Поле «Дата начала» | `req-date` |
| Создание заявки | Поле «Способ оплаты» | `req-payment` |
| Создание заявки | Кнопка «Отправить» | `req-submit` |
| Список заявок | Контейнер списка | `req-list` |
| Список заявок | Поле отзыва | `req-review` |
| Админ-панель | Строка заявки | `admin-row` |
| Админ-панель | Переключатель статуса | `admin-status` |

---

## 12. Модуль 4: REST API

### 12.1. Структура БД `shop_db.sql`

Таблицы: `users`, `products`, `orders`, `order_items`.

### 12.2. Правила формирования ответа

```json
{
    "id": 1,
    "buyer": "Ковалёв Артём Игоревич",
    "status": "Выполнена",
    "created_at": "14.02.2026",
    "address": "Москва, Полярная, д. 31, кв. 12",
    "total": "2770.50",
    "items_count": 2,
    "items": [
        {"title": "Клавиатура механическая", "quantity": 1, "price": "1990.00", "sum": "1990.00"},
        {"title": "Коврик для мыши", "quantity": 2, "price": "390.00", "sum": "780.00"}
    ]
}
```

### 12.3. Соответствие статусов

| Код | Русское название |
|-----|------------------|
| new | Новая |
| paid | Оплачена |
| shipped | Отправлена |
| done | Выполнена |
| cancelled | Отменена |

### 12.4. Пример `api.py`

```python
from flask import Flask, request, jsonify
import sqlite3, os

app = Flask(__name__)
DB = os.path.join(os.path.dirname(__file__), 'shop.db')

STATUS_MAP = {
    'new':       'Новая',
    'paid':      'Оплачена',
    'shipped':   'Отправлена',
    'done':      'Выполнена',
    'cancelled': 'Отменена',
}


def db():
    c = sqlite3.connect(DB)
    c.row_factory = sqlite3.Row
    return c


def fmt_date(s):
    try:
        return f"{s[8:10]}.{s[5:7]}.{s[0:4]}"
    except Exception:
        return s


def fmt_address(u):
    parts = [u['city'], u['street'], f"д. {u['house']}"]
    if u['flat']:
        parts.append(f"кв. {u['flat']}")
    return ", ".join(parts)


def fmt_money(x):
    return f"{float(x):.2f}"


@app.route('/orders', methods=['GET'])
def orders():
    status = request.args.get('status')
    user_id = request.args.get('user_id')

    if status and status not in STATUS_MAP:
        return jsonify({'error': f'Недопустимое значение status: {status}'}), 400

    if user_id and not user_id.isdigit():
        return jsonify({'error': f'Недопустимое значение user_id: {user_id}'}), 400

    try:
        c = db()
        sql = """
            SELECT o.id, o.user_id, o.status, o.created_at,
                   u.last_name, u.first_name, u.middle_name,
                   u.city, u.street, u.house, u.flat
            FROM orders o
            JOIN users u ON u.id = o.user_id
            WHERE 1=1
        """
        params = []
        if status:
            sql += " AND o.status = ?"
            params.append(status)
        if user_id:
            sql += " AND o.user_id = ?"
            params.append(int(user_id))
        sql += " ORDER BY o.id ASC"

        rows = c.execute(sql, params).fetchall()

        result = []
        for o in rows:
            items = c.execute("""
                SELECT oi.quantity, p.title, p.price
                FROM order_items oi
                JOIN products p ON p.id = oi.product_id
                WHERE oi.order_id = ?
                ORDER BY oi.id ASC
            """, (o['id'],)).fetchall()

            item_list = []
            total = 0.0
            for it in items:
                s = float(it['price']) * int(it['quantity'])
                total += s
                item_list.append({
                    'title':    it['title'],
                    'quantity': int(it['quantity']),
                    'price':    fmt_money(it['price']),
                    'sum':      fmt_money(s),
                })

            result.append({
                'id':          o['id'],
                'buyer':       f"{o['last_name']} {o['first_name']} {o['middle_name'] or ''}".strip(),
                'status':      STATUS_MAP[o['status']],
                'created_at':  fmt_date(o['created_at']),
                'address':     fmt_address(o),
                'total':       fmt_money(total),
                'items_count': len(item_list),
                'items':       item_list,
            })

        c.close()
        return jsonify(result), 200

    except Exception as e:
        return jsonify({'error': str(e)}), 500


if __name__ == '__main__':
    app.run(host='127.0.0.1', port=8080, debug=True)
```

---

## 13. Запуск сервера (РОСА Линукс)

### 13.1. Запуск основного приложения «Конференции.РФ»

1. Открыть терминал в РОСА Линукс (сочетание клавиш `Ctrl+Alt+T` или через меню «Терминал»).

2. Перейти в папку проекта:

```bash
cd ~/project
```

3. Активировать виртуальное окружение:

```bash
source venv/bin/activate
```

4. Запустить Flask-приложение:

```bash
python3 app.py
```

5. В терминале появится сообщение:

```
 * Serving Flask app 'app'
 * Debug mode: on
 * Running on http://127.0.0.1:8080
Press CTRL+C to quit
```

6. Открыть браузер (Chromium или Firefox) и перейти по адресу:

```
http://127.0.0.1:8080/register
```

7. Для остановки сервера нажать `Ctrl+C` в терминале.

### 13.2. Запуск API (Модуль 4)

```bash
cd ~/project_api
source venv/bin/activate
python3 api.py
```

API будет доступно по адресу `http://127.0.0.1:8080/orders`.

### 13.3. Альтернативный запуск Flask (без `python3 app.py`)

Если приложение создано как фабрика (`create_app`), используется команда `flask run` :

```bash
export FLASK_APP=app
export FLASK_ENV=development
flask run --host=127.0.0.1 --port=8080
```

### 13.4. Запуск в фоновом режиме

Для запуска сервера в фоне (без блокировки терминала):

```bash
nohup python3 app.py > server.log 2>&1 &
```

Проверка запущенных процессов:

```bash
ps aux | grep app.py
```

Остановка фонового процесса:

```bash
pkill -f app.py
```

### 13.5. Проверка работы сервера

Открыть второй терминал и выполнить:

```bash
curl http://127.0.0.1:8080/register
```

Если сервер работает, в ответ вернётся HTML-код страницы регистрации.

### 13.6. Возможные проблемы при запуске в РОСА Линукс

| Проблема | Причина | Решение |
|----------|---------|---------|
| `ModuleNotFoundError: No module named 'flask'` | Виртуальное окружение не активировано | Выполнить `source venv/bin/activate` |
| `Address already in use` | Порт 8080 занят другим процессом | Изменить порт в `app.run()` или завершить процесс: `sudo fuser -k 8080/tcp` |
| `Permission denied` | Нет прав на файл | `chmod +x app.py` |
| Браузер не открывает страницу | Сервер не запущен или порт заблокирован | Проверить статус: `curl http://127.0.0.1:8080` |
| `sqlite3.OperationalError: unable to open database file` | Нет прав на запись в папку | Проверить права: `ls -la`, при необходимости `chmod 755 project/` |

---

## 14. Развёртывание, упаковка и контрольный список

### 14.1. Упаковка архивов в РОСА Линукс

| Модуль | Имя архива | Команда |
|--------|-----------|---------|
| 1 | `project_1.zip` | `7zz a project_1.zip project/` |
| 2 | `layout_2.zip` | `7zz a layout_2.zip layout/` |
| 3 | `project_3.zip` | `7zz a project_3.zip project/` |

### 14.2. Контрольный список

**Модуль 1:**
- [ ] Проект собирается и запускается (`python3 app.py`).
- [ ] Регистрация и авторизация работают.
- [ ] Создание заявки работает, есть `data-testid`.
- [ ] Просмотр заявок и отзывы работают.
- [ ] Панель администратора доступна (`Conf2027 / Demo77`).
- [ ] Смена статуса фиксируется в БД.
- [ ] Архив `project_1.zip` сформирован.

**Модуль 3:**
- [ ] Все страницы адаптивны под 390×844.
- [ ] HTML5-документы корректны, есть семантические теги.
- [ ] Реализованы анимации и микроанимации.
- [ ] ER-диаграмма построена и сохранена.
- [ ] Архив `project_3.zip` сформирован.

**Модуль 4:**
- [ ] Дамп `shop_db.sql` развёрнут.
- [ ] `GET /orders` возвращает список заказов.
- [ ] Фильтры `status` и `user_id` работают.
- [ ] Обработка ошибок: 400 и 500.

---

Документ завершён. При сборке проекта по разделам 4–13 информационная система «Конференции.РФ» запускается и функционирует в операционной системе РОСА Линукс; автотесты Модулей 1, 3 и 4 проходят при условии корректно расставленных атрибутов `data-testid`.
