# Django Starter Project

An opinionated boilerplate for your Django projects.

## Tool Choices

* **Database:** [PostgreSQL](https://www.postgresql.org/)
* **Task Queue:** [Celery](http://www.celeryproject.org/) with [Redis](https://redis.io/)
* **Testing:** [PyTest](https://docs.pytest.org/en/latest/)
* **Logging:** [Sentry](https://sentry.io)
* **Static File Compiler:** [Webpack](https://webpack.js.org/)
* **JS lint:** [ESLint](https://eslint.org/)
* **Style lint:** [Stylelint](https://github.com/stylelint/stylelint)
* **Static File Storage:** [AWS S3](https://aws.amazon.com/s3/) or [Digital Ocean Spaces](https://www.digitalocean.com/products/spaces/)
* **Dev Orchestration:** [Docker](https://www.docker.com/) & [Docker Compose](https://docs.docker.com/compose/)

## Getting started

This is a guide on how to use this repo and save hours of boring configuration.

### 1) Add the boilerplate
Instead of running `django-admin startproject` to start your new project, clone this repo in a directory of your choosing

```bash
git clone git@github.com:fceruti/django-starter-project.git <new-directory>
```

At this point you may either

1) Start a clean git repo by removing the `.git` directory and then running `git init`.
2) Receive patches and updates of this repo by modifying `.git/config` and switch `[remote "origin"]` for `[remote "upstream"]`, and then add your own `origin` by running `git remote add origin <your_repo>`.

### 2) Set environment variables

Following the [12 Factor App Guide](https://www.12factor.net/), we will configure our app by setting the configuration variables in the environment. To do that, just create a file named `.env` in the root of the project and [django-environ](https://github.com/joke2k/django-environ) will pick it up and populate our settings.

Example:

```
ENV=dev
DEBUG=on
SECRET_KEY=123
DATABASE_URL=postgres://localhost:5432/django_db
EMAIL_BACKEND=django.core.mail.backends.console.EmailBackend
USE_DEBUG_TOOLBAR=on
```

### 3) Create and migrate database

I'll call this database `django_db` for the purposes of this guide, but you can call it whatever you want. Just watch out for careless copy-pasting.

Now run

```bash
pipenv run migrate
```

### 4) Run the project

You have two choices, either you turn every service on your own or you use docker-compose

#### A) Use Docker Compose

We need to override `DATABASE_URL` environment variable inside of the Docker containers to connect directly to your host machine. Create a file called `.env.docker` with the following content:

```
DATABASE_URL=postgres://<user>@host.docker.internal:5432/django_db
```

* **user** is the user on your host machine that has access to postgres in this case.

Now we are ready to start the project.

```bash
docker-compose up
```

Visit [http://127.0.0.1:8000](http://127.0.0.1:8000) and you'll see your site up and running.

#### B) Run by yourself
First of all, install [pyenv](https://github.com/pyenv/pyenv) so you can use the specified python version in `.python-version` file. Then, run `pip install pipenv` to install it's pip's successor: pipenv. Then install dependencies by running `pipenv install ...`.

Then make sure you have `redis-server` running and finally on 3 separate consoles run:

**worker**
```bash
pipenv run worker
```

**worker**
```bash
pipenv run server
```

**webpack**
```bash
cd assets
npm install
npm run dev
```

Visit [http://127.0.0.1:8000](http://127.0.0.1:8000) and you'll see your site up and running.


## Docker commands
Here are a few commands that may come in handy

| Command                                                               | Description                                                   |
| --------------------------------------------------------------------- | ------------------------------------------------------------- |
| `docker ps`                                                           | List all containers (-a to include stopped)                   |
| `docker system df -v`                                                 | Display a list of all images, containers and volumes          |
| `docker logs --follow <container_id>`                                 | Display the logs of a container                               |
| `docker exec -it <container_id> /bin/bash`                            | Attach into a running container                               |
| `docker run --rm <image_name> /bin/bash`                              | Run a docker container based on an image and get a prompt     |
| `docker-compose run --rm web /bin/bash`                               | Same as before but for services defined in docker-compose.yml |
| `docker-compose run --rm web /bin/bash -c 'python manage.py migrate'` | Run a management command                                      |

## Pipenv commands
These shortcuts are at your disposal:

| Command                     | Shortcut for                                    |
| --------------------------- | ----------------------------------------------- |
| `pipenv run server`         | `pipenv run python manage.py runserver_plus`    |
| `pipenv run tests`          | `pipenv run pytest`                             |
| `pipenv run celery`         | `pipenv run python manage.py celery_autoreload` |
| `pipenv run shell`          | `pipenv run python manage.py shell_plus`        |
| `pipenv run makemigrations` | `pipenv run python manage.py makemigrations`    |
| `pipenv run migrate`        | `pipenv run python manage.py migrate`           |

To compile your static files, you need to have npm installed and all the local dependencies (run `npm install`). Then can execute the following commands
| Command             | Shortcut for                          |
| ------------------- | ------------------------------------- |
| `npm run dev`       | `webpack --mode development --watch`  |
| `npm run build`     | `webpack --mode production`           |
| `npm run lint:js`   | `eslint js/** --fix`                  |
| `npm run lint:csss` | `stylelint scss/*.scss --syntax scss` |

### Environment variables
These environment variables can be provided to configure your project.

#### Django
| Name                | Values                            | Default                                     | Description                                                                                                                   |
| ------------------- | --------------------------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| ENV                 | dev, test, qa, prod               | prod                                        | Indicates in which environment the project is running on                                                                      |
| DEBUG               | on, off                           | off                                         | Run server in debug mode                                                                                                      |
| LANGUAGE_CODE       | Language Identifier (RFC 3066)    | en-US                                       | [List of language codes](http://www.i18nguy.com/unicode/language-identifiers.html)                                            |
| TIME_ZONE           | Record of IANA time zone database | America/Santiago                            | [List of timezones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)                                             |
| USE_I18N            | on, off                           | on                                          | Enable translation system                                                                                                     |
| USE_L10N            | on, off                           | on                                          | Enable localized formatting                                                                                                   |
| USE_TZ              | on, off                           | on                                          | Enable timezone aware dates in                                                                                                |
| DEFAULT_FROM_EMAIL  | E-mail address                    | --                                          | Email address from which transactional emails will be sent from                                                               |
| EMAIL_BACKEND       | Path to backend                   | django.core.mail.backends.smtp.EmailBackend | The backend to use for sending emails. [List of backends](https://docs.djangoproject.com/en/2.2/topics/email/#email-backends) |
| SECRET_KEY          | Random string                     | --                                          | Used to provide cryptographic signing                                                                                         |
| ALLOWED_HOSTS       | List of domains                   | --                                          | Represents the host/domain names that this site can serve.                                                                    |
| DJANGO_DATABASE_URL | Database url                      | --                                          | Describes the database connection with [a url structure](https://github.com/joke2k/django-environ).                           |
| LOGIN_URL           | Url                               | /login/                                     | Url to redirect users when login is needed                                                                                    |
| LOGIN_REDIRECT_URL  | Url                               | /                                           | Url to redirect users after login in                                                                                          |
| STATIC_URL          | Url                               | /static/                                    | Url from which static files are served                                                                                        |
| MEDIA_URL           | Url                               | /media/                                     | Url from which media files are served                                                                                         |

#### Celery
| Name                     | Values       | Default | Description                                                                                                 |
| ------------------------ | ------------ | ------- | ----------------------------------------------------------------------------------------------------------- |
| CELERY_BROKER_URL        | Database url | --      | A common value for development is to use redis://cache, but it's recommended for production to use RabbitMQ |
| CELERY_TASK_ALWAYS_EAGER | on, off      | off     | If this is True, all tasks will be executed locally by blocking until the task returns.                     |

#### Django Storages
[Documentation](https://django-storages.readthedocs.io/en/latest/)

| Name                    | Values  | Default | Description                  |
| ----------------------- | ------- | ------- | ---------------------------- |
| USE_S3_STATIC_STORAGE   | on, off | off     | Whether or not to use S3     |
| AWS_ACCESS_KEY_ID       | str     | --      | AWS Access key id            |
| AWS_SECRET_ACCESS_KEY   | str     | --      | AWS Secret access key        |
| AWS_STORAGE_BUCKET_NAME | str     | --      | Name of S3 bucket to be used |

#### Django Debug Toolbar
| Name              | Values  | Default | Description                  |
| ----------------- | ------- | ------- | ---------------------------- |
| USE_DEBUG_TOOLBAR | on, off | off     | Enables django debug toolbar |

#### Django Registration Redux
[Documentation](https://django-registration-redux.readthedocs.io)

| Name                    | Values  | Default | Description                                                                                                  |
| ----------------------- | ------- | ------- | ------------------------------------------------------------------------------------------------------------ |
| ACCOUNT_ACTIVATION_DAYS | int     | 7       | How long (in days) after signup an account has in which to activate.                                         |
| REGISTRATION_OPEN       | on, off | on      | Indicates whether registration of new accounts is currently permitted.                                       |
| REGISTRATION_AUTO_LOGIN | on, off | on      | If this is True, your users will automatically log in when they click on the activation link in their email. |

#### Logging & Sentry
| Name       | Values  | Default | Description                                       |
| ---------- | ------- | ------- | ------------------------------------------------- |
| LOGS_ROOT  | path    | --      | Path to the directory where logs are to be stored |
| USE_SENTRY | on, off | off     | Enables sentry                                    |
| SENTRY_DSN | string  | --      | Private URL-like configuration                    |


### VSCode settings

Nowadays, my go-to editor is VSCode, so here's a template for `.vscode/settings.json`:

```
{
    // Interpreter
    "python.pythonPath": "<path>",
    "python.envFile": "${workspaceFolder}/.env",

    // Linting
    "python.linting.enabled": true,
    "python.linting.pylintEnabled": false,
    "python.linting.flake8Enabled": true,

    // Search
    "search.exclude": {
        "**/.git": true,
        "**/.vscode": true,
        "**/node_modules": true,
        "**/static": true,
        "**/media": true,
        "**/logs": true,
        "**/tmp": true,
        "**/locale": true,
    },
    "search.showLineNumbers": true,

    // Eslint
    "eslint.options": {
        "configFile": ".eslintrc.json"
    },
    "eslint.nodePath": "assets/node_modules/",
    "eslint.workingDirectories": [
        "assets/"
    ],
    "eslint.autoFixOnSave": true,

    // Stylelint
    "css.validate": false,
    "less.validate": false,
    "scss.validate": false,
    "stylelint.enable": true,
    "stylelint.autoFixOnSave": true
}
```

To fill the `python.pythonPath` run `pipenv --py` after initializing the directory's virtual environment.
