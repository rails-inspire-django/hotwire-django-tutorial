# Prepare to Learn Turbo Frame

## Objective

1. Prepare Django app for us to learn `Turbo Frame`

## Django App

Let's create `turbo_frame` app, we will learn how `Turbo Frame` works with this Django app.

```bash
(venv)$ mkdir -p ./hotwire_django_app/turbo_frame
(venv)$ python manage.py startapp turbo_frame ./hotwire_django_app/turbo_frame
```

```
./hotwire_django_app
├── __init__.py
├── asgi.py
├── settings.py
├── tasks
├── templates
├── turbo_drive
├── turbo_frame                     # new
├── urls.py
└── wsgi.py
```

Update *hotwire_django_app/turbo_frame/apps.py* to change the name to `hotwire_django_app.turbo_frame`

```python
from django.apps import AppConfig


class TurboFrameConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'hotwire_django_app.turbo_frame'              # update
```

Add `hotwire_django_app.turbo_frame` to the `INSTALLED_APPS` in *hotwire_django_app/settings.py*

```python
INSTALLED_APPS = [
	...
    'hotwire_django_app.turbo_frame',                  # new
]
```

```bash
# check if there is any error
$ ./manage.py check

System check identified no issues (0 silenced).
```

## View

Edit *hotwire_django_app/turbo_frame/views.py*

```python
import http

from django.shortcuts import render, redirect, get_object_or_404
from django.urls import reverse
from django.contrib import messages

from hotwire_django_app.tasks.models import Task
from hotwire_django_app.tasks.forms import TaskForm


def list_view(request):
    object_list = Task.objects.all().order_by('-pk')

    context = {
        "object_list": object_list,
    }

    return render(request, 'turbo_frame/list_page.html', context)


def create_view(request):
    if request.method == 'POST':
        form = TaskForm(request.POST)
        if form.is_valid():
            instance = form.save()

            messages.success(request, 'Task created successfully')
            return redirect(reverse('turbo-frame:task-detail', kwargs={'pk': instance.pk}))

        status = http.HTTPStatus.UNPROCESSABLE_ENTITY
    else:
        status = http.HTTPStatus.OK
        form = TaskForm()

    return render(request, 'turbo_frame/create_page.html', {'form': form}, status=status)


def update_view(request, pk):
    instance = get_object_or_404(Task, pk=pk)
    if request.method == 'POST':
        form = TaskForm(request.POST, instance=instance)
        if form.is_valid():
            form.save()

            messages.success(request, 'Task update successfully')
            return redirect(reverse('turbo-frame:task-detail', kwargs={'pk': instance.pk}))

        status = http.HTTPStatus.UNPROCESSABLE_ENTITY
    else:
        status = http.HTTPStatus.OK
        form = TaskForm(instance=instance)

    return render(request, 'turbo_frame/update_page.html', {'form': form}, status=status)


def delete_view(request, pk):
    instance = get_object_or_404(Task, pk=pk)
    if request.method == 'POST':
        instance.delete()

        messages.success(request, 'Task deleted successfully')
        return redirect('turbo-frame:task-list')

    return render(request, 'turbo_frame/delete_page.html', {'instance': instance})


def detail_view(request, pk):
    instance = get_object_or_404(Task, pk=pk)
    return render(request, 'turbo_frame/detail_page.html', {'instance': instance})
```

Notes:

1. Here we create 4 FBV, which are for CRUD work.
1. If task is created or updated successfully, user would be redirected to the task detail page.
1. If task is deleted successfully, user would be redirected to the task list page.

## Template

### base.html

create *hotwire_django_app/templates/turbo_frame/base.html*

```html
{% load webpack_loader static %}

<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  {% stylesheet_pack 'turbo_frame' attrs='data-turbo-track="reload"' %}
  {% javascript_pack 'turbo_frame' attrs='data-turbo-track="reload" defer' %}

</head>
<body>

{% include 'turbo_frame/navbar.html' %}

{% include 'turbo_frame/messages.html' %}

{% block content %}
{% endblock content %}

</body>
</html>
```

Notes:

1. Please note in this Django app, we will use `turbo_frame` entry file (`{% javascript_pack 'turbo_frame'`). we will create it in a bit.

Create *hotwire_django_app/templates/turbo_frame/messages.html*

```html
{% for message in messages %}
{#  data-turbo-cache="false" will tell Turbo to not cache the element #}
<div data-turbo-cache="false" class="p-4 mb-4 text-sm text-green-700 bg-green-100 rounded-lg dark:bg-green-200 dark:text-green-800" role="alert">
  {{ message|safe }}
</div>
{% endfor %}
```

Create *hotwire_django_app/templates/turbo_frame/navbar.html*

```html
<nav class="flex items-center justify-between flex-wrap bg-teal-500 p-6 mb-4">
  <div class="w-full">
    <a href="{% url 'turbo-frame:task-list' %}" class="inline-block mt-0 text-teal-200 hover:text-white mr-4">
      List
    </a>
    <a href="{% url 'turbo-frame:task-create' %}" class="inline-block mt-0 text-teal-200 hover:text-white mr-4">
      Create
    </a>
  </div>
</nav>
```

Please note the URL namespace is `turbo-frame` now.

### create_page.html

Create *hotwire_django_app/templates/turbo_frame/create_page.html*

```html
{% extends "turbo_frame/base.html" %}
{% load crispy_forms_tags %}

{% block content %}

<div class="w-full max-w-7xl mx-auto px-4">

  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Create Task</h1>

  <form method="post">
    {% csrf_token %}

    {{ form|crispy }}

    <button type="submit" class="btn-blue">Submit</button>
  </form>

</div>

{% endblock %}
```

### update_page.html

create *hotwire_django_app/templates/turbo_frame/update_page.html*

```html
{% extends "turbo_frame/base.html" %}
{% load crispy_forms_tags %}

{% block content %}
<div class="w-full max-w-7xl mx-auto px-4">

  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Edit Task</h1>

  <form method="post">

    {% csrf_token %}

    {{ form|crispy }}

    <button type="submit" class="btn-blue">Submit</button>

    <a href="{% url 'turbo-frame:task-list' %}" class="btn-red">Cancel</a>

  </form>
</div>

{% endblock %}
```

### delete_page.html

Create *hotwire_django_app/templates/turbo_frame/delete_page.html*

```html
{% extends "turbo_frame/base.html" %}
{% load crispy_forms_tags %}

{% block content %}
<div class="w-full max-w-7xl mx-auto px-4">
  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Delete Task</h1>

  <form method="post">
    {% csrf_token %}

    <div class="p-4 mb-4 text-sm text-red-700 bg-red-100 rounded-lg dark:bg-red-200 dark:text-red-800" role="alert">
      Are you sure you want to delete "{{ instance.title }}"?
    </div>

    {{ form|crispy }}

    <button type="submit" class="btn-blue">Submit</button>

    <a href="{% url 'turbo-frame:task-list' %}" class="btn-red">Cancel</a>

  </form>
</div>
{% endblock %}
```

### list_page.html

Create *hotwire_django_app/templates/turbo_frame/list_page.html*

```html
{% extends "turbo_frame/base.html" %}

{% block content %}

<div class="w-full max-w-7xl mx-auto px-4">
  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Task List</h1>

  <div class="md:w-2/3 bg-white rounded-lg border mb-4">
    <ul class="divide-y-2 divide-gray-100" id="task-list-ul">
      {% for instance in object_list %}
      <li class="p-3 flex items-center">

        {% include 'turbo_frame/task_detail.html' with instance=instance only %}

      </li>
      {% endfor %}
    </ul>
  </div>

</div>

{% endblock %}
```

### detail_page.html

Create *hotwire_django_app/templates/turbo_frame/detail_page.html*

```html
{% extends "turbo_frame/base.html" %}
{% load crispy_forms_tags %}

{% block content %}
<div class="w-full max-w-7xl mx-auto px-4">
  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Task Detail</h1>

  <div class="mb-3 p-3 border">
    {% include 'turbo_frame/task_detail.html' with instance=instance only %}
  </div>

  <div>
    <a href="{% url 'turbo-frame:task-list'%}" class="btn-blue">Go to list</a>
  </div>

</div>
{% endblock %}
```

### task_detail.html

Create *hotwire_django_app/templates/turbo_frame/task_detail.html*

```html
<a class="btn-blue mr-3" href="{% url 'turbo-frame:task-update' instance.pk %}">
  Edit
</a>
<a class="btn-red mr-3" href="{% url 'turbo-frame:task-delete' instance.pk %}">
  Delete
</a>

{{ instance.due_date }}: {{ instance.title }}
```

This template is used in both `list_page.html` and `detail_page.html`

Now we have file structure like this

```
./turbo_frame
├── base.html
├── create_page.html
├── delete_page.html
├── detail_page.html
├── list_page.html
├── messages.html
├── navbar.html
├── task_detail.html
└── update_page.html
```

## Frontend

Create *frontend/src/styles/turbo_frame.scss*

```scss
@import "tailwindcss/base";
@import "tailwindcss/components";
@import "tailwindcss/utilities";

.btn-blue {
  @apply inline-flex items-center;
  @apply px-4 py-2;
  @apply font-semibold rounded-lg shadow-md;
  @apply text-white bg-blue-500;
  @apply hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-400 focus:ring-opacity-75;
}

.btn-red {
  @apply inline-flex items-center;
  @apply px-4 py-2;
  @apply font-semibold rounded-lg shadow-md;
  @apply text-white bg-red-500;
  @apply hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-400 focus:ring-opacity-75;
}
```

Create *frontend/src/application/turbo_frame.js*

```js
// This is the scss entry file
import "../styles/turbo_frame.scss";

import "@hotwired/turbo";
```

Notes:

1. We import `turbo_frame.scss` we just created
1. And this JS entry file would be imported in the above `turbo_frame/base.html`

## URL

Create *hotwire_django_app/turbo_frame/urls.py*

```python
from django.urls import path
from .views import list_view, create_view, update_view, delete_view, detail_view

app_name = 'turbo-frame'

urlpatterns = [
    path('list/', list_view, name='task-list'),
    path('create/', create_view, name='task-create'),
    path('<int:pk>/', detail_view, name='task-detail'),
    path('<int:pk>/update/', update_view, name='task-update'),
    path('<int:pk>/delete/', delete_view, name='task-delete'),
]
```

Notes:

1. Here we use `app_name` to set the `namespace` of the Django app.

Update *hotwire_django_app/urls.py*

```python
from django.contrib import admin
from django.urls import path, include
from django.views.generic import TemplateView

urlpatterns = [
    path('', TemplateView.as_view(template_name="index.html")),
    path('turbo-drive/', include('hotwire_django_app.turbo_drive.urls')),
    path('turbo-frame/', include('hotwire_django_app.turbo_frame.urls')),              # new
    path('admin/', admin.site.urls),
]
```

## Manual Test

Restart `webpack`

```bash
$ npm run start
```

```bash
(venv)$ python manage.py runserver
```

1. Visit [http://127.0.0.1:8000/turbo-frame/list/](http://127.0.0.1:8000/turbo-frame/list/), you can see task list.
1. Try to click the top `Create` link and create a new Task.
1. Try to edit the existing Task.
1. Try to delete the existing Task.

## Conclusion

In this chapter, we created a basic CRUD app, next, we will use Turbo Frame to make it more interactive.
