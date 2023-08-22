# Cascading Selects

## Case 1

**If you do not need to get data from the server**, you can get the job done with Stimulus controller

You can check this great tutorial [Dynamic forms with Stimulus](https://thoughtbot.com/blog/dynamic-forms-with-stimulus), you can also get the source code on GitHub to dive deeper.

The key point is:

First, our controller loops through the form’s `fieldset` elements, marking any element with a [name] attribute that matches the `<input type="radio"></input>` element’s [name] as disabled. We’re relying on the field_name view helper to consistently generate matching [name] attributes.

Then, the controller reads the changed `<input type="radio"></input>` element’s name and aria-controls attributes. If it finds a `fieldset` element whose [id] attribute is referenced by the <input type="radio"></input> element’s [aria-controls] attribute, the controller removes the [disabled] attribute. We’re relying on the field_id view helper to consistently generate matching [id] and [aria-controls] attributes.

## Case 2

If you want the **Select option values changed based on the previous Select value**, we can do in this way.

Let's assume we want to auto update `State` select based on the `Country` select.

1. We wrap the `State` select in a specific `turbo frame` element.
2. We can create a Stimulus controller to handle the `change` event of the `Country` select
3. If country select value changed, we build search params and send GET request to the server
4. The server filter the `State` options based on the `Country` value, and return the form back.
5. Turbo frame will replace the `State` select with the new one

You can check [Dynamic forms with Turbo](https://thoughtbot.com/blog/dynamic-forms-with-turbo) to see how it works.
