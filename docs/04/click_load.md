# Click to Load

## Turbo Frame

> Any links and forms inside a Turbo frame are captured, and the frame contents automatically updated after receiving a response. Regardless of whether the server provides a full document, or just a fragment containing an updated version of the requested frame, only that particular frame will be extracted from the response to **replace** the existing content.

```html
<body>

    <turbo-frame id="messages">
      <a href="/messages/expanded">
        Show all expanded messages in this frame.
      </a>

      <form action="/messages">
        Show response from this form within this frame.
      </form>
    </turbo-frame>

</body>
```

Notes:

1. On the server side, we can return full page which contains `<turbo-frame id="messages">`, or just return a fragment which contains `<turbo-frame id="messages">`.
2. The content within the `<turbo-frame id="messages">` will be replaced by the response from the server.

## Turbo Stream

By default, Turbo Frame would `replace` the content within the respective `turbo-frame` with the response from the server.

If you want to click to load content from the server and **add** it to the current page, you need `Turbo Stream`

> Turbo Streams deliver page changes as fragments of HTML wrapped in self-executing turbo-stream elements. Each stream element specifies an action together with a target ID to declare what should happen to the HTML inside it

```html
<turbo-stream action="append" target="messages">
  <template>
    <div id="message_1">
      This div will be appended to the element with the DOM ID "messages".
    </div>
  </template>
</turbo-stream>

<turbo-stream action="prepend" target="messages">
  <template>
    <div id="message_1">
      This div will be prepended to the element with the DOM ID "messages".
    </div>
  </template>
</turbo-stream>

<turbo-stream action="replace" target="message_1">
  <template>
    <div id="message_1">
      This div will replace the existing element with the DOM ID "message_1".
    </div>
  </template>
</turbo-stream>
```

On the server side, you can return fragment which contains `turbo-stream` to make it work.

## Demo

### Test 1

[https://hotwire-django.fly.dev/stimulus-basic/turbo_frame_load/](https://hotwire-django.fly.dev/stimulus-basic/turbo_frame_load/)

Click the button to load content from the server.

### Test 2

[https://hotwire-django.fly.dev/turbo-stream/](https://hotwire-django.fly.dev/turbo-stream/)

Click the pagination link, and the list content would be updated without reloading the full page.

## Reference

1. [Decompose with Turbo Frames](https://turbo.hotwired.dev/handbook/frames)
1. [Come Alive with Turbo Streams](https://turbo.hotwired.dev/handbook/streams)
