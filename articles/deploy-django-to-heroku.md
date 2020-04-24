# Deploying a Django app to Heroku

* Create new app at [https://dashboard.heroku.com/new-app](https://dashboard.heroku.com/new-app)

* Make sure you are logged into the Heroku CLI: `heroku login`. If you have not installed the CLI, go to [https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli) to install it.

* Add heroku remote to your Git repository: `heroku git:remote -a <app-name>`

* Install psycopg2 in your Django app: `pipenv install psycopg2`

    * If you have an error saying PostgreSQL cannot be found, run `brew install postgresql`

    * to convert your development environment to use PostgreSQL, see below

    * If you get an error that’s a big wall of text with `lssl` somewhere toward the bottom, run `brew info openssl` and then run the commands that it tells you to.

* Install django-heroku in your Django app: `pipenv install django-heroku`

* Add django-heroku to your settings.py. At the bottom of settings.py, add

```py
# Configure Django App for Heroku.
import django_heroku
django_heroku.settings(locals())
```

* Commit your code after adding django-heroku.

* Install gunicorn in your Django app: `pipenv install gunicorn`.

* Add a new file called Procfile:

```
web: gunicorn <project_dir>.wsgi
```

* Commit your code after adding gunicorn and a Procfile.

* Set a secret key just for Heroku: `heroku config:set SECRET_KEY=$(date | md5)`

* Push to Heroku: `git push heroku master`. You will likely have a failure the first time. Debug.

* Run migrations on Heroku: `heroku run python3 manage.py migrate`

* Create a superuser on Heroku: `heroku run python3 manage.py createsuperuser`

* If not using our Django project template

    * Update your settings.py to look in your environment variables for the SECRET_KEY:

```py
SECRET_KEY = os.getenv('SECRET_KEY', '<old-secret-key>')
```

* Commit your code and push to Heroku.

    * Once you are sure your app works, turn off DEBUG on Heroku.

To turn off DEBUG, replace "DEBUG = True" in your settings.py with:

```py
in_production = bool(os.getenv('PRODUCTION'))
DEBUG = not in_production
```

And then run `heroku config:set PRODUCTION=True`

## Converting your dev environment to use PostgreSQL

* Install PostgreSQL: `brew install postgresql`

* Start PostgreSQL: `brew services start postgresql`

* Create a DB user (usually, you should use the same name as your app): `createuser -d <username>`

* Create a database (usually, the same name as your app): `createdb -U <username> <dbname>`

* If using project template, add the following to .env:

```
DATABASE_URL=postgres://<username>:@127.0.0.1:5432/<dbname>
```

* Else, update DATABASES in your settings.py to look like this:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '<dbname>',
        'USER': '<username>',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

