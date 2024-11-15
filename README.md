# GitHub Actions - The Complete Guide

Following the above course on Udemy, and recording my learning here

## First Workflow
The workflow will consist of a .yml file, which has some keywords, like *name*, *on* (specifying the trigger event), as well as *jobs* (specifying the work that should be done).

```yml
name: First Workflow
on: workflow_dispatch
jobs:
  first-job:
    runs-on: ubuntu-latest
    steps:
      - name: Print greeting
        run: echo "Hello World!"
      - name: Print goodbye
        run: echo "Bye bye ;)"
```

The *on: workflow_dispatch* means that the workflow will be executed manually by the user, and not on some other events.

The jobs consist of a list of jobs, in which we give each job an identifier of our choice (ex: first-job).

Then, we will use the *runs-on* keyword to specify what type of machine will be used.
\\
Then we will specify the steps.

**Result:**
![Result of running first workflow](img/first-workflow.png)

## Second Workflow (Running tests for React app)

We have simple unit tests for this React application, in which in a real scenario, we would want to run this using a workflow.

