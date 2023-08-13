# Github Action

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/githubaction.webp" >

Github Action allows you to define custom workflows using YAML files that specify the steps, triggers, and conditions for your CI/CD processes. Indeed it is popular because its integrated with GitHub repositories.

<hr>

### Key Concepts:

**Workflow:** A workflow is a defined automated process that consists of one or more jobs. Workflows are defined in YAML files within a .github/workflows directory in your repository (for example .github/workflows/ci.yml).
>__Note__
Pay attention that a repository may has more than one workflow file.

**Job:** A job is a single unit of work within a workflow. Each job can include multiple steps that are executed sequentially on the same runner.
>__Note__
Also each workflow may has more than one job.

**Runner:** A runner is a machine or virtual environment that runs the jobs within your workflows. GitHub provides hosted runners as well as self-hosted runners that you can configure.
For more information about self hosted github runners you can read this documents: https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners

**Step:** A step is an individual task within a job. Steps are the basic building blocks of a workflow and can be commands, scripts, or actions.

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/githubaction-ci-cd.png" >

**Action:** An action is a standalone reusable unit of code that performs a specific task. Actions can be written by you or other contributors and can be used in workflows to streamline processes (its very typical to use the others actions from [Actions Marketplace](https://github.com/marketplace?type=actions)).

In the below action we will se how to use the others actions; for example the CheckOut action is one of the most useful action.
```yaml
name: Workflow

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v2  # Using the "Checkout" action
        with:
          ref: ${{ github.ref }}
``` 
Exactly the "uses" keyword helps us to use the others action.
>__Warning__
do not panic; I will each part of action file to you; so if you dont have any idea about the other keywords, ignore them please :)

**Event:** An event is a specific activity that triggers a workflow. Events include things like pushes to a repository, pull request creation or updates, issue comments, and more.

#### List of Events
- push: Triggered when code is pushed to any branch or tag in the repository.  
- pull_request: Triggered when a pull request is opened, synchronized, or closed.  
- release: Triggered when a new release is created in the repository.  
- create: Triggered when a branch, tag, or repository is created.  
- delete: Triggered when a branch, tag, or repository is deleted.  
- workflow_run: Triggered when a workflow is run or completed.  
- schedule: Triggered by a scheduled time.  
- repository_dispatch: Triggered by a custom event sent to a repository.  
- workflow_dispatch: Manually triggered by a user using the "Run workflow" button on GitHub.  
- push (with "tags" key): Triggered when a new tag is pushed to the repository.  
- pull_request_review: Triggered when a pull request review is submitted.  
- pull_request_target: Similar to pull_request, but works for pull requests from forked repositories.  
- issue_comment: Triggered when a comment is created on an issue or pull request.  
- page_build: Triggered when a GitHub Pages build is requested.  
- status: Triggered when a status check's state changes.

For more information about the events you can read the https://docs.github.com/en/actions/reference/events-that-trigger-workflows document.

Each event can have its own specific payload data that you can use within your workflows to make decisions or customize actions. When defining your workflow, you specify which events you want to trigger it using the on keyword. For example:

```yaml
on:
  push:
    branches:
      - main
  pull_request:
    types:
      - opened
      - synchronize
```

>__Note__
Pay attention that the events are "or" by each other and not "and". It means the work flow in the above example will be trigger on each push to main, open or push to the opened pull request.

**Environment Variables:** You can set environment variables in your workflows to store data that can be accessed by different steps or actions within the same job.

**Secrets:** Secrets are encrypted environment variables that you can use to store sensitive information, such as API tokens or passwords, without exposing them in your workflow files. You must set them in Repository settings, Security section, Secrets and variables part that the page is something like below image.

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/githubactionsecret.png" >

**Artifacts:** Artifacts are files or data that are produced by a workflow and can be stored and accessed after the workflow completes. They are useful for archiving build outputs or logs.

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/gitHubActionsWorkflowResultsArtifacts.png" >

**Matrix Jobs:** GitHub Actions supports matrix jobs, which allow you to run the same job with different inputs. This is useful for testing across different environments or configurations. Lets se the below example:
```yaml
name: Matrix Build Example

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macOS-latest, windows-latest]
        python-version: [3.8, 3.9, 3.10]

    steps:
      - name: Checkout Code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install Dependencies
        run: pip install -r requirements.txt

      - name: Run Tests
        run: pytest
```
In this example the workflow will be run for 3 different types of OS then each OS run with 3 different version of Python.

**Workflow Status and Logs:** Each workflow run has a status, indicating whether it succeeded, failed, or was canceled. You can also access logs for each step to troubleshoot issues.

<img width="100%" src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/githubaction-status.png" >

**Workflow Permissions:** You can control which branches or pull requests trigger your workflows, as well as who can edit or manage them using GitHub's permission settings.

Ok enough. Lets see the below example:
```yaml
# This line is necessary and specify the workflow name 
name: WORKFLOW NAME


# In this part you specify when you action must be triggerd
# In this below example on each pushing to main branch, open a pull request to any branch or pushing to the opened pull request. 
on:
  push:
    branches:
      - main
  pull_request:
    types:
      - opened
      - synchronize


# The below snippet of code specify the build job. As you see the build job will be run on different operating systems. Also we set environment variable (it can be more)
jobs:
  build:
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        os: [ubuntu-latest, macOS-latest, windows-latest]
        node-version: [12.x, 14.x, 16.x, 18.x]
        versions:
          - latest
          - ${{ github.ref_name }}

    env:
      ENV_VAR: "Hello, GitHub Actions!"


# The below part is related to different steps of build job
# In the first step we use an action named actions/checkout (version 3). You can see all the actions in <github market place>
# Each action could get some additional values by `with` keyword. This is different for action so you must read each action document that will you use.
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3
        with:
          fetch-depth: 0


# The below step will setup node based on all node version in the top part of the action (strategy part).
# There is an equivalent of this action for other languages, you should check GitHub Market place.
      - name: Setup node
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}


# You can use the below snippet to get knowing about the github action builtin variables and using them inside you action. U suggest you try it :D
# The `run` instructioon can run all the linux commands that is possible (you can also install package using it)
      - name: Print actions github builtin variables
        env:
            GITHUB_CONTEXT: ${{ toJSON(github.event) }}
        run: echo "$GITHUB_CONTEXT"

# Below action is use too download a repository assets...
      - name: Downloading assets
        uses: dsaltares/fetch-gh-release-asset@master
        with:
          repo: 'dsaltares/godot-wild-jam-18'
          version: 'tags/v0.1.18'
          regex: true
          file: "plague-.*\\.zip"
          target: 'subdir/'
          token: ${{ secrets.GITHUB_TOKEN }}
# Also you can use the equivalent it using curl command
# Using `run` in multiline format
      - name: Downloading assets V2
        run: |
          curl -s https://api.github.com/repos/dsaltares/godot-wild-jam-18/releases/latest | grep "browser_download_url" | cut -d '"' -f 4 | wget -qi-
          curl -s https://api.github.com/repos/dsaltares/godot-wild-jam-18/releases/latest | grep "zipball_url" | cut -d '"' -f 4 | wget -qi-


# Do some jobs based on a condition (use `if`)
      - name: install dependencies (ubuntu only)
        if: matrix.platform == 'ubuntu-20.04'
        run: |
          sudo apt-get update && sudo apt-get install -y libgtk-3-dev libwebkit2gtk-4.0-dev libappindicator3-dev librsvg2-dev


# Install or something else you need
      - name: Building project
        run: |
          npm ci
          npm run test
          npm run release
          mkdir output/
          mv ./out.bin ./output/


# The belos snippet use to cache something (Its usual to cache installing packages to do not repeat this step in all jobs of an action)
      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: |
            node_modules
            packages/*/node_modules
          key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}


# Also these one store somethig you want (like dist folder) accessible how much you want (the saved items also reachable on other jobs of action)
      - name: Dist Packages Output artifacts
        uses: actions/upload-artifact@v3
        with:
          name: artifact
          path: ./*/dist
          retention-days: 30


# Below action is used to upload a file to assets (Pay attention this type will use when a release created or published)
      - name: Upload File to Assets
        uses: softprops/action-gh-release@v1
        with:
          files: ./output/out.bin
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}


# Below steps use for login to Docker Registry and Publish an image
# I login to Github Registry but you can use this Action to login to Dockerhub also 
      - name: Login to Docker Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: arsalanyavari
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ghcr.io/arsalanyavari/new_image:${{ matrix.versions }}
```

Ok enough i think. There are many action in [Actions Marketplace](https://github.com/marketplace?type=actions) that you can use them also you can write your own action.  
Try to be best one and have fun.
