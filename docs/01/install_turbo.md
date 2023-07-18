# Setup Turbo

## Objective

1. Install Turbo via the npm command.
1. Import Turbo to our frontend project.

## Turbo

Now `Turbo` contains four parts:

1. `Turbo Drive`: accelerates links and form submissions
1. `Turbo Frames`: decompose pages into independent contexts like HTML `iframe`
1. `Turbo Streams`: deliver page changes over WebSocket, SSE or in response to form submissions using just HTML and a set of CRUD-like actions
1. `Turbo Native`: help build hybrid apps for iOS and Android.

You can check [https://turbo.hotwired.dev/](https://turbo.hotwired.dev/) to learn more.

## Preparation

Let's remove some useless files first

```bash
# those files are created by python-webpack-boilerplate
$ rm -rf frontend/src/components
$ rm -f frontend/src/application/app2.js

# let's rename the entry file we just created
$ mv frontend/src/application/app.js frontend/src/application/turbo_drive.js
$ mv frontend/src/styles/index.scss frontend/src/styles/turbo_drive.scss
```

```bash
frontend/src
├── application
│   └── turbo_drive.js
└── styles
    └── turbo_drive.scss
```

Edit *frontend/src/application/turbo_drive.js*

```javascript
// This is the scss entry file
import "../styles/turbo_drive.scss";                    // update

window.document.addEventListener("DOMContentLoaded", function () {
  window.console.log("dom ready");
});
```

Update *hotwire_django_app/templates/index.html*

```html
{% load webpack_loader static %}

<!DOCTYPE html>
<html>
<head>
  <title>Index</title>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  {% stylesheet_pack 'turbo_drive' %}
</head>
<body>

<div class="jumbotron py-5">
  <div class="w-full max-w-7xl mx-auto px-4">
    <h1 class="text-4xl mb-4">Hello, world!</h1>
    <p class="mb-4">This is a template for a simple marketing or informational website. It includes a large callout called a
      jumbotron and three supporting pieces of content. Use it as a starting point to create something more unique.</p>

    <p><a class="btn-blue mb-4" href="#" role="button">Learn more »</a></p>

    <div class="flex justify-center">
      <img src="{% static 'vendors/images/webpack.png' %}" />
    </div>
  </div>
</div>

{% javascript_pack 'turbo_drive' %}

</body>
</html>
```

Notes:

1. We changed the JS file and CSS file from `app` to `turbo_drive`.
1. We use `turbo_drive.js` here because we will create multiple entrypoint files in later chapters.

Now test again in Django dev server, and everything should still work.

## Setup Turbo

Next, we will install `Turbo` via the npm command.

```bash
$ npm install --save-exact @hotwired/turbo@7.3.0
```

Here we install `hotwired/turbo` `7.3.0`, we use `--save-exact` to pin the version here to make sure the bugs are easy to troubleshoot in some cases.

The package installed via `npm` command are located in *node_modules* folder.

If we check `package.json`

```
"dependencies": {
  "@hotwired/turbo": "7.3.0",
}
```

Please note no `^` before the package version because we have pinned the version.

Next, update *frontend/src/application/turbo_drive.js* to import `@hotwired/turbo`

```js
// This is the scss entry file
import "../styles/turbo_drive.scss";

import "@hotwired/turbo";

window.document.addEventListener("DOMContentLoaded", function () {
  window.console.log("dom ready");
});

document.addEventListener('turbo:load', function () {     // new
  console.log('turbo:load');
});
```

Notes:

1. We `import "@hotwired/turbo";` in our js application file.
1. And we add an event listener to the `turbo:load` event, which can let us check if Turbo is installed successfully.

## Template

Let's update *hotwire_django_app/templates/index.html*

```html
{% load webpack_loader static %}

<!DOCTYPE html>
<html>
<head>
  <title>Index</title>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  {% stylesheet_pack 'turbo_drive' %}
  {% javascript_pack 'turbo_drive' attrs='defer' %}       <! --- new --->
</head>
<body>

<div class="jumbotron py-5">
  <div class="w-full max-w-7xl mx-auto px-4">
    <h1 class="text-4xl mb-4">Hello, world!</h1>
    <p class="mb-4">This is a template for a simple marketing or informational website. It includes a large callout called a
      jumbotron and three supporting pieces of content. Use it as a starting point to create something more unique.</p>

    <p><a class="btn-blue mb-4" href="#" role="button">Learn more »</a></p>

    <div class="flex justify-center">
      <img src="{% static 'vendors/images/webpack.png' %}" />
    </div>
  </div>
</div>

</body>
</html>
```

Notes:

1. **Please make sure to move the JS from the bottom to the `head`, and set `defer` attribute**, I will talk about this later.

Let's run our app, and check on [http://127.0.0.1:8000/](http://127.0.0.1:8000/)

In the HTML source code, we can see

```html
<script type="text/javascript" src="http://localhost:9091/js/turbo_drive.js" defer></script>
```

In the `console` of the web devtool, we can see:

```
dom ready
turbo:load
```

That means the Turbo is working in our project.

## Notes:

1. To import Turbo, we do not add the JS link to the HTML template directly (even we can do this), instead, we use `import "@hotwired/turbo";` in our JS application file.
2. And then, we use `{% javascript_pack 'turbo_drive' attrs='defer' %}` to add JS entry file to the HTML template.
3. If we need to add another NPM package in the future, we can just `npm install` the package and then import it in the JS application file.
4. Webpack will handle the dependency for us, and we do not need to add the JS link to the HTML template manually.

## Reference

[Installing Turbo in Your Application](https://turbo.hotwired.dev/handbook/installing)
