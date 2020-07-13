# Deploying a Django app (built with our project template) to Heroku

- Create new app at [https://dashboard.heroku.com/new-app](https://dashboard.heroku.com/new-app)

- Make sure you are logged into the Heroku CLI: `heroku login`. If you have not installed the CLI, go to [https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli) to install it.

- Add the heroku remote to your Git repository: `heroku git:remote -a <app-name>`

- Install psycopg2 in your Django app: `poetry add psycopg2`

  - If you have an error saying PostgreSQL cannot be found, run `brew install postgresql`

  - to convert your development environment to use PostgreSQL, see below

  - If you get an error that’s a big wall of text with `lssl` somewhere toward the bottom, run `brew info openssl` and then run the commands that it tells you to.

- Install django-heroku in your Django app: `poetry add django-heroku`

- Add django-heroku to your settings.py. At the bottom of settings.py, add

```py
# Configure Django App for Heroku.
import django_heroku
django_heroku.settings(locals())
del DATABASES['default']['OPTIONS']['sslmode']
```

- Commit your code after adding django-heroku.

- Install gunicorn in your Django app: `poetry add gunicorn`.

- Add a new file called Procfile:

```
web: gunicorn <project_dir>.wsgi
```

- Commit your code after adding gunicorn and a Procfile.

* Set up the Poetry buildpack for Heroku:

```
heroku buildpacks:clear
heroku buildpacks:add https://github.com/moneymeets/python-poetry-buildpack.git
heroku buildpacks:add heroku/python
heroku addons:create heroku-postgresql:hobby-dev
```

- Set a secret key just for Heroku: `heroku config:set SECRET_KEY=$(date | md5)`
- Make sure your version of Python in `pyproject.toml` is set to an exact version.
- Push to Heroku: `git push heroku <your-git-branch>:master`. You will likely have a failure the first time. Debug. Your Git branch is probably `main`.

- Run migrations on Heroku: `heroku run python3 manage.py migrate`

- Create a superuser on Heroku: `heroku run python3 manage.py createsuperuser`

- Commit your code and push to Heroku.

* Once you are sure your app works, turn off DEBUG on Heroku.

## Converting your dev environment to use PostgreSQL

- Install PostgreSQL: `brew install postgresql`

- Start PostgreSQL: `brew services start postgresql`

- Create a DB user (usually, you should use the same name as your app): `createuser -d <username>`

- Create a database (usually, the same name as your app): `createdb -U <username> <dbname>`

- Add the following to .env:

```
DATABASE_URL=postgres://<username>:@127.0.0.1:5432/<dbname>
```
