# Django dev tip

## Model

### specifying database to use

In the site setting file, find the data base setting

### specifing the table name of a model class

In the meta class, define the db_table propertuy like the following:

```python
db_table = 'music_album'
```

### Generating Model from existing tables

```
python manage.py  inspectdb
```

This command will generate the python code of the
Model classes, copy them to the applications and
change the managed field in the meta class to `True`
or simply remove it, and then run

```
python manage.py migrate
```

### mapping a model to a created table

If you have created a table and just want to map to it,
if you run `python manage.py migrate`, it will complain
that the table already exists.

In this case, run the following command:

```python
python manage.py migrate --fake <appname>
```

### fields

#### DateTimeField

http://stackoverflow.com/questions/2771676/django-datetime-issues-default-datetime-now

```
date = models.DateTimeField(auto_now_add=True, blank=True)
```

#### CharField

#### ForeignField

1. Define a foreign key to it self

    parentId = models.ForeignKey("self")

or
    parentId = models.ForeignKey("CategoryModel")


### querying using model objects

we can use fields to do queries on the model class.
But If we want to query using the fields of a related object
by foreign key, we need to use `__` to as field separator.

E.g

    Entry.objects.filter(pub_date__year=2006)

Is to filter on the entries where the the year of pub_date,
which is a related object is 2006

### relation ship

#### many to many

#### one to many

How to access the set of related objects from the `many side`?
Django adds a special property in the many side, `<relation_name>_set`
all in lower case[^1].


### delete object

Using `delete` method on the object to remove the object from
the database.

```
Entry.objects.get(pk=1).delete()
```

We can also use this method on the result set object.

```
Entry.objects.all().delete()
```

### Qeuery

ResultSet object are setup in the `objects` field of model class.
Queries are made against this object.


1. Get an object by primary key

```
from models import Model

m = Model.objects.get(pk=1)
```

2. getting multiple object using `filter`

```
objects = Model.objects.get(name='xxx')
```
This returns an result set on which we can xalso use list indexing
operations `[]` to get individual object.

## View

### Simple view

In its simplest case, a view is just a function that takes at least `HttpRequest`
as argument. The following is an example:

```python
def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

### Capturing  arguments

### Generic View

### Template


## URLConfig, the controller that connect user request to views

In the project folder and each app folder, there is a file called
`urls.py`, which is responsible for controlling dispatching user request
to the views defined in the project. `urls.py` looks like this:

```
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]

```

The elements defined in urlpatterns are the `routing rules`.
When there is a request from the user,django framework will
extract the url(the part excluding the host) and match it against
each element in this list and dispatch the request to the view in the
second part.

You can also dispatch it to another urls definition. the first line
uses `include` directive to do so.

## Django RESTful framework

## Filter

https://django-filter.readthedocs.io/en/latest/usage.html
http://www.django-rest-framework.org/api-guide/filtering/

## run django with uwsgi and nginx

### Serving static files

#### Setting up STATIC_URL

`STATIC_URL` is the part that will be appended to the urls when accessing static files.

#### Setting up STATIC_ROOT

`STATIC_ROOT` is the directory where the static files will be output by `manage.py collectstatic`

#### setting up rules in nginx

    location /static/  {
        alias /path/to/static/;
    }

### uploading files

## admin app

### Filtering by foreign key fields

in the admin class of the model class, add an item in the filter_list the format
`<relation_field_name>__<field_name_in_the_related_class>`

### Add model to the admin site

In admin.py

```
# Register your models here.
admin.site.register(ChallengeBinary)
```

### Create an admin user:

```
python manage.py createsuperuser
.....
```

Ref: https://tutorial.djangogirls.org/en/django_admin/


### change which columns to show

Add an admin class subclassing `admin.ModelAdmin`:
```
class BookAdmin(admin.ModelAdmin):
    list_display = ('Title', 'Author', 'Price')

```

Register it:

```
admin.site.register(Book, BookAdmin)
```

Ref: https://stackoverflow.com/questions/10543032/how-to-show-all-fields-of-model-in-admin-page

## celery

https://realpython.com/blog/python/asynchronous-tasks-with-django-and-celery/


### Integrating with django

Integrating celery into django

Ref: http://docs.celeryproject.org/en/latest/django/first-steps-with-django.html

#### Config result backend and message broker

In setttings.py

```
CELERY_RESULT_BACKEND = 'django-db'
CELERY_BROKER_URL = 'pyamqp://'
```

### Using the code outside of django

```
import django

# setup django
os.environ['DJANGO_SETTINGS_MODULE'] = 'driller_mng.settings'
django.setup()
```

## debug

When debug is turned off in settings.py, by default admin is not allowed to be accessed,
we need to setup allowed hosts in `ALLOWED_HOSTS`


## other tips



## references
* [Django get started](https://docs.djangoproject.com/en/1.10/intro/)
* http://stackoverflow.com/questions/25924858/django-1-7-migrate-gets-error-table-already-exists
* [How To Serve Django Applications with uWSGI and Nginx on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-ubuntu-14-04)
* [https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-ubuntu-16-04)
* [Setting up Django and your web server with uWSGI and nginx](http://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html)
* [nginx not serving admin static files?](http://serverfault.com/questions/403264/nginx-not-serving-admin-static-files)
* [Managing static files (e.g. images, JavaScript, CSS)](https://docs.djangoproject.com/en/dev/howto/static-files/#deploying-static-files-in-a-nutshell)
* [ALLOWED HOSTS](https://docs.djangoproject.com/en/1.9/ref/settings/#allowed-hosts)
* [Django self-referential foreign key](http://stackoverflow.com/questions/15285626/django-self-referential-foreign-key)

[^1]: http://stackoverflow.com/questions/19799955/django-get-the-set-of-objects-from-many-to-one-relationship
