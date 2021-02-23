# Deploying a Django app (built with our project template) to Heroku

- Create new app at [https://dashboard.heroku.com/new-app](https://dashboard.heroku.com/new-app)

- In your new app on the Heroku website, click "Resources." Click the text box under "Addons." Type "postgres" in the box and choose "Heroku Postgres." Add the hobby plan.

- Make sure you are logged into the Heroku CLI: `heroku login`. If you have not installed the CLI, go to [https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli) to install it.

- Add the heroku remote to your Git repository: `heroku git:remote -a <app-name>`

- **If your application is in a subdirectory, follow the directions under "If your application is in a subdirectory" below before doing anything else.**

- Install psycopg2 in your Django app: `pipenv install psycopg2-binary`

  - If you have an error saying PostgreSQL cannot be found, run `brew install postgresql`

  - to convert your development environment to use PostgreSQL, see below

  - If you get an error that’s a big wall of text with `lssl` somewhere toward the bottom, run `brew info openssl` and then run the commands that it tells you to.

- Install [django-on-heroku](https://pypi.org/project/django-on-heroku/) in your Django app: `pipenv install django-on-heroku`

- Add django-on-heroku to your settings.py. At the bottom of settings.py, add

```py
# Configure Django App for Heroku.
import django_on_heroku
django_on_heroku.settings(locals())
del DATABASES['default']['OPTIONS']['sslmode']
```

- Install gunicorn in your Django app: `pipenv install gunicorn`.

- Add a new file called `Procfile` with the below contents (replace `<project_dir>` with your project directory, probably `project`):

```
web: gunicorn <project_dir>.wsgi
```

- Commit your code.
- Set a secret key just for Heroku: `heroku config:set SECRET_KEY=$(date | md5)`
- Push to Heroku: `git push heroku main`.

- Run migrations on Heroku: `heroku run python3 manage.py migrate`

- Create a superuser on Heroku: `heroku run python3 manage.py createsuperuser`

## If your application is in a subdirectory

- Run the following:

```
heroku buildpacks:clear
heroku buildpacks:set https://github.com/timanovsky/subdir-heroku-buildpack
heroku buildpacks:add heroku/python
heroku config:set PROJECT_PATH=<subdir>
```



## Converting your dev environment to use PostgreSQL

- Install PostgreSQL: `brew install postgresql`

- Start PostgreSQL: `brew services start postgresql`

- Create a DB user (usually, you should use the same name as your app): `createuser -d <username>`

- Create a database (usually, the same name as your app): `createdb -U <username> <dbname>`

- Edit the DATABASE_URL key in the .env file to set it to the url for the postgres database. Make sure to remove the path to the sqlite database that is already there. Replace the values in the angle brackets with the values you set in the above steps:

```
DATABASE_URL=postgres://<username>:@127.0.0.1:5432/<dbname>
```
