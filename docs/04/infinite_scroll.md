# Infinite Scroll

## How it Works

Assume we have HTML code like this

```html
<ul id="task-list-ul">
  <li></li>
  <li></li>
  <li></li>
</ul>

<nav aria-label="Page navigation example" id="task-list-pagination">
  <ul>
      {% if next_url %}
        <a href="{{ next_url }}" data-turbo-stream>Load More</a>
      {% endif %}
    </li>
  </ul>
</nav>
```

Notes:

1. When link has `data-turbo-stream`, `data-turbo-stream` allows Turbo Streams to be used with GET requests as well.
2. On the server side, we can return Turbo Stream response to **append** `task item` to the `task-list-ul` and update the `task-list-pagination` with the new `previous` and `next` link.

This would make manual `Load More` work as expected.

## Auto Scroll

We can create a Stimulus Controler to auto click the `Load More` button when scrolling

```js
// https://github.com/stimulus-use/stimulus-use/blob/main/docs/use-intersection.md

import { Controller } from "@hotwired/stimulus"
import { useIntersection } from 'stimulus-use'

export default class extends Controller {
  options = {
    threshold: 1
  }

  connect() {
    useIntersection(this, this.options)
  }

  appear(entry) {
    this.element.click()
  }
}
```

## Reference

[https://www.colby.so/posts/infinite-scroll-with-turbo-streams-and-stimulus](https://www.colby.so/posts/infinite-scroll-with-turbo-streams-and-stimulus)
