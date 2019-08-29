# Resize and optimize images upon upload

The following uses the Pillow library to resize and optimize images to a specified width. It maintains aspect ratio automatically via `thumbnail`. This snippet does not upsize images; if the image is smaller than the width specified in `thumbnail()` it will only compress it and will not change its display dimensions.

This method does the resizing and compression in memory BEFORE it saves to disk, thus keeping your S3 bucket lean and mean.

First import the Pillow library:

```python
pip install Pillow
```

Import all required libraries in your models.py file:

```python
import sys
from io import BytesIO
from PIL import Image as Img

from django.core.files.uploadedfile import InMemoryUploadedFile
from django.db import models
```

And setup your model like so:

```python
class FeaturedImage(models.Model):
    name = models.CharField(max_length=100)
    image = models.ImageField(upload_to='images/')

    def __str__(self):
        return self.name

    @property
    def url(self):
        return self.image.url

    def save(self, *args, **kwargs):
        if self.image:
            img = Img.open(BytesIO(self.image.read()))
            if img.mode != 'RGB':
                img = img.convert('RGB')
            img.thumbnail((900, 900), Img.ANTIALIAS)
            output = BytesIO()
            img.save(output, format='JPEG', quality=60)
            output.seek(0)
            self.image= InMemoryUploadedFile(output,'ImageField', "%s.jpg" %self.image.name.split('.')[0], 'image/jpeg', sys.getsizeof(output), None)
        super(FeaturedImage, self).save(*args, **kwargs)
```

Customize `img.thumbnail((900, 900), Img.ANTIALIAS)` and `img.save(output, format='JPEG', quality=60)` as needed.

See https://pillow.readthedocs.io/en/3.1.x/index.html for more details on implementation.
