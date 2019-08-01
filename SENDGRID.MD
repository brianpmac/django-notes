## Add Sendgrid to your project

In settings.py:

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.sendgrid.net'
EMAIL_HOST_USER = 'apikey'
EMAIL_HOST_PASSWORD = os.environ.get('SENDGRID_API_KEY')
EMAIL_PORT = 587
EMAIL_USE_TLS = True
DEFAULT_FROM_EMAIL = 'My Project <noreply@notifications.myprojectname.com>'
```

Then be sure to set the SENDGRID_API_KEY environment variable on local and production. 
