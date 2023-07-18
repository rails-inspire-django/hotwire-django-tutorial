# Stimulus Controller (Targets, CSS classes)

## Objective

1. Learn to reference important elements with Stimulus Targets
1. Learn to refer CSS classes in Stimulus Controller

## Improve style

Let's make the count number has `bold` style.

Update *hotwire_django_app/templates/stimulus_basic/counter.html*

```html
{% extends "stimulus_basic/base.html" %}

{% block content %}
<div class="w-full max-w-7xl mx-auto px-4">

  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Counter</h1>

  <button
    class="px-4 py-2 bg-blue-500 hover:bg-blue-700 text-white font-semibold rounded-lg"
    data-controller="counter"
    data-action="click->counter#increment"
  >
    You clicked <span class="font-bold"></span> times!
  </button>

  <h1 data-controller="counter" data-action="click->counter#increment" class="text-4xl sm:text-6xl lg:text-7xl mb-6">
    You clicked <span class="font-bold"></span> times!
  </h1>

  <div data-controller="counter" data-action="click->counter#increment" class="text-4xl">
    You clicked <span class="font-bold"></span> times!
  </div>

</div>

{% endblock %}
```

Notes:

1. We add `You clicked <span class="font-bold"></span> times!` element as child of our DOM element.

Update *frontend/src/controllers/counter_controller.js*

```js
import {Controller} from '@hotwired/stimulus';

export default class extends Controller {
  static values = {
    count: { type: Number, default: 0 },
  };

  connect() {
    this.element.querySelector('span').innerText = this.countValue;
  }

  countValueChanged(value, previousValue) {
    console.log(`${previousValue} changed to ${value}`);
  }

  increment(){
    this.countValue++;
    this.element.querySelector('span').innerText = this.countValue;
  }
}
```

Notes:

1. We use `this.element.querySelector('span')` to find the specific child element, and change `innerText` to make it work.
1. Even this can work, it seems tedious to use `this.element.querySelector('span')` and is there better way to do this?

## Target

> Targets let you reference important elements by name.

Update *hotwire_django_app/templates/stimulus_basic/counter.html*

```html
{% extends "stimulus_basic/base.html" %}

{% block content %}

<div class="w-full max-w-7xl mx-auto px-4">

  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Counter</h1>

  <button
    class="px-4 py-2 bg-blue-500 hover:bg-blue-700 text-white font-semibold rounded-lg"
    data-controller="counter"
    data-action="click->counter#increment"
  >
    You clicked <span class="font-bold" data-counter-target="count"></span> times!
  </button>

  <h1 data-controller="counter" data-action="click->counter#increment" class="text-4xl sm:text-6xl lg:text-7xl mb-6">
    You clicked <span class="font-bold" data-counter-target="count"></span> times!
  </h1>

  <div data-controller="counter" data-action="click->counter#increment" class="text-4xl">
    You clicked <span class="font-bold" data-counter-target="count"></span> times!
  </div>

</div>

{% endblock %}
```

1. We add `data-counter-target="count"` to the span element, the `counter` is the controller name, and `count` is the target name.

Update *frontend/src/controllers/counter_controller.js*

```js
import {Controller} from '@hotwired/stimulus';

export default class extends Controller {
  static values = {
    count: { type: Number, default: 0 },
  };
  static targets = ['count'];

  connect() {
    this.countTarget.innerText = this.countValue;
  }

  countValueChanged(value, previousValue) {
    console.log(`${previousValue} changed to ${value}`);
  }

  increment(){
    this.countValue++;
    this.countTarget.innerText = this.countValue;
  }
}
```

Notes:

1. We add static `targets` to the controller class.
1. **Now we can use `countTarget` to access the `span` element without DOM selection.**
1. We can even use `countTargets` to access all elements and use `hasCountTarget` to check if there is a matching target in scope. [https://stimulus.hotwired.dev/reference/targets#properties](https://stimulus.hotwired.dev/reference/targets#properties)

Please try to click the counters on the page, and they should work as expected.

With Stimulus Targets, we can make our Controller code more readable, since we do not need to use the DOM `Selectors API`

You can check [https://stimulus.hotwired.dev/reference/targets](https://stimulus.hotwired.dev/reference/targets) to learn more.

## Multiple Target

Let's keep improving our counters!

Update *hotwire_django_app/templates/stimulus_basic/counter.html*

```html
{% extends "stimulus_basic/base.html" %}

{% block content %}

<div class="w-full max-w-7xl mx-auto px-4">

  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Counter</h1>

  <button
    class="px-4 py-2 bg-blue-500 hover:bg-blue-700 text-white font-semibold rounded-lg"
    data-controller="counter"
    data-action="click->counter#increment"
  >
    <span data-counter-target="initialDiv">
      Click Me
    </span>
    <span data-counter-target="progressDiv" class="hidden">
      You clicked <span class="font-bold" data-counter-target="count"></span> times!
    </span>
  </button>

  <h1 data-controller="counter" data-action="click->counter#increment" class="text-4xl sm:text-6xl lg:text-7xl mb-6">
    <span data-counter-target="initialDiv">
      Click Me
    </span>
    <span data-counter-target="progressDiv" class="hidden">
      You clicked <span class="font-bold" data-counter-target="count"></span> times!
    </span>
  </h1>

  <div data-controller="counter" data-action="click->counter#increment" class="text-4xl">
    <span data-counter-target="initialDiv">
      Click Me
    </span>
    <span data-counter-target="progressDiv" class="hidden">
      You clicked <span class="font-bold" data-counter-target="count"></span> times!
    </span>
  </div>

</div>

{% endblock %}
```

Notes:

1. We add `initialDiv` and `progressDiv` targets.
1. The `initialDiv` is display and the `progressDiv` is hidden by default.
1. Once user click the counter, we hide the `initialDiv` and display the `progressDiv`

Update *frontend/src/controllers/counter_controller.js*

```js
import {Controller} from '@hotwired/stimulus';

export default class extends Controller {
    static values = {
        count: { type: Number, default: 0 },
    };
    static targets = [
        'count',
        'initialDiv',
        'progressDiv'
    ];

    countValueChanged(value, previousValue) {
        console.log(`${previousValue} changed to ${value}`);
        if (value === 1){
            this.initialDivTarget.classList.add('hidden');
            this.progressDivTarget.classList.remove('hidden');
        }
    }

    increment(){
        this.countValue++;
        this.countTarget.innerText = this.countValue;
    }
}
```

In `countValueChanged` method, if the `value` increase to `1`, we hide the `initialDiv` and show the `progressDiv`

The css class we manipulate here is `hidden`, what if we want it configurable?

## CSS classes

To make CSS class manipulated by our controller is configurable, we set the css value in the HTML and then pass it to the controller.

Update *hotwire_django_app/templates/stimulus_basic/counter.html*

```html
{% extends "stimulus_basic/base.html" %}

{% block content %}

<div class="w-full max-w-7xl mx-auto px-4">

  <h1 class="text-4xl sm:text-6xl lg:text-7xl mb-6">Counter</h1>

  <button
    class="px-4 py-2 bg-blue-500 hover:bg-blue-700 text-white font-semibold rounded-lg"
    data-controller="counter"
    data-action="click->counter#increment"
    data-counter-hidden-class="hidden"
  >
    <span data-counter-target="initialDiv">
      Click Me
    </span>
    <span data-counter-target="progressDiv" class="hidden">
      You clicked <span class="font-bold" data-counter-target="count"></span> times!
    </span>
  </button>

  <h1 data-controller="counter" data-action="click->counter#increment" data-counter-hidden-class="hidden" class="text-4xl sm:text-6xl lg:text-7xl mb-6">
    <span data-counter-target="initialDiv">
      Click Me
    </span>
    <span data-counter-target="progressDiv" class="hidden">
      You clicked <span class="font-bold" data-counter-target="count"></span> times!
    </span>
  </h1>

  <div data-controller="counter" data-action="click->counter#increment" data-counter-hidden-class="hidden" class="text-4xl" >
    <span data-counter-target="initialDiv">
      Click Me
    </span>
    <span data-counter-target="progressDiv" class="hidden">
      You clicked <span class="font-bold" data-counter-target="count"></span> times!
    </span>
  </div>

</div>

{% endblock %}
```

Notes:

1. We add `data-counter-hidden-class="hidden"` to the controller DOM element.

Update *frontend/src/controllers/counter_controller.js*

```js
import {Controller} from '@hotwired/stimulus';

export default class extends Controller {
  static values = {
    count: { type: Number, default: 0 },
  };
  static targets = [
    'count',
    'initialDiv',
    'progressDiv'
  ];
  static classes = ['hidden'];

  countValueChanged(value, previousValue) {
    console.log(`${previousValue} changed to ${value}`);
    if (value === 1){
      this.initialDivTarget.classList.add(this.hiddenClass);
      this.progressDivTarget.classList.remove(this.hiddenClass);
    }
  }

  increment(){
    this.countValue++;
    this.countTarget.innerText = this.countValue;
  }
}
```

Notes:

1. We defined `static classes = ['hidden']`
1. We use `this.initialDivTarget.classList.add(this.hiddenClass)` to add hidden class to the div.
1. This makes our controller code decoupled with the css. Controller only care about the behavior, and we can pass css class name from the HTML.

More details can be found on [https://stimulus.hotwired.dev/reference/css-classes](https://stimulus.hotwired.dev/reference/css-classes)

## Conclusion

Stimulus’s use of data attributes helps separate content from behavior in the same way CSS separates content from presentation

Stimulus helps you build small, reusable controllers, giving you just enough structure to keep your code from devolving into “JavaScript soup.”
