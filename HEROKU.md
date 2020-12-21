# Setup a new Django project for deployment on Heroku

## Create Heroku app

Create a new Heroku app for your project. If your project requires a database tier higher than the default hobby tier, go ahead and provision it now as well.

## Create repo

Create a new repo for your project on GitHub with the README option checked and Python .gitignore selected. Clone it to local.

Then add the Heroku remote to git:
```
$ heroku git:remote -a herokuappname
```

## Virtual environment

Create and activate a virtual environment for your project, and install dependencies:

```
$ pip install django
$ pip install gunicorn
$ pip install dj-database-url
$ pip install psycopg2
$ pip install whitenoise
```

Or simply:

```
$ pip install django gunicorn dj-database-url psycopg2 whitenoise
```

### pyscopg2 trouble?

You may run into some trouble installing/building psycopg2. This can be caused by not having the proper prerequisite packages installed and in your path. See this link for more info: https://www.psycopg.org/docs/install.html#build-prerequisites

If you are on a Mac, your best bet is to install Postgres.app: https://postgresapp.com. And be sure to add it to your path: https://postgresapp.com/documentation/cli-tools.html. This should take care of any missing prerequisites when installing psycopg2. 

## Django setup

From the root of your project folder, run:

```
$ django-admin startproject config .
```

## Procfile, runtime.txt and requirements.txt

To deploy on Heroku, you will need to create the following files: 
* Procfile
* runtime.txt
* requirements.txt.

Create a file called `Procfile` (no extension) that tells Heroku to start and run your app with Gunicorn:

```
release: python manage.py migrate
web: gunicorn config.wsgi --log-file -
```

Create a `runtime.txt` file that contains just one line indicating the version of Python you are using. To get your exact version of Python use the command `$ python -V`.

runtime.txt:

```
python-3.9.1
```

Then use `$ pip freeze > requirements.txt` to generate your `requirements.txt` file:

```
asgiref==3.3.1
certifi==2020.12.5
dj-database-url==0.5.0
Django==3.1.4
gunicorn==20.0.4
psycopg2==2.8.6
pytz==2020.4
sqlparse==0.4.1
whitenoise==5.2.0
```

## .env file

We will use the method of having one settings file that works across all development environments (local, staging, dev) with variables handled by importing values from the respective environments. 

For your local machine, create a .env file in your project root with the following contents:

```
export DJANGO_SECRET_KEY=""
export DJANGO_DEBUG="Truthy"
export DATABASE_URL=""
```

We will add values to these variables later. 

Note: Add `.env` to your .gitignore file if it's not already present. 

## settings.py

Make sure you have the following imports at the top of your settings.py file:

```python
import os
import dj_database_url
````

Copy/paste the auto-generated secret key in settings.py to your .env `DJANGO_SECRET_KEY` variable. Then replace the entire line in settings.py with:

```python
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')
```

Now set the secret key variable on Heroku using the command:

`$ heroku config:set DJANGO_SECRET_KEY=$(python -c 'import random; import string; print("".join([random.SystemRandom().choice("{}{}{}".format(string.ascii_letters, string.digits, string.punctuation)) for i in range(50)]))')`

Replace `DEBUG = True` with:

```python
DEBUG = bool(os.environ.get('DJANGO_DEBUG', False))
```
Note that any value in your environment variable `DJANGO_DEBUG` besides an empty string -- or the outright absence of that environment variable -- will evaluate to `True`. Using this method, you do NOT need to set `DJANGO_DEBUG` on Heroku at all, unless for some reason you wanted to run debug mode in production (don't). 

Modify allowed hosts to include your domain name(s), Heroku app domain, and localhosts:

```python
ALLOWED_HOSTS = [
    'www.myproject.com',
    'myproject.com',
    'myproject.herokuapp.com',
    'localhost',
    '127.0.0.1',
]
```

To take advantage of Heroku's free / auto-managed SSL certificates, add the following. This will redirect all users to https:

```python
# Honor the 'X-Forwarded-Proto' header for request.is_secure()
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
SECURE_SSL_REDIRECT = bool(os.environ.get('DJANGO_SSL_REDIRECT', False))
```

Then set a Heroku DJANGO_SSL_REDIRECT environment variable:

`$ heroku config:set DJANGO_SSL_REDIRECT=True`

Whitenoise will take care of serving your static files on Heroku. Add it to your middleware in settings.py above all other middleware except SecurityMiddleware:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Get your database URL from the environment. Replace all of the auto-generated `DATABASES` code with:

```python
DATABASES = {'default': dj_database_url.config(conn_max_age=500)}
```

Now add your full database URL to your local .env file. The DATABASE_URL variable has already been added to production automatically by Heroku. 

Add this to your static file settings:

```python
# The absolute path to the directory where collectstatic will collect static files for deployment.
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Add `staticfiles/` to your .gitignore file. 

For additional logging for collectstatic in production, set the following environment variable:
```
$ heroku config:set DEBUG_COLLECTSTATIC=1
```

## Local

Load your environment variables:

```
$ source .env
```

Run the intial database migration on your local database:

 ```
 $ python manage.py migrate
 ```

Create a superuser:

```
$ python manage.py createsuperuser
```

Confirm everything is working:

```
$ python manage.py runserver
```

## Push / Deploy

Commit changes:

```
$ git add .
$ git commit -m 'Project setup'
$ git push origin main
```

Push to Heroku:

```
$ git push heroku master
```

Run migrations, create superuser, and open your live app:

```
$ heroku run python manage.py migrate
$ heroku run python manage.py createsuperuser
$ heroku open
```
