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

### Problem with Docker compose
- using `depends_one` ensures `service` starts
  - it doesn't ensure though that the application is running, it means database has started but may not be fully ready for the Django application to interact with it

### Solution
  - the solution for this is to make Django `wait for db` by creating a custom solution for this
    - check for database availability
    - continue when database is ready
  - create custom Django management command

### When is this an issue?
  - Running `docker-compose` locally
  - Running on deployed environment

**Note: to create a Django app, in this case named "core"**
```
docker-compose run --rm app sh -c "python manage.py startapp core"
```

So basically the above command adds the template app to our project

then next thing that we need to do is:
- delete `tests.py` file inside the `core` app since we will not be using it 
- also delete the `views.py` apps since we will not be using it either.
- Then create a `tests` folder inside the `core` app folder
- then create a file named `__init__.py` inside the `core/tests` folder
- then go to `app/app/settings.py` and search for `INSTALLED_APPS` and make sure to add the core app to it at the end, so just basically adding a string named `core`

### Creating a basic command to use it to wait for the database to be up and running

- create a folder named `management` inside the `core` app
- inside the folder `core/management` add an `__init__.py` file also add another folder named `commands`
- inside the folder `/core/management/commands` add another file named `__init__.py` 
- also inside `/core/management/commands` add a file named `wait_for_db.py`  , so beacause of this folder structure we can then run this command later on using `manage.py` 

then inside the `wait_for_db.py` command add the following:
````
"""
Django command to wait for the database to be available
"""
from django.core.management.base import BaseCommand


class Command(BaseCommand):
    """Django command to wait for database."""

    def handle(self, *args, **options):
        pass

```

this is the minimum code that we need foradding a Django management command

**Important Note:***

```
@patch('core.management.commands.wait_for_db.Command.check')
class CommandTests(SimpleTestCase):
    """Test commands."""

    def test_wait_for_db_ready(self, patched_check):
        """Test waiting for database if database read"""
        patched_check.return_value = True  # checks if database is ready

        call_command('wait_for_db')

        patched_check.assert_called_once_with(database=['default'])

    @patch('time.sleep')
    def test_wait_for_db_delay(self, patched_sleep, patched_check):
        """Test waiting for database when getting OperationalError"""
        patched_check.side_effect = [Psycopg2Error] * 2 + \
            [OperationalError] * 3 + [True]

        call_command('wait_for_db')

        self.assertEqual(patched_check.call_count, 6)
        patched_check.assert_called_with(database=['default'])

```

notice the:
```
    @patch('time.sleep')
    def test_wait_for_db_delay(self, patched_sleep, patched_check):
```

`patched_sleep` in this case is the first argument after `self` and it takes from `@patch('time.sleep')` and `patched_check` argument is the second argument after `self` and it takes after `@patch('core.management.commands.wait_for_db.Command.check')` meaning the patch operations work on an inside-out order

To run the custom created `wait_for_db` command:

```
docker-compose run --rm  app sh -c "python manage.py wait_for_db"
```

To ignore warning/errors due to flake8 linting, we can add the following command inside a python file:
```
# noqa 
```

### Models
- Each model maps to a table
- Models contain
  - Name
  - Fields
  - Other metadata
  - Custom Python logic

### Creating migrations
- Ensure app is enabled in settings.py
- Use Django CLI
  - `python manage.py makemigrations` (command that is used to create migrations)

### Applying migrations
- Use Django CLI
  - `python manage.py migrate` (command that is used to apply migrations)
  - run the command anytime we start the application after waiting for the database (run `wait_for_db` command first and then run the migrations command)

Note: if we run into an issue with the following message:

```
PostgreSQL Database directory appears to contain a database; Skipping initialization
```

we need to run:
```
docker-compose down --volumes
```

left it at: Update docker compose and CI/CD at 2:45 

### Building using docker-compose
```
docker-compose up -d --build
```

### To find out what services are running on a specific port:
```
sudo lsof -i :5432
```

To stop a specific service:
```
sudo kill <PID>
```

app_1  |        Is the server running on host "db" (172.18.0.2) and accepting
app_1  |        TCP/IP connections on port 5432?

Note: Restart Docker service
```
sudo systemctl restart docker.service
```