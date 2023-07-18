# Explore Turbo Drive (Cache) 

## Objective

1. Dive deeper about cache in Turbo Drive
1. Learn how to disable cache in Turbo Drive.
1. Improve Page Navigation Style 

## turbo:before-cache

In the previous chapter, we learned Turbo save the page to cache and use it when we visit the page.

> Turbo Drive saves a copy of the current page to its cache just before rendering a new page

Update *frontend/src/application/turbo_drive.js*

```js
document.addEventListener("turbo:before-cache", function () {
  console.log('turbo:before-cache');
  const form = document.querySelector('form');
  if (form) {
    form.reset();
  }
});
```

Notes:

1. We add an event handler to listen to the `turbo:before-cache`, which fires before Turbo saves the current page to cache.
1. In the event handler, we use js to reset the form.

Let's do a test

1. Force reload [http://127.0.0.1:8000/turbo-drive/create/](http://127.0.0.1:8000/turbo-drive/create/)
1. Add some text to the `title` field.
1. Click the top `List` link.
1. Then click browser `Back` button, so Turbo will render cached content. (restoration visit)
1. We can see the `title` field has empty value.

As you can see, **we can hook `turbo:before-cache` to do some cleanup work (close Modal, close Dropdown list) and make the page content ready to be cached.**

Now, please comment out the event handler since I will talk about another solution soon.

## data-turbo-cache

> Annotate elements with `data-turbo-cache="false"` if you do not want Turbo to cache them. This is helpful for temporary elements, like flash notices.

### View

Let's update *hotwire_django_app/turbo_drive/views.py*

```python
from django.shortcuts import render, redirect, reverse
from django.contrib import messages                                               # new

from hotwire_django_app.tasks.forms import TaskForm
from hotwire_django_app.tasks.models import Task


def create_view(request):
    import time
    time.sleep(1)
    if request.method == 'POST':
        form = TaskForm(request.POST)
        if form.is_valid():
            form.save()

            messages.success(request, 'Task created successfully')                # new
            return redirect(reverse('turbo-drive:task-list'))
    else:
        form = TaskForm()

    return render(request, 'turbo_drive/create.html', {'form': form})
```

Notes:

1. After the `form.save()` method, we call `messages.success` to display success message on the next page.
1. And we redirect user to the `task-list` page.

### Template

Create *hotwire_django_app/templates/turbo_drive/messages.html*

```html
{% for message in messages %}
  {#  data-turbo-cache="false" will tell Turbo to not cache the element #}
  <div data-turbo-cache="false" class="p-4 mb-4 text-sm text-green-700 bg-green-100 rounded-lg dark:bg-green-200 dark:text-green-800" role="alert">
    {{ message|safe }}
  </div>
{% endfor %}
```

We have set `data-turbo-cache="false"` because we tell Turbo to ignore the flash messages when caching.

Update *hotwire_django_app/templates/turbo_drive/base.html*

```html
{% load webpack_loader static %}

<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  {% stylesheet_pack 'turbo_drive' %}
  {% javascript_pack 'turbo_drive' attrs='defer' %}
</head>
<body>

{% include 'turbo_drive/navbar.html' %}

{% include 'turbo_drive/messages.html' %}             <! --- new --->

{% block content %}
{% endblock content %}

</body>
</html>
```

### Test

1. Fully reload [http://127.0.0.1:8000/turbo-drive/create/](http://127.0.0.1:8000/turbo-drive/create/)
1. Input random `title` and submit the form, then you will be redirected to the `task-list` page, which display the `success` message on the top.
1. Then click the top `Create` page, wait for some seconds.
1. Then click browser `Back` button, Turbo will restore the list page from the cache.
1. You will see the `success` message is gone, Turbo does not cache the element because of the `data-turbo-cache="false"`

Please note, in new version of Turbo, `[data-turbo-temporary]` is recommended to replace `[data-turbo-cache="false"]`

## turbo-cache-control

We can also control the cache behavior at page level using `<meta name="turbo-cache-control">` in `<head>`

### no-preview

```html
<head>
  <meta name="turbo-cache-control" content="no-preview">
</head>
```

1. Cached version of the page should not be shown as a preview during an `application visit`
1. Cached version will only be used for restoration visits.

### no-cache

```html
<head>
  <meta name="turbo-cache-control" content="no-cache">
</head>
```

1. Disable the page cache behavior.
1. The restoration visits will send a HTTP request.

To know more about Turbo cache, please check [Understanding Caching](https://turbo.hotwired.dev/handbook/building#understanding-caching)

## Improve Page Navigation Style

### data-turbo-preview

> Turbo Drive adds a data-turbo-preview attribute to the `<html>` element when it displays a preview from cache.

Let's add code below to the *frontend/src/styles/turbo_drive.scss*

```scss
[data-turbo-preview] body {
  opacity: 0.5;
}
```

This makes the page content a little transparent, which tell user the page content is not truly ready.

### turbo-progress-bar

> The progress bar is a `<div>` element with the class name turbo-progress-bar. Its default styles appear first in the document and can be overridden by rules that come later.

Let's add code below to the *frontend/src/styles/turbo_drive.scss*

```scss
.turbo-progress-bar {
  @apply bg-blue-500;

  height: 5px;
}
```
