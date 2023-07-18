# Create Task App

## Objective

1. Create Task app as base application

## Tasks App

Let's first create django app `tasks`

```bash
# we put all django apps under the hotwire_django_app
(venv)$ mkdir -p ./hotwire_django_app/tasks
(venv)$ python manage.py startapp tasks ./hotwire_django_app/tasks
```

We will have structure like this

```
├── hotwire_django_app
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── tasks                      # new
│   │   ├── __init__.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── migrations
│   │   ├── models.py
│   │   ├── tests.py
│   │   └── views.py
│   ├── templates
│   │   └── index.html
│   ├── urls.py
│   └── wsgi.py
```

Update *hotwire_django_app/tasks/apps.py* to change the name to `hotwire_django_app.tasks`

```python
from django.apps import AppConfig


class TasksConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'hotwire_django_app.tasks'                 # update
```

Add `hotwire_django_app.tasks` to the `INSTALLED_APPS` in *hotwire_django_app/settings.py*

```python
INSTALLED_APPS = [
	...
    'hotwire_django_app.tasks',                  # new
]
```

```bash
# check if there is any error
(venv)$ ./manage.py check

System check identified no issues (0 silenced).
```

## Model

Update *hotwire_django_app/tasks/models.py*

```python
from django.db import models
from django.utils import timezone
from django.core.validators import MinLengthValidator


class Task(models.Model):
    title = models.CharField(
        max_length=250,
        validators=[MinLengthValidator(limit_value=3)]
    )
    due_date = models.DateField(default=timezone.now)
```

Migrate the db

```bash
(venv)$ python manage.py makemigrations
(venv)$ python manage.py migrate
```

## Form

Create *hotwire_django_app/tasks/forms.py*

```python
from django import forms
from .models import Task


class TaskForm(forms.ModelForm):

    class Meta:
        model = Task
        fields = ("title", "due_date")
```

Now the Django `tasks` app is created, all the next chapters will be around the Task model and the TaskForm.
