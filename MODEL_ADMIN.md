# Django ModelAdmin Reference
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#modeladmin-options

# ModelAdmin.actions
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.actions

A list of actions to make available on the change list page.

# ModelAdmin.date_hierarchy
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.date_hierarchy

Set date_hierarchy to the name of a DateField or DateTimeField in your model, and the change list page will include a date-based drilldown navigation by that field.
```python
date_hierarchy = 'pub_date'
```
You can also specify a field on a related model using the __ lookup, for example:
```python
date_hierarchy = 'author__pub_date'
```

# ModelAdmin.empty_value_display
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.empty_value_display

This attribute overrides the default display value for record’s fields that are empty (None, empty string, etc.). The default value is - (a dash).
```python
empty_value_display = '-empty-'
```

# ModelAdmin.exclude
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.exclude

This attribute, if given, should be a list of field names to exclude from the form.

# ModelAdmin.fields
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fields

Use the fields option to make simple layout changes in the forms on the “add” and “change” pages such as showing only a subset of available fields, modifying their order, or grouping them into rows.
```python
fields = ('url', 'title', 'content')
```
Multiple fields, same line:
```python
fields = (('url', 'title'), 'content')
```

# ModelAdmin.fieldsets
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets

```python
fieldsets = (
    (None, {
        'fields': ('url', 'title', 'content', 'sites')
    }),
    ('Advanced options', {
        'classes': ('collapse',),
        'fields': ('registration_required', 'template_name'),
    }),
)
```

# ModelAdmin.inlines
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.inlines

# ModelAdmin.list_display
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display

Tons of options, see docs.
```python
list_display = ('first_name', 'last_name')
```

# ModelAdmin.list_display_links
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display_links

Use list_display_links to control if and which fields in list_display should be linked to the “change” page for an object.
```python
list_display_links = ('first_name', 'last_name')
```

# ModelAdmin.list_editable
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_editable

# ModelAdmin.list_filter
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter

# ModelAdmin.list_max_show_all
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_max_show_all

Set list_max_show_all to control how many items can appear on a “Show all” admin change list page. The admin will display a “Show all” link on the change list only if the total result count is less than or equal to this setting. By default, this is set to 200.

# ModelAdmin.list_per_page
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_per_page

# ModelAdmin.list_select_related
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_select_related

Set list_select_related to tell Django to use select_related() in retrieving the list of objects on the admin change list page. This can save you a bunch of database queries.

# ModelAdmin.ordering
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.ordering

Set ordering to specify how lists of objects should be ordered in the Django admin views. This should be a list or tuple in the same format as a model’s ordering parameter.
```python
ordering = ('-date',)
ordering = ('-pub_date', 'author')
```

# ModelAdmin.prepopulated_fields
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.prepopulated_fields

Set prepopulated_fields to a dictionary mapping field names to the fields it should prepopulate from:

```python
prepopulated_fields = {"slug": ("title",)}
```

# ModelAdmin.preserve_filters
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.preserve_filters

By default, applied filters are preserved on the list view after creating, editing, or deleting an object. You can have filters cleared by setting this attribute to False.

# ModelAdmin.readonly_fields
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.readonly_fields
```python
readonly_fields = ('address_report',)
```

# ModelAdmin.save_as
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.save_as

Use this as an easy way to duplicate objects!

# ModelAdmin.save_on_top
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.save_on_top

Add save buttons to top of page as well as bottom.

# ModelAdmin.search_fields
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields

Lot's of flexibility, see docs.
```python
search_fields = ['user__email']
```

# ModelAdmin.sortable_by
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.sortable_by

Only use this to disable certain columns for sorting. By default all colunns are sortable.

# ModelAdmin.view_on_site
https://docs.djangoproject.com/en/3.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.sortable_by

# Example Inline Model
```python
class MyInlineModelAdmin(admin.TabularInline):
    model = Model
    fields = ()


class MyMainModelAdmin(admin.ModelAdmin):
    list_display = ()
    list_filter = ()
    inlines = (MyInlineModelAdmin,)

 admin.site.register(MyMyMainModel, MyMainModelAdmin)
```

# All The Things
```python
date_hierarchy = ''
exclude = ()
fields = ()
fieldsets = (
    ('Section Title', {
        'fields': (
            '',
            '',
            '',
        ),
    }),
)
inlines = ()
list_display = ()
list_display_links = ()
list_editable = ()
list_filter = ()
ordering = ('-date',)
prepopulated_fields = {"slug": ("title",)}
readonly_fields = ()
save_as = True
save_on_top = True
search_fields = ()
```
