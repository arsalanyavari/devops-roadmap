# GitLab CI/CD

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/ci-cd-test-deploy-illustration.png">

GitLab CI/CD is a powerful continuous integration and continuous delivery platform integrated into the GitLab repository management platform. It allows you to automate the build, test, and deployment processes of your codebase using configuration files defined within your repository.

<hr>

### Key Concepts:

**Pipeline:** A pipeline is a defined sequence of stages and jobs that automate the processes from code commit to deployment. Pipelines are defined in `.gitlab-ci.yml` files in your repository's root directory.
So first of all create the `.gitlab-ci.yml` file.

**Stage:** A stage represents a specific phase within a pipeline, such as building, testing, and deploying. Each stage is composed of one or more jobs.

**Job:** A job is a single unit of work within a stage. Each job is responsible for executing a specific task, such as running tests, building apps or deploying code.  

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/GitLab-Pipeline.jpg">

**Runner:** A runner is an instance that executes the jobs defined in your pipeline. GitLab provides shared runners as well as the option to set up your own runners for specific needs. Also you can setup you own runners instead of shared runners (look at [setup gitlab runner](https://medium.com/thinkhuman-dev/how-to-set-up-your-own-gitlab-ci-cd-runner-766b36a8e626) if you want)

**Artifact:** Similar to GitHub Actions, artifacts are files generated during the pipeline process that can be stored and accessed after the pipeline completes. They are useful for archiving build outputs or logs.  

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/gitlab-artifact-download.ipg">
<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/gitlab-artifact-download2.ipg" width="100%">

**Trigger:** Triggers are events that initiate a pipeline run. These events can be code pushes, merge requests, tags, or scheduled intervals.

**Variables:** Variables allow you to define dynamic values that can be used in your CI/CD configuration. They can be environment-specific settings or parameters passed to jobs. You can set the variables such as below example in project > settings > CI/CD sextion. Also if you check `protected` it will be secure (such as secrets in github action).

<img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/gitlab-variables.jpg">

### Example .gitlab-ci.yml:

```yaml
# Define a simple pipeline with some usual stages: install, build, test and publish

# In this part you must specify the stages of your pipline.
stages:
  - installation
  - build
  - test
  - publish


# The first stages job is `installation` in this example.
# In the `rule` section we specify when this job must be run and in the other times job will be skipped. `always` keword define running job on every pipeline trigeration.
# Using `cache` gives you the ability to use caching. ‍‍`key` keyword helps yoou to distinguish different pipeline products. `path` is an array of caching targets.
# by using `script` keyword you can run all linux commands (like `run` in github action).
installation_job:
  stage: installation
  rules:
    - when: always
  cache:
    key: $CI_COMMIT_REF_NAME
    policy: push
    paths:
      - ./node_modules
  script:
    - npm ci


# `artifacts` keyword use when you want to store something as an artifacts of pipeline. Unlike `cache` that you must mention you want to use it, the `artifacts` are accessible on every next jobs of this pipeline.
build_job:
  stage: build
  needs:
    - installation
  rules:
    - when: on_success
  cache:
    - key: $CI_COMMIT_REF_NAME
      policy: pull-push
      paths:
        - ./node_modules
  artifacts:
    expire_in: 1 month
    paths:
      - ./dist
  script:
    - npm run build


# With `allow_failure` in this job and next one you can specify the pipeline will be continue if failure happend.
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  rules:
    - when: on_success
  cache:
    key: $CI_COMMIT_REF_NAME
    policy: push
    paths:
      - ./node_modules
  allow_failure: true


# As you see in the below job, you can use another rule using `if` keyword. The gitlab pipeline has some builtin variables that you can use them.
publish_job:
  state: publish
  needs:
    - test
    - build
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event" && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "main"'
  cache:
    key: $CI_COMMIT_REF_NAME
    policy: pull
    paths:
      - ./node_modules
  script:
    - echo "//registry.npmjs.org/:_authToken=$NPM_TOKEN" > $HOME/.npmrc
    - npm publish --access public
  allow_failure:
    exit_codes:
      - 137
      - 255
```

At the last, you can use CI/CD configuration from other files (like inheritance). Look at the below page: https://docs.gitlab.com/ee/ci/yaml/includes.html
