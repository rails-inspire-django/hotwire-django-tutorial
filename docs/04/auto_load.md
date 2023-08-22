# Auto Load

## Eager Load

```html
<turbo-frame id="messages" src="/messages">
  Content will be replaced when /messages has been loaded.
</turbo-frame>
```

## Lazy Load

```html
<turbo-frame id="messages" src="/messages" loading="lazy">
  Content will be replaced when the frame becomes visible and /messages has been loaded.
</turbo-frame>
```

## Demo

[https://hotwire-django.fly.dev/turbo-frame/](https://hotwire-django.fly.dev/turbo-frame/)

The form and list part would be loaded from the server when the page is loaded.

## Reference

[Turbo Frame Reference](https://turbo.hotwired.dev/reference/frames)