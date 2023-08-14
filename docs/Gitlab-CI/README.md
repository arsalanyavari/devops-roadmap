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

**Variables:** Variables allow you to define dynamic values that can be used in your CI/CD configuration. They can be environment-specific settings or parameters passed to jobs.

### Example .gitlab-ci.yml:

```yaml
# Define a simple pipeline with two stages: build and test

stages:
  - build
  - test

# Jobs in the 'build' stage
build_job:
  stage: build
  script:
    - echo "Building the project..."
    - npm install

# Jobs in the 'test' stage
test_job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
