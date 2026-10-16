# Gaming Stats

Личный трекер игровой библиотеки на Django и SQLite. Пользователи ведут свои коллекции, отмечают статус прохождения, оценку, время в игре и избранное. Графики строятся по записям текущего пользователя.

## Локальный запуск

Требуется Python 3.10 или новее (рекомендуется Python 3.12).

В PowerShell из корня проекта:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
$env:SECRET_KEY = "local-development-key-change-me"
$env:DEBUG = "1"
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Откройте <http://127.0.0.1:8000/>. Все переменные для локальной настройки перечислены в `.env.example`; Django не загружает этот файл автоматически, поэтому задайте их в окружении терминала. При локальном запуске без `SECRET_KEY` приложение создаст временный ключ; при `DEBUG=0` ключ обязателен.

## Проверки

```powershell
python manage.py check
python manage.py makemigrations games
python manage.py migrate
python manage.py test
```

Модель игры доступна также в `/admin/` после создания администратора.

## Render

Репозиторий содержит `render.yaml` для создания Web Service. Blueprint устанавливает зависимости, собирает статические файлы, выполняет миграции перед деплоем и запускает Gunicorn. `SECRET_KEY` генерируется Render, а SQLite-файл размещается на подключённом persistent disk.

SQLite сохраняет данные между перезапусками только при наличии постоянного диска. В `render.yaml` указан план Starter с диском 1 GB; проверьте доступность и стоимость persistent disk для выбранного региона/тарифа перед созданием сервиса. Для тарифа без диска локальная SQLite-база может быть потеряна при пересоздании экземпляра.

Для ручной настройки задайте `SECRET_KEY`, `DEBUG=0`, `ALLOWED_HOSTS=<имя-сервиса>.onrender.com` и `SQLITE_PATH=/opt/render/project/src/var/db.sqlite3`. Сбор статических файлов выполняется через WhiteNoise.

## Основные маршруты

- `/` — обзор для вошедшего пользователя или приветственная страница.
- `/games/` — библиотека с поиском, фильтрами и сортировкой.
- `/games/add/` — добавление игры.
- `/favorites/` и `/statistics/` — избранное и личная статистика.
- `/accounts/signup/` и `/accounts/login/` — регистрация и вход.
- `/admin/` — встроенная административная панель Django.

Обложки задаются URL-адресом; изображения хранятся на удалённом источнике и требуют доступности этого источника в браузере.