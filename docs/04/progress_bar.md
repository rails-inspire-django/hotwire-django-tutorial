# Progress Bar

## Background

In some cases, we need to launch a long-running task in the background, and we want to show the progress of the task to the user.

## How it works

1. After user click the button, we can launch the job in the background, and return a `job_id` to the frontend.
2. In the frontend, we can use Stimulus to send request to the server to get the progress of the job at X seconds interval.
3. The server can return Turbo Stream response to update the progress bar.
4. Or we can push the Turbo Stream response to the frontend via WebSocket to update the progress bar.

## Demo

You can launch a Celery task and see how the progress bar works.

[https://saashammer.com/demo-celery/subscribe/](https://saashammer.com/demo-celery/subscribe/)
