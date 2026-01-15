# Как развернуть проект

Здесь описан процесс установки и настройки проекта Blogicum на локальной машине или сервере.

## Что нужно

Для работы проекта требуется Python версии 3.10 или новее. Также понадобится pip для установки пакетов и Git для клонирования репозитория. На диске должно быть хотя бы 50 МБ свободного места.

## Установка на Windows

Клонируйте репозиторий:

```powershell
git clone <адрес-репозитория> django_sprint4
cd django_sprint4
```

Создайте виртуальное окружение:

```powershell
python -m venv venv
```

Активируйте его:

```powershell
.\venv\Scripts\Activate.ps1
```

Если возникает ошибка с политикой выполнения скриптов, запустите PowerShell от администратора и выполните:

```powershell
Set-ExecutionPolicy RemoteSigned
```

Установите зависимости:

```powershell
pip install -r requirements.txt
```

## Установка на Linux или Mac

Клонируйте репозиторий:

```bash
git clone <адрес-репозитория> django_sprint4
cd django_sprint4
```

Создайте виртуальное окружение:

```bash
python3 -m venv venv
```

Активируйте его:

```bash
source venv/bin/activate
```

Установите зависимости:

```bash
pip install -r requirements.txt
```

## Настройка базы данных

Примените миграции для создания таблиц:

```bash
python manage.py migrate
```

Создайте суперпользователя для доступа в админку:

```bash
python manage.py createsuperuser
```

Введите имя пользователя, email и пароль когда программа попросит.

## Загрузка тестовых данных

Если хотите сразу посмотреть на заполненный сайт, загрузите фикстуры:

```bash
python manage.py loaddata db.json
```

В базе появятся несколько категорий, постов и комментариев.

## Запуск

Запустите сервер разработки:

```bash
python manage.py runserver
```

Откройте в браузере http://127.0.0.1:8000/

Админ-панель доступна по адресу http://127.0.0.1:8000/admin/

## Настройка для продакшена

Для запуска на реальном сервере нужно изменить несколько настроек.

### Секретный ключ

Не используйте дефолтный SECRET_KEY из репозитория. Сгенерируйте новый:

```python
import secrets
print(secrets.token_urlsafe(50))
```

Скопируйте полученную строку и вставьте в blogicum/settings.py:

```python
SECRET_KEY = 'ваш-секретный-ключ'
```

### Отключение отладки

В settings.py измените:

```python
DEBUG = False

ALLOWED_HOSTS = ['ваш-домен.ru', 'www.ваш-домен.ru', '123.45.67.89']
```

В ALLOWED_HOSTS укажите домен вашего сайта или IP-адрес сервера.

### База данных PostgreSQL

Для продакшена лучше использовать PostgreSQL вместо SQLite. Сначала установите psycopg2:

```bash
pip install psycopg2-binary
```

Затем в settings.py замените настройки базы:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'blogicum_db',
        'USER': 'blogicum_user',
        'PASSWORD': 'сложный-пароль',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

Предварительно создайте базу и пользователя в PostgreSQL.

### Статические файлы

Добавьте в settings.py:

```python
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

Соберите статику:

```bash
python manage.py collectstatic
```

Настройте веб-сервер (nginx) для раздачи файлов из папки staticfiles/.

### Настоящий email

Для отправки настоящих писем измените в settings.py:

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = 'ваш-email@gmail.com'
EMAIL_HOST_PASSWORD = 'пароль-приложения'
```

Для Gmail нужно создать пароль приложения в настройках безопасности аккаунта.

### Переменные окружения

Хранить пароли и секретные ключи в коде небезопасно. Создайте файл .env в корне проекта:

```
SECRET_KEY=ваш-секретный-ключ
DEBUG=False
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
EMAIL_HOST_USER=ваш-email@gmail.com
EMAIL_HOST_PASSWORD=пароль-приложения
```

Добавьте .env в .gitignore, чтобы не попал в репозиторий.

Установите python-decouple:

```bash
pip install python-decouple
```

В settings.py используйте:

```python
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

## Структура папок

В корне проекта находится manage.py для управления Django и несколько конфигурационных файлов.

Папка blogicum содержит настройки проекта: settings.py, urls.py и wsgi.py.

Приложение blog отвечает за основной функционал блога. В нем модели, views, формы, URL и админка.

Приложение pages содержит статические страницы и обработчики ошибок.

В templates лежат все HTML-шаблоны, разделенные по приложениям. Папка includes содержит переиспользуемые части.

Папка static для статических файлов: изображений, CSS и JavaScript.

В media сохраняются загружаемые пользователями файлы.

Папка sent_emails используется только для разработки, там сохраняются "отправленные" письма.

В tests находятся тесты от Практикума.

## Основные адреса

Главная страница: http://127.0.0.1:8000/

Посты конкретной категории: http://127.0.0.1:8000/category/название/

Профиль пользователя: http://127.0.0.1:8000/profile/имя/

Создание поста: http://127.0.0.1:8000/posts/create/

Просмотр поста: http://127.0.0.1:8000/posts/1/

Регистрация: http://127.0.0.1:8000/auth/registration/

Вход: http://127.0.0.1:8000/auth/login/

Админка: http://127.0.0.1:8000/admin/

## Решение проблем

Если возникает ошибка при установке Pillow на Windows, попробуйте обновить pip:

```bash
python -m pip install --upgrade pip
```

Затем установите Pillow заново:

```bash
pip install Pillow
```

Если миграции не применяются, проверьте, что в каждом приложении есть папка migrations с файлом **init**.py:

```bash
mkdir blog/migrations
echo. > blog/migrations/__init__.py
```

Затем создайте миграции заново:

```bash
python manage.py makemigrations
python manage.py migrate
```

Если порт 8000 занят, можно запустить на другом:

```bash
python manage.py runserver 8080
```

На Windows проверить, что использует порт:

```bash
netstat -ano | findstr :8000
```

На Linux или Mac:

```bash
lsof -i :8000
```

Если нужно убить процесс, используйте его PID из вывода команды выше.

При проблемах с доступом к media/ на Linux проверьте права:

```bash
chmod 755 media/
```

## Запуск тестов

Проверить, что все работает правильно:

```bash
pytest
```

Запустить конкретный файл с тестами:

```bash
pytest tests/test_post.py
```

С подробным выводом:

```bash
pytest -v
```

Остановиться на первой ошибке:

```bash
pytest -x
```

## Дополнительная информация

Если что-то не работает, проверьте версию Python:

```bash
python --version
```

Должна быть 3.10 или выше.

Убедитесь, что все пакеты установились:

```bash
pip list
```

Посмотрите логи Django в консоли, там обычно написано, в чем проблема.

Документация Django поможет разобраться с настройками: https://docs.djangoproject.com/

Удачи с проектом!
