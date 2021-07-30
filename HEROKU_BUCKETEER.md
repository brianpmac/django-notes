# Host file uploads on S3

First, provision a Bucketeer instance on your Heroku app. The appropriate environment variables will be added automatically. You can copy them down to your local as well. 

Now add this to your settings.py:

```python
# Media files
# https://testdriven.io/blog/storing-django-static-and-media-files-on-amazon-s3/
AWS_STORAGE_BUCKET_NAME = os.environ.get('BUCKETEER_BUCKET_NAME')
AWS_ACCESS_KEY_ID = os.environ.get('BUCKETEER_AWS_ACCESS_KEY_ID')
AWS_SECRET_ACCESS_KEY = os.environ.get('BUCKETEER_AWS_SECRET_ACCESS_KEY')
AWS_S3_CUSTOM_DOMAIN = '%s.s3.amazonaws.com' %AWS_STORAGE_BUCKET_NAME

MEDIA_ROOT = '/media/'
AWS_LOCATION = 'public/' # Default folder to upload files into on bucket.
MEDIA_URL = "https://%s/" % AWS_S3_CUSTOM_DOMAIN
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
AWS_S3_FILE_OVERWRITE = False
```

Then install the following:
```bash
$ pip install Pillow django-storages boto3
```

To add an image field to your models use:
```python
image = models.ImageField()
```

Make your migration and be sure to source your env variables on local. Now you can try uploading an image on your model in Django admin to test things out.

For more details, and using S3 directly without the Bucketeer shortcut, see: https://testdriven.io/blog/storing-django-static-and-media-files-on-amazon-s3/
