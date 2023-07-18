# Explore Turbo Drive (Page Navigation)

## Objective

1. Learn how page navigation works in Turbo Drive
1. Understand what is `cache` in Turbo Drive and how `preview` works.

## List Page

Update *hotwire_django_app/turbo_drive/views.py*

```python
from django.shortcuts import render, redirect

from hotwire_django_app.tasks.forms import TaskForm
from hotwire_django_app.tasks.models import Task


def list_view(request):
    object_list = Task.objects.all().order_by('-pk')

    context = {
        "object_list": object_list,
    }

    return render(request, 'turbo_drive/list.html', context)
```

Notes:

1. We add a simple list view using Django FBV

### Template

Create *hotwire_django_app/templates/turbo_drive/list.html*

```html
{% extends "turbo_drive/base.html" %}

{% block content %}

  <div class="w-full max-w-7xl mx-auto px-4">
    <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Task List</h1>
    <div class="md:w-2/3 bg-white rounded-lg border mb-4">
      <ul class="divide-y-2 divide-gray-100" id="task-list-ul">
        {% for instance in object_list %}
          <li class="p-3 flex items-center">
            {{ instance.due_date|date:"Y-m-d" }}: {{ instance.title }}
          </li>
        {% endfor %}
      </ul>
    </div>
  </div>

{% endblock %}
```

### URL

Update *hotwire_django_app/turbo_drive/urls.py*

```python
from django.urls import path
from .views import create_view, list_view

app_name = 'turbo-drive'

urlpatterns = [
    path('list/', list_view, name='task-list'),                # new
    path('create/', create_view, name='task-create'),
]
```

Now test on the [http://127.0.0.1:8000/turbo-drive/list/](http://127.0.0.1:8000/turbo-drive/list/) and we should see the task list page.

## Add NavBar

Next, let's put some navigation links at the top of the page.

Create *hotwire_django_app/templates/turbo_drive/navbar.html*

```html
<nav class="flex items-center justify-between flex-wrap bg-teal-500 p-6 mb-4">
  <div class="w-full">
    <a href="{% url 'turbo-drive:task-list' %}" class="inline-block mt-0 text-teal-200 hover:text-white mr-4">
      List
    </a>
    <a href="{% url 'turbo-drive:task-create' %}" class="inline-block mt-0 text-teal-200 hover:text-white mr-4">
      Create
    </a>
  </div>
</nav>
```

Update *hotwire_django_app/templates/turbo_drive/base.html*

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  {% stylesheet_pack 'turbo_drive' %}
  {% javascript_pack 'turbo_drive' attrs='defer' %}
</head>
<body>

{% include 'turbo_drive/navbar.html' %}                <!-- new --> 

{% block content %}
{% endblock content %}

</body>
</html>
```

Now we can click links on the top navbar to jump between the list and the create view.

## How Turbo Drive Works

In the previous chapter, we have imported Turbo in *frontend/src/application/turbo_drive.js*

Next, we can dive deeper to learn how Turbo Drive works.

From the [https://turbo.hotwired.dev/handbook/drive#page-navigation-basics](https://turbo.hotwired.dev/handbook/drive#page-navigation-basics)

> Turbo Drive is the part of Turbo that enhances page-level navigation. It watches for link clicks and form submissions, performs them in the background, and updates the page without doing a full reload. It’s the evolution of a library previously known as Turbolinks.

## Page Navigation Basics

Turbo Drive models page navigation as a visit to a `location` (URL) with an `action`.

There are two types of visit in Turbo Drive:

### Application Visits (standard navigation)

This happens in most cases.

1. When the user click links, the visits starts and Turbo Drive sends a HTTP request in the background 
1. If possible, Turbo will render `preview` of the page from the cache after the visits starts.
1. Then Turbo will change browser history, for example: pushes a new entry onto the browser’s history
1. When Turbo receives the response, it will render the HTML.

### Restoration Visits

> Turbo Drive automatically initiates a restoration visit when you navigate with the browser’s Back or Forward buttons

In `Restoration Visits`, Turbo Drive will restore the page from cache **without loading a fresh copy from the network**, if possible

## Simple Tests

### Test 1

1. Visit [http://127.0.0.1:8000/turbo-drive/create/](http://127.0.0.1:8000/turbo-drive/create/), and add random string to the `title` field
1. Not submit the form, but click the top `List` link.
1. Now click the browser back button, Turbo will do `Restoration Visits`, restore the page from the cache (without sending request)
1. The `title` field already has value we just typed.

### Test 2

Let's add some delay to the `create_view` and `list_view`

Update *hotwire_django_app/turbo_drive/views.py*

```python
from django.shortcuts import render, redirect, reverse

from hotwire_django_app.tasks.forms import TaskForm
from hotwire_django_app.tasks.models import Task


def create_view(request):
    import time                                      # new
    time.sleep(1)
    if request.method == 'POST':
        form = TaskForm(request.POST)
        if form.is_valid():
            form.save()

            return redirect(reverse('turbo-drive:task-list'))     # update
    else:
        form = TaskForm()

    return render(request, 'turbo_drive/create.html', {'form': form})


def list_view(request):
    import time
    time.sleep(1)                                   # new
    object_list = Task.objects.all().order_by('-pk')

    context = {
        "object_list": object_list,
    }

    return render(request, 'turbo_drive/list.html', context)
```

Notes:

1. Try to visit [http://127.0.0.1:8000/turbo-drive/create/](http://127.0.0.1:8000/turbo-drive/create/) in a new browser tab
1. You will see browser blank page for about 2 seconds, and then the page will render.
1. Add some text in the title field, do NOT submit the form.
1. Then click top `List` link, you will see `a blue progress bar` on the top, after 2 seconds, the list page will render.
1. Then click `Create` link, Turbo will restore the page from the cache while `sending HTTP request`. So you can see the form page **immediately**, after 2 seconds, the Turbo receive the response, it will then use the response to update the page, and the text in the title field will disappear.

> During Turbo Drive navigation, the browser will not display its native progress indicator. Turbo Drive installs a CSS-based progress bar to provide feedback while issuing a request.

> The progress bar is enabled by default. It appears automatically for any page that takes longer than `500ms` to load. 

As you can see, Turbo makes the page navigation more smooth, while bringing better user experience.
