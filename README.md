pip install django

django-admin startproject myproject
cd myproject


python manage.py startapp myapp


# myproject/settings.py
INSTALLED_APPS = [
    ...
    'django.contrib.flatpages',
    'myapp',
]


python manage.py migrate


# myproject/settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / "static",  # Папка для пользовательских статических файлов
]


# myproject/urls.py
from django.contrib import admin
from django.urls import path, include
from django.contrib.flatpages import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('pages/', include('django.contrib.flatpages.urls')),
]


mkdir -p myapp/templates/myapp
mkdir -p static/bootstrap


<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}Мой сайт{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'bootstrap/css/bootstrap.min.css' %}">
    <style>
        /* Подключите ваши пользовательские стили здесь */
        .custom-font {
            font-family: Arial, sans-serif;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <!-- Навигация -->
    </nav>

    <div class="container">
        {% block content %}{% endblock %}
    </div>
</body>
</html>


{% extends "myapp/base.html" %}
{% load static %}

{% block title %}{{ flatpage.slug|title }}{% endblock %}
{% block content %}
    <div class="custom-font">
        {{ flatpage.content|safe }}
    </div>
{% endblock %}


# В myapp/views.py добавьте
from django.contrib.auth.decorators import user_passes_test

@user_passes_test(lambda u: u.is_superuser)  # Замените на ваш условие
def admin_only_view(request):
    return render(request, 'myapp/flatpage.html', {'flatpage': flatpage})


python manage.py runserver
