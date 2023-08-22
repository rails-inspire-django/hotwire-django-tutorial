# Bulk Operation

In this guide, I will show you how to `Bulk Operation` with Hotwire.

## How it Works

1. We can build a Stimulus controller to handle the click event of the button for `Bulk Operation`
2. The Stimulus controller will collect the `id` of the selected items, and then send data to the server using native `fetch` or 3-party js lib such as `axios`
3. The server can process request according to the `id`, and return `Turbo Stream` to update the page item details.
4. Once Stimulus controller receive the response, use `Turbo.renderStreamMessage(streamActionHTML)` to process the HTML which contains Turbo Stream code.

To make `Select All` work, you can also use this Stimulus Controller [Stimulus Checkbox Select All](https://www.stimulus-components.com/docs/stimulus-checkbox-select-all/)

## Reference

[Sample Stimulus Controller](https://github.com/gorails-screencasts/bulk-operations-in-rails/blob/223a3d97b520b90d36c8beaa2e25432e1189bc75/app/javascript/controllers/posts_bulk_controller.js)