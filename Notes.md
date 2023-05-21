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

# Django has something that is called Django Test Framework which we will use for testing

### Django Test framework
- Based on the `unittest` library 
- Simulate authentication
- Temporary database (it will automatically create a database for us and then it clears the data once the tests have finished running)
- Django REST Framework adds features
  - API test client (specifically used for testing APIs)


### Where do you put tests?
- Placeholder `tests.py` added to each app (when you create a new app it automatically adds a new `tests.py` module to each app, which is a placeholder where to add tests)
- Or create `tests/` subdirectory to split tests up
- Keep in mind:
  - Only use `tests.py` or `tests/` directory (not both)
  - Test modules must start with `test_` if we create tests inside the `tests/` folder we need to make sure that each module is prefixed with `test_`
  - Test directories must contain `__init__.py`

### Test Database
- Test code that uses the DB
- Specific database for tests
- Each tests that is run in the Django Framework it will have a fresh database to use. This happens for every test (by default)


There are various test classes provided by the Django Test Framework, for example:
- SimpleTestCase
  - No database integration
  - Useful if not database is required for your test
  - Save time executing tests
- TestCase
  - Database integration
  - Useful for testing code that uses the database

### Writing tests
- Import test class
  - SimpleTestCase - No database
  - TestCase - Database

- Import objects to test
- Define test class
- Add test method
- make sure to prefix the methods inside a test class by `test_` if we want them to be picked up by the test runner
- Setup inputs
- Execute code to be tested
- Check output

To run tests in Django:
```
python manage.py test
```

### How to mock tests?
- Use `unittest.mock` library
  - `MagicMock/Mock` - Replace real objects
  - `patch` - Overrride code for tests

### Testing Web Requests

**Testing APIs**
- Django Rest Framework provides us with `APIClient` which is based on the Django's `TestClient`
- Make requests
- Check result
- Override authentication

### Using the APIClient
- Import `APIClient`
- Create client
- Make request
- Check result

### Common Testing Issues
- Tests not running
- Ran less tests than you have

### Possible reasons for tests not running
- Missing `__init__.py` in `tests/` dir
- Indentation of tests cases
- Missing `test` prefix for method
- `ImportError` when running tests, usually the reason for this that we might have both the `tests/` directory and `tests.py` in our application and we need to use either or, not both


# Database

- PostgreSQL
  - Popular open source DB
  - Integrates well with Django
- Docker Compose
  - defined with project (re-usable)
  - Persistent data using volumes
  - Handles network configuration
  - Environment variable configuration


### Network connectivity
- Set `depends_on` on `app` service to start `db` first
- Docker Compose creates a network
- The `app` service can use `db` hostname

### Volumes
- we use volumes to store persistent data using `docker-compose`
- Maps directory in container to local machine

**Note:** In docker-compose we are using a db image named `postgres:13-alpine` from dockerhub

Note: at `docker-compose` see `volumes` the name `dev-db-data` is the name of the volume that we want to give it to the volume

### Steps for configuring database
- Configure Django
  - Tell Django how to connect
- Install database adaptor dependencies
  - Install the tool Django uses to connect
- Update Python requirements (to include Postgres adaptor that Django needs to use in order to connect to Postgres)

### Django needs to know...
- Engine (ty)pe of database)
- Hostname (IP or domain name for database)
- Port
- Database Name
- Username
- Password
- such settings are defined in `settings.py`

### Environment variables
- Pull config valus from environment variables
- Easily passed to Docker
- Used in local dev or prod
- Single place to configure project
- Easy to do with Python

### Psycopg2 - Python package/adaptor needed for Django to connect to Postgres
- the most popular Postgres adaptor for Python
- Supported by Django

### Installation options
- psycopg2-binary
  - OK for development
  - not good, not recommended for production
- psycopg2 (same as the above but without the `-binary` suffix)
  - Compiles from source (it is compiled for the specific operating system that is running on)
  - Required additional dependencies
  - Easy to install with Docker

`psycopg2-binary` is easier for local development because it does not require additional dependencies to be installed (which is the case with `psycopg2`) however `psycopg2-binary` is not good for production usage

we will be using `psycopg2` in this course

### Installing Psycopg2
- list of package dependencies in docs
  - C compiler
  - python3-dev
  - libpq-dev
- Equivalent packages for Alpine
  - postgresql-client
  - build-base
  - postgresql-dev
  - musl-dev
- Found by searching and trial and error
Docker best practice:
  - Clean up build dependencies

