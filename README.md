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

```yml
name: Test Project
on: push
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install NodeJS
        uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Install dependecies
        run: npm ci
      - name: Run tests
        run: npm test
```

For the above *on* keyword, we have various triggers such as repository-related (push, pull_request, and so on), or dispatch events.
\\
We are then getting the code from the repo, installing NodeJS, installing dependencies, and then executing the `npm test` command.

We can have **multiple jobs** in the same workflow, as shown in *deployment.yml*.

By default, jobs run in parallel, but if we want them to run sequentially, we have to add the **needs** keyword.

We can see the sequential execution visually:

![Sequential workflow](img/sequential.png)

## Skipping Workflows
This can be done by adding specific keywords to your commit message.

For example, a commit message can look like this: `added comments [skip ci]`.

This commit message will cause no new workflows to be started.

## Job Artifacts
Job artifacts are output assets (ex: app binary, website files, etc.) which are produced by the job.
These assets can be downloaded and used manually using GitHub UI or REST API, or be downloaded and used in other jobs **using Actions**.

For example, in our practice-react-app, when we run `npm run build`, we get files in the `dist` folder.

We can use these files that are generated here further to do other tasks, such as upload them on a server where we are hosting the website.

We do that by adding an **Upload Artifacts** step like so:

```yml
- name: Upload artifacts
  uses: actions/upload-artifact@v3
  with:
    name: dist-files
    path: ./practice-react-app/dist
```

The same way we are uploading, we can also download artifacts

```yml
- name: Get build artifacts
  uses: actions/download-artifact@v3
  with:
    name: dist-files
- name: Output contents
  run: ls
```

We want to **cache dependencies** since we do not want to download them for every job.

If dependencies are cached in one job, they can be used in other jobs and workflows.
But we need to specify the same piece of code in all our jobs.

```yml
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: deps-node-modules-${{hashFiles('**/package-lock.json')}}
```

It is taking much less time now to download the dependencies in other jobs, since it is using the cached dependencies from the first-ran job.

Since we are using the package lock json file as part of the cache key, if we use `npm update` to update our dependencies, it will no longer use those cached dependencies since they are now outdated.

## Environment Variables

We want to provide environment variables inside our workflow itself, so as to simulate different variables based on the environment (testing or production).

The way we do that is by adding an `env` keyword in the workflow file, where we can add it on a whole workflow level (shared between all jobs), or we can add it on a job level as well.

Ideally, we do not want to hard-code our values inside the workflow files, and there is a better way (much like Hashicorp Vault).