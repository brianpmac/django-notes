# Setup a new Django project for deployment on Heroku

## Create Heroku app

Create a new Heroku app for your project. If your project requires a database tier higher than the default hobby tier, go ahead and provision it now as well.

## Create repo

Create a new repo for your project on GitHub with the README option checked and Python .gitignore selected. Clone it to local.

Then add the Heroku remote to git:
```
$ heroku git:remote -a myproject
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
$ pip install django, gunicorn, dj-database-url, psycopg2, whitenoise
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

To deploy on Heroku, you will need to create the following files: Procfile, runtime.txt and requirements.txt.

Create a file called `Procfile` (no extension) that tells Heroku to start and run your app with Gunicorn:

```
web: gunicorn myproject.wsgi --log-file -
```

Create a `runtime.txt` file that contains just one line indicating the version of Python you are using. To get your exact version of Python use the command `$ python -V`.

runtime.txt:

```
python-3.7.3
```

Then use `$ pip freeze > requirements.txt` to generate your `requirements.txt` file:

```
certifi==2019.6.16
dj-database-url==0.5.0
Django==2.2.3
gunicorn==19.9.0
psycopg2==2.8.3
pytz==2019.1
sqlparse==0.3.0
whitenoise==4.1.3
```

## settings.py

Make sure you have the following imports at the top of your settings.py file:

```python
import os
import dj_database_url
````

Instruct Django to get the security key from the Heroku environment:

```python
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')
```

And set the secret key variable on Heroku using the command:

`$ heroku config:set DJANGO_SECRET_KEY=$(python -c 'import random; import string; print("".join([random.SystemRandom().choice("{}{}{}".format(string.ascii_letters, string.digits, string.punctuation)) for i in range(50)]))')`

Set debug to False:

```python
DEBUG = False
```

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

Add whitenoise to your middleware in settings.py above all other middleware except SecurityMiddleware:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Instruct Django to get the database URL from the Heroku environment:

```python
DATABASES = {'default': dj_database_url.config(conn_max_age=500)}
```

Add this to your static file settings:

```python
# The absolute path to the directory where collectstatic will collect static files for deployment.
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

To add additional logging for collectstatic, set the following environment variable:
```
$ heroku config:set DEBUG_COLLECTSTATIC=1
```

Finally, add this to very bottom of settings.py:

```python
########################################################
### ALWAYS KEEP THIS AT THE VERY BOTTOM OF THIS FILE ###
########################################################
try:
    from . local_settings import *
except ImportError:
    pass
```

## local_settings.py

First, make sure there is a `local_settings.py` entry in your .gitignore file.

Create a `local_settings.py` file at the same level as settings.py.

Add the following to it:
```python
SECRET_KEY = ''

DEBUG = True

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '',
        'USER': '',
        'PASSWORD': '',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

Generate a new local secret key and copy/paste into local_settings.py:

```
$ python -c 'import random; import string; print("".join([random.SystemRandom().choice("{}{}{}".format(string.ascii_letters, string.digits, string.punctuation)) for i in range(50)]))'
```

Create a local Postgres database and populate local_settings.py with your local database credentials.

## Local

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
$ git push origin master
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
