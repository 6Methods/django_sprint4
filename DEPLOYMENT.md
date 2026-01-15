# Инструкция по развертыванию проекта Blogicum

## Системные требования

- Python 3.10 или выше
- pip (менеджер пакетов Python)
- Git
- 50 MB свободного места на диске

## Пошаговая установка

### 1. Подготовка окружения

#### Windows:
```powershell
# Клонирование репозитория
git clone <your-repo-url> django_sprint4
cd django_sprint4

# Создание виртуального окружения
python -m venv venv

# Активация виртуального окружения
.\venv\Scripts\Activate.ps1

# Установка зависимостей
pip install -r requirements.txt
```

#### Linux/Mac:
```bash
# Клонирование репозитория
git clone <your-repo-url> django_sprint4
cd django_sprint4

# Создание виртуального окружения
python3 -m venv venv

# Активация виртуального окружения
source venv/bin/activate

# Установка зависимостей
pip install -r requirements.txt
```

### 2. Настройка базы данных

```bash
# Применение миграций
python manage.py migrate

# Создание суперпользователя
python manage.py createsuperuser
```

### 3. Загрузка тестовых данных (опционально)

```bash
python manage.py loaddata db.json
```

### 4. Запуск сервера разработки

```bash
python manage.py runserver
```

Откройте в браузере: http://127.0.0.1:8000/

## Настройка для продакшена

### 1. Измените SECRET_KEY

В файле `blogicum/settings.py`:
```python
import secrets

SECRET_KEY = secrets.token_urlsafe(50)
```

### 2. Отключите DEBUG

```python
DEBUG = False

ALLOWED_HOSTS = ['your-domain.com', 'www.your-domain.com']
```

### 3. Настройте базу данных

Замените SQLite на PostgreSQL:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'blogicum_db',
        'USER': 'blogicum_user',
        'PASSWORD': 'your-password',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

### 4. Настройте статические файлы

```python
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

Затем выполните:
```bash
python manage.py collectstatic
```

### 5. Настройте реальный email-бэкенд

Для Gmail:
```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = 'your-email@gmail.com'
EMAIL_HOST_PASSWORD = 'your-app-password'
```

### 6. Используйте переменные окружения

Создайте файл `.env`:
```
SECRET_KEY=your-secret-key
DEBUG=False
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-password
```

Установите python-decouple:
```bash
pip install python-decouple
```

В `settings.py`:
```python
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

## Структура файлов проекта

```
django_sprint4/
├── blog/                    # Приложение блога
├── pages/                   # Статические страницы
├── blogicum/               # Настройки проекта
├── templates/              # HTML-шаблоны
├── static/                 # Статические файлы (CSS, JS, img)
├── media/                  # Загружаемые файлы пользователей
├── sent_emails/           # Email-файлы (для разработки)
├── tests/                  # Тесты
├── manage.py              # Утилита управления Django
├── requirements.txt       # Зависимости Python
├── .gitignore            # Игнорируемые Git файлы
└── README.md             # Документация
```

## Основные URL-адреса

- `/` - Главная страница
- `/posts/create/` - Создание поста
- `/posts/<id>/` - Просмотр поста
- `/posts/<id>/edit/` - Редактирование поста
- `/posts/<id>/delete/` - Удаление поста
- `/profile/<username>/` - Профиль пользователя
- `/category/<slug>/` - Посты по категории
- `/auth/registration/` - Регистрация
- `/auth/login/` - Вход
- `/auth/logout/` - Выход
- `/admin/` - Админ-панель

## Возможные проблемы

### Ошибка импорта PIL
```bash
pip install --upgrade Pillow
```

### Ошибка с миграциями
```bash
python manage.py makemigrations
python manage.py migrate
```

### Проблемы с правами доступа к media/
```bash
# Linux/Mac
chmod 755 media/
```

### Сервер не запускается
Проверьте, что порт 8000 не занят:
```bash
# Windows
netstat -ano | findstr :8000

# Linux/Mac
lsof -i :8000
```

## Тестирование

Запуск всех тестов:
```bash
pytest
```

Запуск конкретного теста:
```bash
pytest tests/test_post.py
```

С подробным выводом:
```bash
pytest -v
```

## Поддержка

Если возникли проблемы:
1. Проверьте логи в консоли
2. Убедитесь, что все зависимости установлены
3. Проверьте настройки в `settings.py`
4. Обратитесь к документации Django: https://docs.djangoproject.com/

## Лицензия

MIT License - см. файл LICENSE
