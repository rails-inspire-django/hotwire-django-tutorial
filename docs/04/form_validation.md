# Form Validation

In this guide, I will show you how to do field validation and form validation with Hotwire.

## Field Level

We can write a Stimulus controller to do field level validation.

### Remote Check

If you want to do remote check using Ajax (check availability of username, email, etc), you can use Stimulus controller to send input value to server, and then update the `innerHtml` of the field

```js
import { Controller } from "stimulus";

export default class extends Controller {

  async onBlur(event) {
    const response = await fetch('/username/check/', {
      method: 'POST',
      body: this.element.value
    });

    if (!response.ok) {
      throw new Error('Request failed');
    }

    const data = await response.text();
    this.element.innerHTML = data;
  }
}
```

Notes:

1. The server would check the input value, and return the result as HTML
2. Or server can return JSON response, and you can use `JSON.parse` to parse the response, and then update the field accordingly.
3. You can even create a custom form widget to make the field validation more reusable in your whole project. (for example, make the entrypoint configurable)

### HTML attributes

We can add below attributes to the input element to do field level validation.

1. required: Specifies whether a form field needs to be filled in before the form can be submitted.
1. minlength and maxlength: Specifies the minimum and maximum length of textual data (strings).
1. min and max: Specifies the minimum and maximum values of numerical input types.
1. type: Specifies whether the data needs to be a number, an email address, or some other specific preset type.
1. pattern: Specifies a regular expression that defines a pattern the entered data needs to follow.

> The HTMLInputElement.checkValidity() method returns a boolean value which indicates validity of the value of the element. If the value is invalid, this method also fires the invalid event on the element.

[Using built-in form validation](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation#using_built-in_form_validation)

Below is an example, the Stimulus controller use `checkValidity` to check the `input` field.

```js
// https://github.com/jorgemanrubia/rails-form-validations-example/blob/master/app/javascript/controllers/form_controller.js 

import { Controller } from 'stimulus'

export default class extends Controller {
  
  onBlur = (event) => {
    this.validateField(event.target)
  }

  validateField(field) {
    if (!this.shouldValidateField(field))
      return true
    const isValid = field.checkValidity()
    field.classList.toggle('invalid', !isValid)
    this.refreshErrorForInvalidField(field, isValid)
    return isValid
  }

  shouldValidateField(field) {
    return !field.disabled && !['file', 'reset', 'submit', 'button'].includes(field.type)
  }

  refreshErrorForInvalidField(field, isValid) {
    this.removeExistingErrorMessage(field)
    if (!isValid)
      this.showErrorForInvalidField(field)
  }

  removeExistingErrorMessage(field) {
    const fieldContainer = field.closest('.field')
    if (!fieldContainer)
      return;
    const existingErrorMessageElement = fieldContainer.querySelector('.error')
    if (existingErrorMessageElement)
      existingErrorMessageElement.parentNode.removeChild(existingErrorMessageElement)
  }

  showErrorForInvalidField(field) {
    field.insertAdjacentHTML('afterend', this.buildFieldErrorHtml(field))
  }

  buildFieldErrorHtml(field) {
    return `<p class="error">${field.validationMessage}</p>`
  }
  
}
```

## Form Level

### Server Side with Turbo Frame

We can use `turbo-frame` to wrap the form, if the form validation fail on the server side, then the form would be updated on the frontend side without full page reload

You can test on this [Demo](https://hotwire-django.fly.dev/turbo-frame/)

If the title is too short, then you will see the error message.

## Reference

[Real-time Form Validation in Ruby on Rails](https://stevepolito.design/blog/rails-real-time-form-validation)
