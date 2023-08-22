# Click to Edit

## How it Works

This feature also called `Inline Editing`, we need `Turbo Frame` to make it work.

> Any links and forms inside a Turbo frame are captured, and the frame contents automatically updated after receiving a response. Regardless of whether the server provides a full document, or just a fragment containing an updated version of the requested frame, only that particular frame will be extracted from the response to **replace** the existing content.

Let's assume we have a list of todo items, and we want to edit the todo item by clicking the `Edit` button

```html

<li>
  <turbo-frame id="task-detail-{pk}" class="flex-1">
    <a href="/{pk}/update/">
      Edit
    </a>
    TODO item detail
  </turbo-frame>
</li>
```

Notes:

1. We use `turbo-frame` to wrap the todo item detail
2. If user click the `Edit` button, the server return fragment which contains the form to edit the todo item, and replace the content within the `turbo-frame`.

After Turbo update the page, the `li` element would be like this:

```html
<li>
  <turbo-frame id="task-detail-{pk}">
    <form method="post" action="/{pk}/update/">
      <button type="submit">Submit</button>
    </form>
  </turbo-frame>
</li>
```

The content of the `li` would be updated because of the `Turbo Frame`, and user can submit the form to update the todo item.

Once the todo item updated successfully, the server can still return `Turbo Frame` to update the todo item detail.

## Demo

[https://hotwire-django.fly.dev/turbo-stream/](https://hotwire-django.fly.dev/turbo-stream/)

You can click the `Edit` button to edit the todo item, and click the `Submit` button to update the todo item to see how it works.
