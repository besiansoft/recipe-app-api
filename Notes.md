Course: https://www.udemy.com/course/django-python-advanced/learn/lecture/32238716#overview

We will learn about:
- App Design
- Intro to TDD
- System Setup
- Project Setup
- Configure Github actions
- TDD with Django
- Configure Database
- Create user Model
- Setup Django Admin
- API Documentation
- Build User API
- Build Recipe API
- Build Tags API
- Build Ingredients API
- Recipe Image API
- Implement Filtering
- Deploy to AWS


Run all commands through Docker Compose

example:
```
docker-compose run --rm app sh -c "python manage.py collectstatic"
```

- `docker-compose` runs a Docker Compose command
- `run` will start a specific container defined in config
- `--rm` removes the container
- `app` is the name of the service
- `sh -c` passes in a shell command
- Command to run inside container

path to folder linked to the remote repo: `~/Documents/python/recipe-app-ap `

anything specified in the `.dockerignore` it will be ignored by Docker to minimize issues when building the container


To create a new Django project, run the following command:
```
docker-compose run --rm app sh -c "django-admin startproject app ."
```

Flake 8 package is used for linting

to run:
```
docker-compose run --rm app sh -c "flake8"
```

For unit testing we are going to use Django test suite

We will run tests through Docker Compose

```
docker-compose run --rm app sh -c "python manage.py test"
```

It is useful to create a development only `requirements.dev.txt` to separate development dependencies from production dependencies so that we do not introduce unnecessary packages into the image that we are deploying to production server

Important Note: We are using `docker-compose.yml` only for local development

To run Flake 8 we need to run the following command:
```
docker-compose run --rm app sh -c "flake8"
```

To create a Django app, run:
```
docker-compose run --rm app sh -c "django-admin startproject app ."
```

### Github Actions

Common uses are:
- Deployment (not covering deployments on this course, there is a separate course for that: https://www.udemy.com/course/devops-deployment-automation-terraform-aws-docker/?kw=devops+deployment&src=sac&subs_filter_type=subs_only) that is covered in course: 
- Code linting
- Unit tests

**Pricing of Github actions**
- charged per minutes
- 2000 free minutes
- various plans available

**Configuring Github Actions**
- create a config file at: `.github/workflows/checks.yml`
  - Set trigger
  - Add steps for running testing and linting
- Configure Docker Hub auth

**Dcoker Hub**
- hosted service that allows to pull base images onto our machine
- rate limits
  - Anynomous users: 100 different images per 6 hours time frame
  - Authenticated users: 200/6h
- Github Actions uses shared IP addresses
  - Limit applied to all users
- Authenticate with Docker Hub
  - 200 pulls per 6h all to ourselves
  - more than enough for most projects
- Additional plans are available

**Register on hub.docker.com**
- Register account on hub.docker.com
- Use `docker login` during our job
- Add secrets to GitHub project
- Add actions secrets
- Secrets are encrypted
- Decrypted when needed in actions

It does not matter how we call the file inside the `.github/workflows` directory, but on this case we are calling it `checks.yml`

In the `checks.yml` file add `---` at the top of the file because it means that we are adding a yaml file

**Note:**
`uses: docker/login-action@v1` means that we are going to use a pre-made action that is provided in the Github actions repository. Github actions works in the way that we can either create our own actions or we can use premade Github actions. An action is a docker configuration or docker container that is set up to perform a certain task

**Note:**
`uses: actions/checkout@v2` is a premade actions that checks out our code


**Note:**
command that runs our unit tests inside the project:
`run: docker-compose run --rm app sh -c "python manage.py test"`

**Note:**
`docker-compose` is already installed in `ubuntu-20.04` there is no need to install `docker-compose` there

left it at: Configure Docker Hub Credentials

**Note:**
A Github workflow can be created locally and then once the code is pushed we should see the workflon under the `Actions` tab, the name of the workflow in Github is the same as the name of the workflow file in the codebase

TODO: compare why the Django app has been created inside the `.app` folder and not on the `app` folder like the codebase shows

left it at Test Github Actions

