Отлично! Все компоненты получены. Создаю комплексный README для вашего Django блога.

# 🚀 Django Blog CBV Project

<div align="center">

![Django](https://img.shields.io/badge/Django-5.2.6-green?style=for-the-badge&logo=django)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-blue?style=for-the-badge&logo=postgresql)
![Bootstrap](https://img.shields.io/badge/Bootstrap-5.3-purple?style=for-the-badge&logo=bootstrap)

**Современный блог на Django с древовидными комментариями, системой рейтингов и тегами**

</div>

## 📖 О проекте

Django Blog CBV - это полнофункциональная блог-платформа с современным стеком технологий. Проект реализован с использованием Class-Based Views (CBV) и включает передовые практики Django разработки.

## 🛠 Технологический стек

### Backend
- **Python 3.11+** - основной язык программирования
- **Django 5.2.6** - веб-фреймворк
- **PostgreSQL** - система управления базами данных
- **Django MPTT** - древовидные структуры данных
- **Django Taggit** - система тегов
- **Django CKEditor** - визуальный редактор контента

### Frontend
- **Bootstrap 5.3** - CSS-фреймворк
- **JavaScript (ES6+)** - интерактивность
- **AJAX** - асинхронные запросы

### Безопасность
- **reCAPTCHA v2** - защита форм
- **FileValidation** - валидация загружаемых файлов
- **IP-based rating** - контроль рейтингов по IP

## 📦 Установка и запуск

### Предварительные требования
- Python 3.11+
- PostgreSQL 12+
- Virtualenv

### 1. Клонирование репозитория
```bash
git clone <repository-url>
cd Sec2-proj-master
```

### 2. Создание виртуального окружения
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# или
venv\Scripts\activate  # Windows
```

### 3. Установка зависимостей
```bash
pip install -r requirements.txt
```

### 4. Настройка окружения
Создайте файл `.env` в корне проекта:
```env
SECRET_KEY=your-django-secret-key
BD_PASSWORD=your-postgres-password
RECAPTCHA_PUBLIC_KEY=your-recaptcha-public-key
RECAPTCHA_PRIVATE_KEY=your-recaptcha-private-key
```

### 5. Настройка базы данных
```sql
CREATE DATABASE blog_cbv;
CREATE USER blog WITH PASSWORD 'your-password';
GRANT ALL PRIVILEGES ON DATABASE blog_cbv TO blog;
```

### 6. Миграции и суперпользователь
```bash
python manage.py migrate
python manage.py createsuperuser
```

### 7. Запуск разработческого сервера
```bash
python manage.py runserver
```

## 🗄 Модели данных

### 👤 Система пользователей (accounts)
```python
class Profile(models.Model):
    user = OneToOneField(User)
    slug = SlugField(unique=True)  # Автогенерация
    avatar = ImageField(validators=[FileExtensionValidator])
    bio = TextField()
    birth_date = DateField()
    
    def is_online(self)  # Статус онлайн через кэш
```

### 📝 Блог-система (blog)
```python
class Post(models.Model):
    title, slug, description, text = RichTextField(CKEditor)
    category = TreeForeignKey(Category)  # MPTT вложенность
    tags = TaggableManager()  # Django-taggit
    status = ['published', 'draft']
    ratings = GenericRelation(Rating)  # Лайки/дизлайки
    
    objects = PostManager()  # Кастомный менеджер
```

### 💬 Комментарии (MPTT)
```python
class Comment(MPTTModel):
    post = ForeignKey(Post)
    author = ForeignKey(User)
    content = TextField()
    parent = TreeForeignKey('self')  # Древовидная структура
    status = ['published', 'draft']
```

### ⭐ Рейтинги
```python
class Rating(models.Model):
    post = ForeignKey(Post)
    user = ForeignKey(User, null=True)
    value = [(1, 'Нравится'), (-1, 'Не нравится')]
    ip_address = GenericIPAddressField()  # Контроль по IP
```

## 🚀 Основной функционал

### 📊 Блог-система
- **Создание/редактирование** постов с CKEditor
- **Категории** с древовидной структурой (MPTT)
- **Теги** с автоматическим предложением
- **Статусы** публикации (черновик/опубликовано)
- **Закрепленные** посты

### 💭 Система комментариев
- **Древовидные комментарии** с неограниченной вложенностью
- **AJAX-добавление** без перезагрузки страницы
- **Модерация** комментариев
- **Время создания** с локализацией

### ⭐ Система рейтингов
- **Лайки/дизлайки** для постов
- **IP-контроль** для анонимных пользователей
- **AJAX-обновление** рейтинга
- **Уникальность** по IP + пользователь

### 👤 Пользовательская система
- **Расширенные профили** с аватарами
- **Онлайн-статус** через кэширование
- **Регистрация** с reCAPTCHA
- **Редактирование профиля** с транзакциями

## 📡 API Endpoints

### Основные маршруты
| Метод | URL | Назначение |
|-------|-----|------------|
| GET | `/` | Главная страница с постами |
| GET | `/post/<slug>/` | Детали поста |
| GET | `/category/<slug>/` | Посты по категории |
| GET | `/post/tags/<tag>/` | Посты по тегу |

### Пользовательские маршруты
| Метод | URL | Назначение |
|-------|-----|------------|
| GET/POST | `/register/` | Регистрация |
| GET/POST | `/login/` | Авторизация |
| POST | `/logout/` | Выход |
| GET | `/user/<slug>/` | Профиль пользователя |
| GET/POST | `/user/edit/` | Редактирование профиля |

### AJAX endpoints
| Метод | URL | Назначение |
|-------|-----|------------|
| POST | `/rating/` | Добавление рейтинга |
| POST | `/post/<pk>/comments/create/` | Добавление комментария |

## 🎯 Админ-панель

### Модели в админке
- **Посты** - с предзаполнением slug
- **Категории** - древовидный интерфейс (Django MPTT Admin)
- **Комментарии** - древовидное управление
- **Рейтинги** - просмотр оценок
- **Профили** - управление пользователями

### Особенности админки
```python
@admin.register(Category)
class CategoryAdmin(DjangoMpttAdmin):  # Древовидный интерфейс
    prepopulated_fields = {'slug': ('title',)}
```

## 🔧 Кастомные компоненты

### Миксины
```python
class AuthorRequiredMixin(AccessMixin):
    """Контроль доступа к редактированию постов"""
    def dispatch(self, request, *args, **kwargs):
        if not (request.user == self.get_object().author):
            messages.info(request, 'Изменение статьи доступно только автору!')
            return redirect('home')
```

### Утилиты
```python
def unique_slugify(instance, slug, slug_field):
    """Генерация уникальных slug с проверкой существования"""
    if model.objects.filter(slug=slug_field).exists():
        slug_field = f'{slugify(slug)}-{uuid4().hex[:8]}'
    return slug_field
```

### Мидлвари
```python
class ActiveUserMiddleware(MiddlewareMixin):
    """Отслеживание онлайн-статуса пользователей"""
    def process_request(self, request):
        if request.user.is_authenticated:
            cache_key = f'last-seen-{request.user.id}'
            cache.set(cache_key, timezone.now(), 300)
```

## 🎨 Frontend особенности

### Шаблоны
- **Bootstrap 5.3** - современный UI
- **Наследование шаблонов** - DRY принцип
- **Включения** - переиспользуемые компоненты
- **Пагинация** с элидированным диапазоном

### JavaScript функционал
- **AJAX комментарии** - динамическое добавление
- **Система рейтингов** - интерактивное голосование
- **Динамические формы** - улучшенный UX

## ⚙️ Настройки проекта

### Основные настройки
```python
# База данных
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'blog_cbv',
        'USER': 'blog',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}

# Кэширование
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': BASE_DIR / 'cache',
    }
}
```

### Установленные приложения
```python
INSTALLED_APPS = [
    'apps.blog', 'apps.accounts',           # Кастомные приложения
    'mptt', 'django_mptt_admin',           # Древовидные структуры
    'taggit',                              # Система тегов
    'ckeditor', 'ckeditor_uploader',       # Визуальный редактор
    'django_recaptcha',                    # Защита форм
    'debug_toolbar',                       # Отладка
]
```

## 🧪 Тестирование

### Запуск тестов
```bash
python manage.py test apps.blog
python manage.py test apps.accounts
```

### Генерация тестовых данных
```python
# Пример создания тестовых постов
from apps.blog.models import Post, Category
from django.contrib.auth.models import User

user = User.objects.get(username='admin')
category = Category.objects.first()

Post.objects.create(
    title='Тестовый пост',
    author=user,
    category=category,
    description='Краткое описание',
    text='Полный текст поста',
    status='published'
)
```

## 🐛 Обработка ошибок

### Кастомные обработчики
- **404** - Страница не найдена
- **500** - Ошибка сервера  
- **403** - Доступ запрещен

```python
def tr_handler404(request, exception):
    return render(request, 'errors/error_page.html', status=404, context={
        'title': 'Страница не найдена: 404',
        'error_message': 'К сожалению такая страница была не найдена',
    })
```

## 📁 Структура проекта

```
blog_cbv/
├── apps/
│   ├── accounts/          # Система пользователей
│   ├── blog/              # Блог-система
│   └── services/          # Общие утилиты
├── templates/             # HTML шаблоны
│   ├── accounts/          # Шаблоны аккаунтов
│   ├── blog/              # Шаблоны блога
│   └── includes/          # Включаемые шаблоны
├── blog_cbv/              # Настройки проекта
└── manage.py              # Точка входа
```

## 🔮 Возможности расширения

- [ ] REST API на Django REST Framework
- [ ] Кэширование с Redis
- [ ] Elasticsearch для поиска
- [ ] WebSocket для чата
- [ ] Экспорт данных
- [ ] Многоязычность

## 👥 Разработчики

Проект разработан с использованием лучших практик Django:
- **Class-Based Views** для повторного использования кода
- **MPTT** для эффективной работы с иерархическими данными
- **Сигналы** для автоматизации процессов
- **Миксины** для расширения функциональности

---

<div align="center">

## 📦 Установка и запуск

### Предварительные требования
- Python 3.11+
- PostgreSQL 12+
- Virtualenv

### 1. Клонирование репозитория
```bash
git clone <repository-url>
cd Sec2-proj-master
```

### 2. Создание виртуального окружения
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# или
venv\Scripts\activate  # Windows
```

### 3. Установка зависимостей
```bash
pip install -r requirements.txt
```

### 4. Настройка окружения
Создайте файл `.env` в корне проекта:
```env
SECRET_KEY=your-django-secret-key
BD_PASSWORD=your-postgres-password
RECAPTCHA_PUBLIC_KEY=your-recaptcha-public-key
RECAPTCHA_PRIVATE_KEY=your-recaptcha-private-key
```

### 5. Настройка базы данных
```sql
CREATE DATABASE blog_cbv;
CREATE USER blog WITH PASSWORD 'your-password';
GRANT ALL PRIVILEGES ON DATABASE blog_cbv TO blog;
```

### 6. Миграции и суперпользователь
```bash
python manage.py migrate
python manage.py createsuperuser
```

### 7. Запуск разработческого сервера
```bash
python manage.py runserver
```

**Django Blog CBV** - современное решение для блоговых платформ с открытым исходным кодом.

[Документация Django](https://docs.djangoproject.com/) | [MPTT Documentation](https://django-mptt.readthedocs.io/)

</div>
