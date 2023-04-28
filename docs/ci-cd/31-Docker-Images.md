# 3.1 Docker Images and CI/CD

## Introduction

In this exercise, we will be investigating how Docker Compose works and create a CI/CD pipeline with GitHub Actions to automatically build and push images to GitHub Container Registry.

## Prerequisites

1. Installed Docker Desktop
2. Forked the repository and checkout `main` branch to have the completed frontend and backend
3. Successfully ran `docker-compose up` for the frontend and backend from yesterday's workshop

## Exercises

### Understanding docker-compose.yml

In earlier workshops, you were able to run the frontend and backend servers locally using the `docker-compose up` command.

This was made possible with the help of the `docker-compose.yml` file, which consists of the necessary configurations to build images, mount volumes, expose ports, run containers, etc.

```yaml
version: '3.0'

services:
  frontend:
    build: ../frontend
    command: "npm start"
    environment:
      API_ENDPOINT: http://backend:9000/api
    volumes:
      - ../frontend:/home/node/app/
    ports:
      - 3000:3000
  
  backend:
    build: ../backend
    command: "npm run dist"
    volumes:
      - ../backend:/home/node/app/
    ports:
      - 9000:9000
```

Let's breakdown the configurations for the frontend service, which can be applied to the backend as well:

1. `build: ../frontend`: Specifies the build context (ie. path to the Dockerfile). This is like running the `docker build ../frontend` command.
2. `command: "npm start"`: Specifies the command to be executed by the container. Overrides `CMD` in the Dockerfile.
3. `environment:`: Specifies environment variables.
4. `volumes: - ../frontend:/home/node/app/`: Mount a host path. The syntax is `SOURCE:TARGET`. In this case, `../frontend` from the host is mounted to `/home/node/app/` in the container.
5. `ports: - 3000:3000`: Expose ports in the format `HOST:CONTAINER`.

You will need to modify this file in a later exercise. Optionally, you may wish to view the full [Docker Compose configuration reference](https://docs.docker.com/compose/compose-file/compose-file-v3/) for further learning.

### Optimizing Images

There are lots of ways to write a Dockerfile to containerize your application. While the resulting container may be functional, it may not be the most optimized. Here are some considerations when creating a Docker image.

1. Higher storage requirements. You may easily exceed storage quotas in your container registries.
2. Higher bandwidth utilization to download and upload images.
3. Potentially slower image build times.

If we take a look at one of the Dockerfiles (either in `/frontend` or `/backend`), we will notice the first line `FROM node:alpine`. This specifies the parent image to be used to build the image.

In this case, the `node:alpine` image is built from Alpine Linux Project with Node.js installed, which is much smaller than most distribution images. Unlike images like `node:latest`, which is build with a bunch of additional source controls software, libraries and build tools. You may not require all these additional tools in most cases.

You can view other image variants for Node.js [here](https://hub.docker.com/_/node).

### Stopping The Application

Once done, run `docker-compose down` to stop the containers that are running locally.

### Build and Push Images with Gitlab Pipeline

There are drawbacks to the way we build and run our application now:

1. It is difficult to share images with other members in the project team since everything is done locally.
2. We must manually run commands to rebuild images every time any code changes are made.

Fortunately, we can resolve both issues with the help of container registries and automation.

1. Images can be pushed (uploaded) to a container registry, which can be pulled (downloaded) by the rest of your team. Container registries are a central place to store and distribute images. Some examples include Dockerhub and the GitHub Container Registry (GHCR).
2. We can configure CI/CD pipelines to automatically build and push the images to a container registry. This can be achieved using Gitlab Pipeline.

Note that it is a best practice to use a unique tag for each image pushed to a container registry. This is to ensure that images are immutable (ie. will not be changed). For example, we can use the Git commit SHA for the image tag.

### Register Runner Locally (Optional, Skip this if you want to use Gitlab Provided Shared Runners)

1. Start Docker on your computer if it’s not already running
2. Install the gitlab-runner binary on your machine
    - [Runner for Mac](https://docs.gitlab.com/runner/install/osx.html?_gl=1*wo382l*_ga*MzUwODI4Nzk1LjE2NjEwODY1MzA.*_ga_ENFH3X7M5Y*MTY2MjkwMzk2NS4xMi4xLjE2NjI5MDQwMjIuMC4wLjA.#manual-installation-official)
    - [Runner for Windows](https://docs.gitlab.com/runner/install/windows.html?_gl=1*1hhpc8*_ga*MzUwODI4Nzk1LjE2NjEwODY1MzA.*_ga_ENFH3X7M5Y*MTY2MjkwMzk2NS4xMi4xLjE2NjI5MDQwMjIuMC4wLjA.#installation)
3. Register runner with Docker executor to Gitlab workspace
    - Within the group where you have forked your projects
    - On the Group's sidebar, Go to CI/CD -> Runners
    - Select **Register a group runner** and and copy the Registration token
    - On your machine's terminal
      1. Run. **gitlab-runner register**
      2. Register runner with URL: https://gitlab.com
      3. Paste the Registration Token copied earlier
      4. Description: (enter) to accept default 
      5. Tags: Type byod-runner
      6. Executor: Type Docker
      7. Docker Image: alpine:latest
    - Modify the gitlab-runner configuration file 
      1. Located: 
          - ~/.gitlab-runner/config.toml (Mac)
          - C:\Gitlab-Runner\config.toml (Windows)
      2. Find your gitlab runner and replace the volume setting with: [“/var/run/docker.sock:/var/run/docker.sock”, “/cache”]
      3. Run gitlab-runner restart to apply the changes

#### Exercise 1

Create a pipeline using GitHub Pipeline to build, publish and deploy the frontend and backend images to Amazon Web Services.

**Steps:**

1. Create a `.gitlab-ci.yml` file at the root folder of both your frontend and backend app. 
2. Copy and paste the baseline configuration provided to you below and replace the `<INSERT COMMANDS HERE>`. 
3. Commit your newly created pipeline file. Gitlab Pipeline should automatically start running. You can view it at the "CI/CD -> Pipelines" tab in your repository sidebar.
4. Verify that the pipeline completes successfully, where Build, Test and Publish should complete
5. Note that the deploy stage will be set to manual since we have not setup the Container Service yet.

Hint: You would run the same commands used to build a Docker image locally as you would in the CI/CD pipeline. Consider using `docker build` and `docker push`.

Here's the baseline configuration (`.gitlab-ci.yml`) for you to get started:

```yaml
include:
  - project: "<INSERT PATH TO TEMPLATES>/templates" # Location of aws assume-role template 
    ref: "main"
    file:
      - .gitlab-aws-cli.yml

stages:
  - build
  - test
  - publish
  - deploy

# Build stage will build your docker image and stage a copy in Gitlab's Container Registry
build-stage:
  image: docker:stable
  stage: build
  variables:
    # Variable holding the name of Gitlab Container Registry
    CONTAINER_BUILD_IMAGE: $CI_REGISTRY_IMAGE:latest
  tags: # Use tags if you have setup your own local Gitlab Runner, else you can omit this
    - <INSERT TAG HERE>
  before_script:
    # Login to Gitlab Conatiner Registry
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY 
  script:
    # Build and tag your image to the name of $CONTAINER_BUILD_IMAGE
    - docker build -t $CONTAINER_BUILD_IMAGE .
    # Push image to Gitlab Container Registry
    - docker push $CONTAINER_BUILD_IMAGE

# Test stage will be used to run unit testing to make sure your image is ready to be deployed
test-stage:
  image: node:alpine
  stage: test
  tags: # Use tags if you have setup your own local Gitlab Runner, else you can omit this
    - <INSERT TAG HERE>
  before_script:
    - npm install
  script:
    - npm run test:extra

# Publish stage will establish a connection with AWS and publish your staged image to the Elastic Container Registry
publish-stage:
  # Extends defines the job action that it inherited from
  extends: .invoke-awscli-commands-with-assumerole
  # This image is a special image that is build with both Docker and AWS components
  image:
    name: $CI_REGISTRY/<PATH TO TEMPLATES>/templates
    entrypoint:
      - '/usr/bin/env'
  tags: # Use tags if you have setup your own local Gitlab Runner, else you can omit this
    - <INSERT TAG HERE>
  variables:
    # Enter ROLE_ARN of your created role from the previous aws setup
    ROLE_ARN: <INSERT ROLE IDENTITY>
    ECR_REGISTRY: <INSERT ECR REGISTRY NAME>
  stage: publish
  script:
    # Login to Gitlab Conatiner Registry
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    # Retrieve Image from Registry
    - docker pull $CI_REGISTRY_IMAGE:latest
    # Re-Tag pulled image to ECR Registry Name
    - docker tag $CI_REGISTRY_IMAGE:latest $ECR_REGISTRY
    # Login to ECR Registry 
    - aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <INSERT YOUR ECR ENDPOINT>
    # Push to ECR Registry
    - docker push $ECR_REGISTRY

# Deploy stage will run an ecs update command to force a new image deployment
deploy-stage:
  extends: .invoke-awscli-commands-with-assumerole
  stage: deploy
  tags: # Use tags if you have setup your own local Gitlab Runner, else you can omit this
    - <INSERT TAG HERE>
  variables:
    # Enter ROLE_ARN of your created role from the previous aws setup
    ROLE_ARN: <INSERT ROLE IDENTITY>
  script:
    # Command to update ECS Service
    - aws ecs update-service --cluster <INSERT ECS CLUSTER NAME> --service backend-app --force-new-deployment --region ap-southeast-1
  # Enforces manual triggered deployment to prevent accidental runs
  when: manual
```
### Using Built Images

The current docker-compose.yml file builds the image locally and runs it.

We can modify the file to pull the images from Gitlab Registry that you just pushed instead. This is useful when sharing images with other team members and to ensure that everyone is using a consistent image to run their application.

These images can also be used for cloud deployments, which you will get to try in the next few exercises.

In the next exercise, we will begin deploying the frontend and Backend to AWS so that the application is accessible over the web.

---