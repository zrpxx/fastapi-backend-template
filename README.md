The backend API without `Docker` can be found in `http://localhost:8000/docs`.

## Why the above Tech-Stack?

Well, the easy answer is **Asynchronousity** and **Speed**!

* **FastAPI** is crowned as the fastest web framework for Python and thus we use it for our backend development.
* The database of my choice is the **asynchronous** version of **PostgreSQL** (via [SQLAlchemy 2.0](https://docs.sqlalchemy.org/en/20/orm/extensions/asyncio.html)). Read [this blog from Packt](https://subscription.packtpub.com/book/programming/9781838821135/6/ch06lvl1sec32/synchronous-asynchronous-and-threaded-execution) if you want to educate yourself further about the topic **Asynchronous, Synchronous, Concurrency,** and **Parallelism**.
* **Docker** is a technology that packages an application into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime.

## What Code is included?

For the backend application:
* The project, linter, and test configurations in `backend/pyproject.toml`.
* 3 settings classes (development, staging, production) with the super class in `backend/src/config/settings/base.py`.
* Event logger in `backend/src/config/events.py`.
* The `Account` object table model in `backend/src/models/db/account.py`.
* The `Account` object schema model in `backend/src/models/schemas/account.py`.
* PostgreSQL database connection with asynchronous SQLAlchemy 2.0 in `backend/src/repository/database.py`.
* A custom SQLAlchemy Base class in `backend/src/repository/table.py`
* PostgreSQL database connection with asynchronous SQLAlchemy 2.0 in `backend/src/repository/database.py`.
* Database-related events e.g. database table registration by app startup in `backend/src/repository/events.py`.
* C. R. U. D. methods for `Account` object in `backend/src/repository/crud/account.py`.
* Table classes registration file in `backend/src/repository/base.py`.
* Alembic setup for auto-generating asynchronous database migrations in `backend/src/repository/migration/**`.
* Alembic main configuration file in `backend/alembic.ini`.
* Dependency injection for database session and repository in `backend/src/api/**`.
* API endpoints for `Account` signup and signin in `backend/src/api/routes/authentication.py`.
* API endpoints for `Account` get all, get 1, update, and delete in `backend/src/api/routes/account.py`.
* API endpoints registration file in `backend/src/api/endpoints`.
* Hashing and JWT generators and simple verification functions in `backend/src/securities/**`.
* Helper functions, string messages, and error handling in `backend/src/utilities/**`.
* A comprehensive FastAPI application initialization in `backend/src/main.py`.

For testing, I have prepared the following simple code to kick-start your test-driven development:
* A simple replication of the backend application for testing purposes and the asynchronous test client in `backend/tests/conftest.py`.
* 2 simple test functions to test the backend application initialization in `tests/unit_tests/test_src.py`.

For containerization:
* A `Docker` configuration that utilizes the 3.10 Python image in `backend/Dockerfile`.

For the team development environment:
* A pre-commit hooks for `Black`, `Isort`, and `MyPy` to ensure the conventional commit message before pushing an updated code into the remote repository in `.pre-commit-config.YAML`.
* **All secret variables are listed in `.env.example`. You need to copy these variables and set the values respectively to your need and save them in a new `.env` in the root directory.**

## Setup Guide

This backend application is setup with `Docker`. Nevertheless, you can see the full local setup without `Docker` in [backend/README.md](https://github.com/Aeternalis-Ingenium/FastAPI-Backend-Template/blob/trunk/backend/README.md).

1. Before setting up the backend app, please create a new directory called `coverage` for the testing report purpose:
   ```shell
   cd backend && mkdir coverage
   ```

2. Backend app setup:
    ```shell
    # Creating VENV
    conda create -n any_venv_name python=3.10
    conda activate any_venv_name

    # Install dependencies
    pip3 install -r requirements.txt

    # Test run your backend server
    uvicorn src.main:backend_app --reload
    ```

3. Testing with `PyTest`:
   Make sure that you are in the `backend/` directory.
   ```shell
   # For testing without Docker
   pytest
   
   # For testing within Docker
   docker exec backend_app pytest
   ```

4. `Pre-Commit` setup:
    ```shell
    # Make sure you are in the ROOT project directory
    pre-commit install
    pre-commit update
    ```

5. Backend app credentials setup:
    If you are not used to VIM or Linux CLI, then ignore the `echo` command and do it manually. All the secret variables for this template are located in `.env.example`.

    If you want to have another name for the secret variables, don't forget to change them also in:

    * `backend/src/config/base.py`
    * `docker-compose.yaml`

    ```shell
    # Make sure you are in the ROOT project directory
    touch .env

    echo "SECRET_VARIABLE=SECRET_VARIABLE_VALUE" >> .env
    ```

8. (IMPORTANT) Database setup:
   ```shell
    # (Local) Generate revision for the database auto-migrations
    alembic revision --autogenerate -m "YOUR MIGRATION TITLE"
    alembic upgrade head    # to register the database classes
   ```

## Project Structure

```shell
backend/
├── coverage/
├── src/
    ├── api/
        ├── dependencies/               # Dependency injections
            ├── session.py
            ├──repository.py
        ├── routes/                     # Endpoints
            ├── account.py              # Account routes
            ├── authentication.py       # Signup and Signin routes
        ├── endpoints.py                # Endpoint registration
    ├── config/
        ├── settings/
            ├── base.py                 # Base settings / settings parent class
                ├── development.py      # Development settings
                ├── environments.py     # Enum with PROD, DEV, STAGE environment
                ├── production.py       # Production settings
                ├── staging.py          # Test settings
        ├── events.py                   # Registration of global events
        ├── manager.py                  # Manage get settings
    ├── models/
        ├── db/
            ├── account.py              # Account class for database entity
        ├── schemas/
            ├── account.py              # Account classes for data validation objects
            ├── base.py                 # Base class for data validation objects
    ├── repository/
        ├── crud/
            ├── account.py              # C. R. U. D. operations for Account entity
            ├── base.py                 # Base class for C. R. U. D. operations
        ├── migrations/
            ├── versions/
            ├── env.py                  # Generated via alembic for automigration
            ├── script.py.mako          # Generated via alembic
        ├── base.py                     # Entry point for alembic automigration
        ├── database.py                 # Database class with engine and session
        ├── events.py                   # Registration of database events
        ├── table.py                    # Custom SQLAlchemy Base class
    ├── security/
        ├── hashing/
            ├── hash.py                 # Hash functions with passlib
            ├── password.py             # Password generator with hash functions
        ├── authorizations/
            ├── jwt.py                  # Generate JWT tokens with python-jose
        ├── verifications/
            ├── credentials.py          # Check for attributes' availability
    ├── utilities/
        ├── exceptions/
            ├── http/
                ├── http_exc_400.py     # Custom 400 error handling functions
                ├── http_exc_401.py     # Custom 401 error handling functions
                ├── http_exc_403.py     # Custom 403 error handling functions
                ├── http_exc_404.py     # Custom 404 error handling functions
            ├── database.py             # Custom `Exception` class
            ├── password.py             # Custom `Exception` class
        ├── formatters/
            ├── datetime_formatter.py   # Reformat datetime into the ISO form
            ├── field_formatter.py      # Reformat snake_case to camelCase
        ├── messages/
            ├── http/
                ├── http_exc_details.py	# Custom message for HTTP exceptions
    ├── main.py                         # Our main backend server app
├── tests/
    ├── end_to_end_tests/               # End-to-end tests
    ├── integration_tests/              # Integration tests
    ├── security_tests/                 # Security-related tests
    ├── unit_tests/                     # Unit tests
        ├── test_src.py                 # Testing the src directory's version
    ├── conftest.py                     # The fixture codes and other base test codes
├── Dockerfile                          # Docker configuration file for backend application
├── README.md                           # Documentation for backend app
├── alembic.ini                         # Automatic database migration configuration
├── pyproject.toml                      # Linter and test main configuration file
├── requirements.txt                    # Packages installed for backend app
.dockerignore                           # A file that list files to be excluded in Docker container
.gitignore                              # A file that list files to be excluded in GitHub repository
.pre-commit-config.yaml                 # A file with Python linter hooks to ensure conventional commit when committing
README.md                               # The main documentation file for this repository
```