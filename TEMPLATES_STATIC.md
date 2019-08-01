## Serve templates and static files from project root

Add directory pointers to the top of your settings.py file:

```python
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')
STATIC_DIR = os.path.join(BASE_DIR, 'static')
```

Modify TEMPLATES settings:

```python
'DIRS': [TEMPLATE_DIR,],
```

And replace your static file settings with:

```python
# The absolute path to the directory where collectstatic will collect static files for deployment.
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# Tell Django where it can find static files
STATICFILES_DIRS = [STATIC_DIR,]

# The URL to use when referring to static files (where they will be served from)
STATIC_URL = '/static/'
```
