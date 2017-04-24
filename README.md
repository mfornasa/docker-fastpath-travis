# Docker FastPath - Travis CI

This repository shows how to implement [Docker FastPath](https://github.com/mfornasa/docker-fastpath) using [Travis CI](https://travis-ci.com). Travis CI is free for open source projects and you can sign up for a free trial for private projects.

Refer to this [blog post](https://medium.com/@mfornasa/docker-fastpath-only-build-your-images-once-a4c3beeae7d3) for a general introduction to Docker FastPath.

The repository contains a mokcup Node.js application, but the approach is applicable to any application based on Docker and built on Travis CI. The procedure assumes that you have an account on Docker Hub (they offer a free 1-image plan), but you can use your own private Docker registry (standalone, AWS ECR, etc.). I you prefer to use Jenkins, see the [Jenkins version](https://github.com/mfornasa/docker-fastpath-jenkins).

## Prerequisites
* A GitHub account
* A Travis CI account
* The `travis` [command line tool](https://github.com/travis-ci/travis.rb#installation)
* `git` command line
* A Docker Hub account (or an account on a private Docker registry)

## Installation
* [Create](https://hub.docker.com/add/repository/) an image repository on Docker Hub (or on your private registry). Make sure that you create a private repository if your image is private.
* [Create](https://help.github.com/articles/create-a-repo/) a code repository on GitHub. Make sure that you create a private repository if your code is private.
* Clone this repository on your local machine: `git clone https://github.com/mfornasa/docker-fastpath-travis.git`
* Move to the repository directory: `cd docker-fastpath-travis`
* Edit `.travis.yml`, set `IMAGE_REPO` to the name of the image repository created in Docker Hub.
* Encrypt your Docker Hub credentials:
  * `travis encrypt DOCKER_USERNAME=your_username --add`
  * `travis encrypt DOCKER_PASSWORD=your_password --add`
* Commit your changes: `git add .travis.yml; git commit -m "Configure FastPath"`
* Configure your code repository: `git remote set-url origin https://github.com/you-repo-url.git`
* [Activate](https://travis-ci.org/getting_started) your code repository in Travis CI (you may need to force a GitHub sync to see your new repository in the list).
* Push your changes: `git push origin master`

## Usage
After the Installation, take a look at Travis CI logs. You will see the image build process:
```
New code. Performing a Docker build
Sending build context to Docker daemon 6.216 MB

Step 1 : FROM node:6.10.1-alpine
6.10.1-alpine: Pulling from library/node
Status: Downloaded newer image for node:6.10.1-alpine
...
```
your (placeholder) tests are happening:
```
Do some tests...
```
the image is pushed to the image repository:
```
Pushing xxx/test:447ce1915a9c1dd55ed23e57270fb90765fb06e2 to the registry
```
and your (placeholder) deploy is happening:
```
Deploy xxx/test:447ce1915a9c1dd55ed23e57270fb90765fb06e2 (placeholder)
```

To make sure that FastPath is working correctly, let's simulate a merge to a deploy branch.

```
git checkout -b deploy
git push origin deploy
```

In this case, logs are gonna be rather different. FastPath notices that you already have an image for this codebase:

```
Found a suitable image xxx/test:8385be7eba53d8278b7307e5ac058b6535e80bab
```

and the image is deployed:
```
Deploy xxx/test:8385be7eba53d8278b7307e5ac058b6535e80bab (placeholder)
```

Super-fast, and no risks of introducing untested errors in you deploy.

This is a simplified example: FastPath is going to detect every case in which the pushed codebase is identical to a codebase alredy built in the past. See this [blog post](https://medium.com/@mfornasa/docker-fastpath-only-build-your-images-once-a4c3beeae7d3) for some examples.

