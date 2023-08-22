# File Upload With Progress

## How it works

The `XMLHttpRequest` object can be used to upload files asynchronously to a server.

To achieve file upload with progress tracking using Stimulus, you can follow these steps:

1. Set up your HTML template with Stimulus attributes and data-controller:

```html
<form data-controller="upload-form">
  {% csrf_token %}
  <input type="file" data-target="upload-form.fileInput">
  <progress data-target="upload-form.progressBar" value="0" max="100"></progress>
  <button type="submit" data-action="upload-form#upload">Upload</button>
</form>
```

2. Define a Stimulus controller called "upload-form" to handle the file upload:

```javascript
import { Controller } from "@hotwired/stimulus";
import { getCookie } from "../helpers";

export default class extends Controller {
  static targets = ['fileInput', 'progressBar'];

  upload(event) {
    event.preventDefault();
    const file = this.fileInputTarget.files[0];
    if (file) {
      this.uploadFile(file);
    }
  }

  uploadFile(file) {
    const xhr = new XMLHttpRequest();

    xhr.upload.addEventListener('progress', (e) => {
      if (e.lengthComputable) {
        const percentComplete = (e.loaded / e.total) * 100;
        this.progressBarTarget.value = percentComplete;
      }
    });

    xhr.addEventListener('load', () => {
      this.progressBarTarget.value = 100;
      console.log('Upload completed');
    });

    const url = '/users/profile_avatar_upload/';
    xhr.open('POST', url, true);
    xhr.setRequestHeader('X-CSRFToken', getCookie("csrftoken"));
    xhr.send(file);
  }
}
```

The `upload` method is triggered by the form's submit event and prevents the default form submission. It retrieves the selected file from the file input target and calls the `uploadFile` method to initiate the upload.

The `uploadFile` method sets up the XHR object, attaches event listeners for progress tracking and upload completion, and sends the file data. The progress value is updated using the `progressBarTarget` element.

You can also use 3-party lib such as `Dropzone` to build file upload feature. [https://github.com/kanety/stimulus-dropzone](https://github.com/kanety/stimulus-dropzone)
