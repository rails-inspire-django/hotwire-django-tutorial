# Confirm

In some cases, we need to ask user to confirm before doing some risky actions.

> `data-turbo-confirm` presents a confirm dialog with the given value. Can be used on form elements or links.

```html
<a href="/articles" data-turbo-confirm="Do you want to leave this page?">Back to articles</a>
<a href="/articles/54" data-turbo-method="delete" data-turbo-confirm="Are you sure you want to delete the article?">Delete the article</a>
```

Or we can use it with `form` element

```html
<form action="/posts" method="post" data-turbo-confirm="Are you sure you want to submit this form?">
  <!-- Submit button -->
  <input type="submit" name="commit" value="Submit">
</form>
```
