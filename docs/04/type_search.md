# Type as Search

## How it Works

```html
<form action="{% url 'turbo-stream:task-list' %}" 
      method="get"
      data-controller="autocomplete"
      data-turbo-stream
>
  <input type="text" placeholder="Search" name="title" data-autocomplete-target="input" />
</form>
```

Notes:

1. We build a `autocomplete` Stimulus controller, and make it listen to the `input`
2. When user type in the `input` element, the `input` event would fire, we send a `GET` request to the server, and the server return `Turbo Stream` response to update list and pagination.

```js
import { Controller } from '@hotwired/stimulus';
import { debounce } from "../helpers";

export default class extends Controller {
  static targets = ["input"];

  connect() {
    if (this.hasInputTarget) {
      this.inputTarget.addEventListener("input", this.onInputChange);
    }
  }

  disconnect() {
    if (this.hasInputTarget) {
      this.inputTarget.removeEventListener("input", this.onInputChange);
    }
  }

  onInputChange = debounce(() => {
    this.loadResults();
  }, 500);

  loadResults() {
    this.element.requestSubmit();
  }
}
```

## Demo

[https://hotwire-django.fly.dev/turbo-stream/](https://hotwire-django.fly.dev/turbo-stream/)

Try to input some text in the search box and the list would be updated automatically.
