# Click to Delete

This example would teach you how to build a button and click to delete the `li` element in the list or `row` in table

## How it Works

> Turbo Streams deliver page changes as fragments of HTML wrapped in self-executing turbo-stream elements. Each stream element specifies an action together with a target ID to declare what should happen to the HTML inside it

```html
<!--Removes the element designated by the target dom id.-->
<turbo-stream action="remove" target="dom_id">
</turbo-stream>
```

If we plan to make some DOM elements to be deleted, we can assign unique ID and then use Turbo Stream which has `action="remove"`

## Demo

[https://hotwire-django.fly.dev/turbo-stream/](https://hotwire-django.fly.dev/turbo-stream/)

Try to delete item in the list and see how it works