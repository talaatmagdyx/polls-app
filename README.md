# django first step

Class: ENG 455
Created: January 5, 2022 9:20 PM
Materials: https://docs.djangoproject.com/en/4.0/intro/tutorial01/
Reviewed: Yes
Type: Section

## how to creating new project ?

```jsx
django-admin startproject name_of_project
```

structure of project

```jsx
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

These files are:

- The outer **`mysite/`** root directory is a container for your project. Its name doesn’t matter to Django; you can rename it to anything you like.
- **`manage.py`**: A command-line utility that lets you interact with this Django project in various ways. You can read all the details about **`manage.py`** in [django-admin and manage.py](https://docs.djangoproject.com/en/4.0/ref/django-admin/).
- The inner **`mysite/`** directory is the actual Python package for your project. Its name is the Python package name you’ll need to use to import anything inside it (e.g. **`mysite.urls`**).
- **`mysite/__init__.py`**: An empty file that tells Python that this directory should be considered a Python package. If you’re a Python beginner, read [more about packages](https://docs.python.org/3/tutorial/modules.html#tut-packages) in the official Python docs.
- **`mysite/settings.py`**: Settings/configuration for this Django project. [Django settings](https://docs.djangoproject.com/en/4.0/topics/settings/) will tell you all about how settings work.
- **`mysite/urls.py`**: The URL declarations for this Django project; a “table of contents” of your Django-powered site. You can read more about URLs in [URL dispatcher](https://docs.djangoproject.com/en/4.0/topics/http/urls/).
- **`mysite/asgi.py`**: An entry-point for ASGI-compatible web servers to serve your project. See [How to deploy with ASGI](https://docs.djangoproject.com/en/4.0/howto/deployment/asgi/) for more details.
- **`mysite/wsgi.py`**: An entry-point for WSGI-compatible web servers to serve your project. See [How to deploy with WSGI](https://docs.djangoproject.com/en/4.0/howto/deployment/wsgi/) for more details.

## The development server

```jsx
python manage.py runserver

Starting development server at http://127.0.0.1:8000/
```

### **Changing the port**

```jsx
python manage.py runserver 8080

If you want to change the server’s IP, pass it along with the port. For example, to listen on all available public IPs (which is useful if you are running Vagrant or want to show off your work on other computers on the network), use:
python manage.py runserver 0:8000
```

**Projects vs. apps**

What’s the difference between a project and an app? An app is a web application that does something – e.g., a blog system, a database of public records or a small poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.

**Creating the Polls app**

```jsx
python manage.py startapp name
ex: 
python manage.py startapp polls
```

which is laid out like this:

`polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py`

This directory structure will house the poll application.

## How to add new view/api in project.

- make new method in polls/view.py

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

- make new route/path for index method in polls/urls.py

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

- The next step is to point the root URLconf at the **`polls.urls`** module. In **`mysite/urls.py`**, add an import for **`django.urls.include`** and insert an **`[include()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.include)`** in the **`urlpatterns`** list, so you have:

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

The **`[include()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.include)`** function allows referencing other URLconfs. Whenever Django encounters **`[include()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.include)`**, it chops off whatever part of the URL matched up to that point and sends the remaining string to the included URLconf for further processing.

The idea behind **`[include()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.include)`** is to make it easy to plug-and-play URLs. Since polls are in their own URLconf (**`polls/urls.py`**), they can be placed under “/polls/”, or under “/fun_polls/”, or under “/content/polls/”, or any other path root, and the app will still work.

**When to use `[include()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.include)`**

> You should always use **`include()`** when you include other URL patterns. **`admin.site.urls`** is the only exception to this.
> 

The **`[path()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.path)`** function is passed four arguments, two required: **`route`** and **`view`**, and two optional: **`kwargs`**, and **`name`**. At this point, it’s worth reviewing what these arguments are for.

- **`route`**

**`route`** is a string that contains a URL pattern. When processing a request, Django starts at the first pattern in **`urlpatterns`** and makes its way down the list, comparing the requested URL against each pattern until it finds one that matches.

- **`view`**

When Django finds a matching pattern, it calls the specified view function with an **`[HttpRequest](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpRequest)`** object as the first argument and any “captured” values from the route as keyword arguments. We’ll give an example of this in a bit.

- **`name`**

Naming your URL lets you refer to it unambiguously from elsewhere in Django, especially from within templates. This powerful feature allows you to make global changes to the URL patterns of your project while only touching a single file.

- **`kwargs`**

## Database setup

- open up **`mysite/settings.py`**.

> By default, the configuration uses SQLite
> 

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

- **`[ENGINE](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-DATABASE-ENGINE)`** Either **`'django.db.backends.sqlite3'`**, **`'django.db.backends.postgresql'`**, **`'django.db.backends.mysql'`**, or **`'django.db.backends.oracle'`**. Other backends are [also available](https://docs.djangoproject.com/en/4.0/ref/databases/#third-party-notes).
- **`[NAME](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-NAME)`** – The name of your database. If you’re using SQLite, the database will be a file on your computer; in that case, **`[NAME](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-NAME)`** should be the full absolute path, including filename, of that file. The default value, **`BASE_DIR / 'db.sqlite3'`**, will store the file in your project directory.

If you are not using SQLite as your database, additional settings such as **`[USER](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-USER)`**, **`[PASSWORD](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-PASSWORD)`**, and **`[HOST](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-HOST)`** must be added. For more details, see the reference documentation for **`[DATABASES](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-DATABASES)`**.

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

While you’re editing **`mysite/settings.py`**, set **`[TIME_ZONE](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-TIME_ZONE)`** to your time zone.

Also, note the **`[INSTALLED_APPS](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-INSTALLED_APPS)`** setting at the top of the file. That holds the names of all Django applications that are activated in this Django instance. Apps can be used in multiple projects, and you can package and distribute them for use by others in their projects.

```python
By default, INSTALLED_APPS contains the following apps, all of which come with Django:

django.contrib.admin – The admin site. You’ll use it shortly.
django.contrib.auth – An authentication system.
django.contrib.contenttypes – A framework for content types.
django.contrib.sessions – A session framework.
django.contrib.messages – A messaging framework.
django.contrib.staticfiles – A framework for managing static files.
```

Some of these applications make use of at least one database table, though, so we need to create the tables in the database before we can use them.

```python
python manage.py migrate
```

The **`[migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`** command looks at the **`[INSTALLED_APPS](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-INSTALLED_APPS)`** setting and creates any necessary database tables according to the database settings in your **`mysite/settings.py`** file and the database migrations shipped with the app (we’ll cover those later). You’ll see a message for each migration it applies. If you’re interested, run the command-line client for your database and type **`\dt`** (PostgreSQL), **`SHOW TABLES;`** (MariaDB, MySQL), **`.tables`** (SQLite), or **`SELECT TABLE_NAME FROM USER_TABLES;`** (Oracle) to display the tables Django created.

> **For the minimalists** Like we said above, the default applications are included for the common case, but not everybody needs them. If you don’t need any or all of them, feel free to comment-out or delete the appropriate line(s) from **`[INSTALLED_APPS](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-INSTALLED_APPS)`** before running **`[migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`**. The **`[migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`** command will only run migrations for apps in **`[INSTALLED_APPS](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-INSTALLED_APPS)`**.
> 

## Creating models

A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Django follows the [DRY Principle](https://docs.djangoproject.com/en/4.0/misc/design-philosophies/#dry). The goal is to define your data model in one place and automatically derive things from it.

This includes the migrations - unlike in Ruby On Rails, for example, migrations are entirely derived from your models file, and are essentially a history that Django can roll through to update your database schema to match your current models.

In our poll app, we’ll create two models: **`Question`** and **`Choice`**. A **`Question`** has a question and a publication date. A **`Choice`** has two fields: the text of the choice and a vote tally. Each **`Choice`** is associated with a **`Question`**.

These concepts are represented by Python classes. Edit the **`polls/models.py`** file so it looks like this:

```python
These concepts are represented by Python classes. Edit the polls/models.py file so it looks like this:

from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

Here, each model is represented by a class that subclasses **`[django.db.models.Model](https://docs.djangoproject.com/en/4.0/ref/models/instances/#django.db.models.Model)`**. Each model has a number of class variables, each of which represents a database field in the model.

Each field is represented by an instance of a **`[Field](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.Field)`** class – e.g., **`[CharField](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.CharField)`** for character fields and **`[DateTimeField](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.DateTimeField)`** for datetimes. This tells Django what type of data each field holds.

The name of each **`[Field](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.Field)`** instance (e.g. **`question_text`** or **`pub_date`**) is the field’s name, in machine-friendly format. You’ll use this value in your Python code, and your database will use it as the column name.

You can use an optional first positional argument to a **`[Field](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.Field)`** to designate a human-readable name. That’s used in a couple of introspective parts of Django, and it doubles as documentation. If this field isn’t provided, Django will use the machine-readable name. In this example, we’ve only defined a human-readable name for **`Question.pub_date`**. For all other fields in this model, the field’s machine-readable name will suffice as its human-readable name.

Some **`[Field](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.Field)`** classes have required arguments. **`[CharField](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.CharField)`**, for example, requires that you give it a **`[max_length](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.CharField.max_length)`**. That’s used not only in the database schema, but in validation, as we’ll soon see.

A **`[Field](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.Field)`** can also have various optional arguments; in this case, we’ve set the **`[default](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.Field.default)`** value of **`votes`** to 0.

Finally, note a relationship is defined, using **`[ForeignKey](https://docs.djangoproject.com/en/4.0/ref/models/fields/#django.db.models.ForeignKey)`**. That tells Django each **`Choice`** is related to a single **`Question`**. Django supports all the common database relationships: many-to-one, many-to-many, and one-to-one.

To include the app in our project, we need to add a reference to its configuration class in the **`[INSTALLED_APPS](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-INSTALLED_APPS)`** setting. The **`PollsConfig`** class is in the **`polls/apps.py`** file, so its dotted path is **`'polls.apps.PollsConfig'`**. Edit the **`mysite/settings.py`** file and add that dotted path to the **`[INSTALLED_APPS](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-INSTALLED_APPS)`** setting. It’ll look like this:

```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Now Django knows to include the **`polls`** app. Let’s run another command:

```python
python manage.py makemigrations polls
```

You should see something similar to the following:

`Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice`

By running **`makemigrations`**, you’re telling Django that you’ve made some changes to your models (in this case, you’ve made new ones) and that you’d like the changes to be stored as a *migration*.

Migrations are how Django stores changes to your models (and thus your database schema) - they’re files on disk. You can read the migration for your new model if you like; it’s the file **`polls/migrations/0001_initial.py`**. Don’t worry, you’re not expected to read them every time Django makes one, but they’re designed to be human-editable in case you want to manually tweak how Django changes things.

There’s a command that will run the migrations for you and manage your database schema automatically - that’s called **`[migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`**, and we’ll come to it in a moment - but first, let’s see what SQL that migration would run. The **`[sqlmigrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-sqlmigrate)`** command takes migration names and returns their SQL:

```python
python manage.py sqlmigrate polls 0001
```

> If you’re interested, you can also run **`[python manage.py check](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-check)`**; this checks for any problems in your project without making migrations or touching the database.
> 

Now, run **`[migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`** again to create those model tables in your database:

```python
python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Rendering model states... DONE
  Applying polls.0001_initial... OK
```

The **`[migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`** command takes all the migrations that haven’t been applied (Django tracks which ones are applied using a special table in your database called **`django_migrations`**) and runs them against your database - essentially, synchronizing the changes you made to your models with the schema in the database.

Migrations are very powerful and let you change your models over time, as you develop your project, without the need to delete your database or tables and make new ones - it specializes in upgrading your database live, without losing data. We’ll cover them in more depth in a later part of the tutorial, but for now, remember the three-step guide to making model changes:

- Change your models (in **`models.py`**).
- Run **`[python manage.py makemigrations](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-makemigrations)`** to create migrations for those changes
- Run **`[python manage.py migrate](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-migrate)`** to apply those changes to the database.

## Playing with the API

Now, let’s hop into the interactive Python shell and play around with the free API Django gives you. To invoke the Python shell, use this command:

```python
python manage.py shell
```

We’re using this instead of simply typing “python”, because **`manage.py`** sets the **`[DJANGO_SETTINGS_MODULE](https://docs.djangoproject.com/en/4.0/topics/settings/#envvar-DJANGO_SETTINGS_MODULE)`** environment variable, which gives Django the Python import path to your **`mysite/settings.py`** file.

Once you’re in the shell, explore the [database API](https://docs.djangoproject.com/en/4.0/topics/db/queries/):

```python
>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

Wait a minute. **`<Question: Question object (1)>`** isn’t a helpful representation of this object. Let’s fix that by editing the **`Question`** model (in the **`polls/models.py`** file) and adding a **`[__str__()](https://docs.djangoproject.com/en/4.0/ref/models/instances/#django.db.models.Model.__str__)`** method to both **`Question`** and **`Choice`**:

```python
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```

It’s important to add **`[__str__()](https://docs.djangoproject.com/en/4.0/ref/models/instances/#django.db.models.Model.__str__)`** methods to your models, not only for your own convenience when dealing with the interactive prompt, but also because objects’ representations are used throughout Django’s automatically-generated admin.

Let’s also add a custom method to this model:

```python
import datetime

from django.db import models
from django.utils import timezone

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

```
>>>frompolls.modelsimport Choice, Question

# Make sure our __str__() addition worked.
>>>Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>>Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>>Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>>fromdjango.utilsimport timezone
>>>current_year = timezone.now().year
>>>Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>>Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>>Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>>q = Question.objects.get(pk=1)
>>>q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>>q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>>q.choice_set.all()
<QuerySet []>

# Create three choices.
>>>q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>>q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>>c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>>c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>>q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>>q.choice_set.count()
3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>>Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>>c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>>c.delete()
```

## **Creating an admin user**

```python
python manage.py createsuperuser
```

Now, open a web browser and go to “/admin/” on your local domain e.g., [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/). You should see the admin’s login screen:

**Make the poll app modifiable in the admin**

But where’s our poll app? It’s not displayed on the admin index page.

Only one more thing to do: we need to tell the admin that **`Question`** objects have an admin interface. To do this, open the **`polls/admin.py`** file, and edit it to look like this:

Each view is responsible for doing one of two things: returning an **`[HttpResponse](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponse)`** object containing the content for the requested page, or raising an exception such as **`[Http404](https://docs.djangoproject.com/en/4.0/topics/http/views/#django.http.Http404)`**. The rest is up to you.

**Template namespacing**

Now we *might* be able to get away with putting our templates directly in **`polls/templates`** (rather than creating another **`polls`** subdirectory), but it would actually be a bad idea. Django will choose the first template it finds whose name matches, and if you had a template with the same name in a *different* application, Django would be unable to distinguish between them. We need to be able to point Django at the right one, and the best way to ensure this is by *namespacing* them. That is, by putting those templates inside *another* directory named for the application itself.

```html
polls/templates/polls/index.html

{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

```python
from django.http import HttpResponse
from django.template import loader

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

That code loads the template called **`polls/index.html`** and passes it a context. The context is a dictionary mapping template variable names to Python objects.

### **A shortcut: `[render()](https://docs.djangoproject.com/en/4.0/topics/http/shortcuts/#django.shortcuts.render)`**

It’s a very common idiom to load a template, fill a context and return an **`[HttpResponse](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponse)`** object with the result of the rendered template. Django provides a shortcut. Here’s the full **`index()`** view, rewritten:

polls/views.py

```python
from django.shortcuts import render

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

**Raising a 404 error**

```python
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

**A shortcut: `[get_object_or_404()](https://docs.djangoproject.com/en/4.0/topics/http/shortcuts/#django.shortcuts.get_object_or_404)`**

It’s a very common idiom to use **`[get()](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#django.db.models.query.QuerySet.get)`** and raise **`[Http404](https://docs.djangoproject.com/en/4.0/topics/http/views/#django.http.Http404)`** if the object doesn’t exist. Django provides a shortcut. Here’s the **`detail()`** view, rewritten:

```python
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

The **`[get_object_or_404()](https://docs.djangoproject.com/en/4.0/topics/http/shortcuts/#django.shortcuts.get_object_or_404)`** function takes a Django model as its first argument and an arbitrary number of keyword arguments, which it passes to the **`[get()](https://docs.djangoproject.com/en/4.0/ref/models/querysets/#django.db.models.query.QuerySet.get)`** function of the model’s manager. It raises **`[Http404](https://docs.djangoproject.com/en/4.0/topics/http/views/#django.http.Http404)`** if the object doesn’t exist.

## Removing hardcoded URLs in templates

Remember, when we wrote the link to a question in the **`polls/index.html`** template, the link was partially hardcoded like this:

```html
<**li**><**a** href="/polls/{{ question.id }}/">{{ question.question_text }}</**a**></**li**>
```

The problem with this hardcoded, tightly-coupled approach is that it becomes challenging to change URLs on projects with a lot of templates. However, since you defined the name argument in the **`[path()](https://docs.djangoproject.com/en/4.0/ref/urls/#django.urls.path)`** functions in the **`polls.urls`** module, you can remove a reliance on specific URL paths defined in your url configurations by using the **`{% url %}`** template tag:

```html
<**li**><**a** href="{% **url** 'detail' question.id %}">{{ question.question_text }}</**a**></**li**>
```

The way this works is by looking up the URL definition as specified in the **`polls.urls`** module. You can see exactly where the URL name of ‘detail’ is defined below:

```html
...
*# the 'name' value as called by the {% url %} template tag*path('<int:question_id>/', views.detail, name='detail'),
...
```

**Namespacing URL names**

The tutorial project has just one app, **`polls`**. In real Django projects, there might be five, ten, twenty apps or more. How does Django differentiate the URL names between them? For example, the **`polls`** app has a **`detail`** view, and so might an app on the same project that is for a blog. How does one make it so that Django knows which app view to create for a url when using the **`{% url %}`** template tag?

The answer is to add namespaces to your URLconf. In the **`polls/urls.py`** file, go ahead and add an **`app_name`** to set the application namespace:

```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Now change your **`polls/index.html`** template from:

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

to point at the namespaced detail view:

```html
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

```html
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
<fieldset>
    <legend><h1>{{ question.question_text }}</h1></legend>
    {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
    {% for choice in question.choice_set.all %}
        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
        <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
    {% endfor %}
</fieldset>
<input type="submit" value="Vote">
</form>
```

A quick rundown:

- The above template displays a radio button for each question choice. The **`value`** of each radio button is the associated question choice’s ID. The **`name`** of each radio button is **`"choice"`**. That means, when somebody selects one of the radio buttons and submits the form, it’ll send the POST data **`choice=#`** where # is the ID of the selected choice. This is the basic concept of HTML forms.
- We set the form’s **`action`** to **`{% url 'polls:vote' question.id %}`**, and we set **`method="post"`**. Using **`method="post"`** (as opposed to **`method="get"`**) is very important, because the act of submitting this form will alter data server-side. Whenever you create a form that alters data server-side, use **`method="post"`**. This tip isn’t specific to Django; it’s good web development practice in general.
- **`forloop.counter`** indicates how many times the **`[for](https://docs.djangoproject.com/en/4.0/ref/templates/builtins/#std:templatetag-for)`** tag has gone through its loop
- Since we’re creating a POST form (which can have the effect of modifying data), we need to worry about Cross Site Request Forgeries. Thankfully, you don’t have to worry too hard, because Django comes with a helpful system for protecting against it. In short, all POST forms that are targeted at internal URLs should use the **`[{% csrf_token %}](https://docs.djangoproject.com/en/4.0/ref/templates/builtins/#std:templatetag-csrf_token)`** template tag.

This code includes a few things we haven’t covered yet in this tutorial:

- **`[request.POST](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpRequest.POST)`** is a dictionary-like object that lets you access submitted data by key name. In this case, **`request.POST['choice']`** returns the ID of the selected choice, as a string. **`[request.POST](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpRequest.POST)`** values are always strings.
    
    Note that Django also provides **`[request.GET](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpRequest.GET)`** for accessing GET data in the same way – but we’re explicitly using **`[request.POST](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpRequest.POST)`** in our code, to ensure that data is only altered via a POST call.
    
- **`request.POST['choice']`** will raise **`[KeyError](https://docs.python.org/3/library/exceptions.html#KeyError)`** if **`choice`** wasn’t provided in POST data. The above code checks for **`[KeyError](https://docs.python.org/3/library/exceptions.html#KeyError)`** and redisplays the question form with an error message if **`choice`** isn’t given.
- After incrementing the choice count, the code returns an **`[HttpResponseRedirect](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponseRedirect)`** rather than a normal **`[HttpResponse](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponse)`**. **`[HttpResponseRedirect](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponseRedirect)`** takes a single argument: the URL to which the user will be redirected (see the following point for how we construct the URL in this case).
    
    As the Python comment above points out, you should always return an **`[HttpResponseRedirect](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponseRedirect)`** after successfully dealing with POST data. This tip isn’t specific to Django; it’s good web development practice in general.
    
- We are using the **`[reverse()](https://docs.djangoproject.com/en/4.0/ref/urlresolvers/#django.urls.reverse)`** function in the **`[HttpResponseRedirect](https://docs.djangoproject.com/en/4.0/ref/request-response/#django.http.HttpResponseRedirect)`** constructor in this example. This function helps avoid having to hardcode a URL in the view function. It is given the name of the view that we want to pass control to and the variable portion of the URL pattern that points to that view. In this case, using the URLconf we set up in [Tutorial 3](https://docs.djangoproject.com/en/4.0/intro/tutorial03/), this **`[reverse()](https://docs.djangoproject.com/en/4.0/ref/urlresolvers/#django.urls.reverse)`** call will return a string like

**Amend URLconf**

```python
# polls/urls.py

from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Note that the name of the matched pattern in the path strings of the second and third patterns has changed from **`<question_id>`** to **`<pk>`**.

**Amend views**

Next, we’re going to remove our old **`index`**, **`detail`**, and **`results`** views and use Django’s generic views instead. To do so, open the **`polls/views.py`** file and change it like so:

```python
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]

class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'

def vote(request, question_id):
    ... # same as above, no changes needed.
```

We’re using two generic views here: **`[ListView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.list.ListView)`** and **`[DetailView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.detail.DetailView)`**. Respectively, those two views abstract the concepts of “display a list of objects” and “display a detail page for a particular type of object.”

- Each generic view needs to know what model it will be acting upon. This is provided using the **`model`** attribute.
- The **`[DetailView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.detail.DetailView)`** generic view expects the primary key value captured from the URL to be called **`"pk"`**, so we’ve changed **`question_id`** to **`pk`** for the generic views.

By default, the **`[DetailView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.detail.DetailView)`** generic view uses a template called **`<app name>/<model name>_detail.html`**. In our case, it would use the template **`"polls/question_detail.html"`**. The **`template_name`** attribute is used to tell Django to use a specific template name instead of the autogenerated default template name. We also specify the **`template_name`** for the **`results`** list view – this ensures that the results view and the detail view have a different appearance when rendered, even though they’re both a **`[DetailView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.detail.DetailView)`** behind the scenes.

Similarly, the **`[ListView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.list.ListView)`** generic view uses a default template called **`<app name>/<model name>_list.html`**; we use **`template_name`** to tell **`[ListView](https://docs.djangoproject.com/en/4.0/ref/class-based-views/generic-display/#django.views.generic.list.ListView)`** to use our existing **`"polls/index.html"`** template.

In previous parts of the tutorial, the templates have been provided with a context that contains the **`question`** and **`latest_question_list`** context variables. For **`DetailView`** the **`question`** variable is provided automatically – since we’re using a Django model (**`Question`**), Django is able to determine an appropriate name for the context variable. However, for ListView, the automatically generated context variable is **`question_list`**. To override this we provide the **`context_object_name`** attribute, specifying that we want to use **`latest_question_list`** instead. As an alternative approach, you could change your templates to match the new default context variables – but it’s a lot easier to tell Django to use the variable you want.

Run the server, and use your new polling app based on generic views.

For full details on generic views, see the [generic views documentation](https://docs.djangoproject.com/en/4.0/topics/class-based-views/).

**Running tests**

```python
python manage.py test polls
```

What happened is this:

- **`manage.py test polls`** looked for tests in the **`polls`** application
- it found a subclass of the **`[django.test.TestCase](https://docs.djangoproject.com/en/4.0/topics/testing/tools/#django.test.TestCase)`** class
- it created a special database for the purpose of testing
- it looked for test methods - ones whose names begin with **`test`**
- in **`test_was_published_recently_with_future_question`** it created a **`Question`** instance whose **`pub_date`** field is 30 days in the future
- … and using the **`assertIs()`** method, it discovered that its **`was_published_recently()`** returns **`True`**, though we wanted it to return **`False`**

The test informs us which test failed and even the line on which the failure occurred.

**The Django test client**

We will start again with the **`[shell](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-shell)`**, where we need to do a couple of things that won’t be necessary in **`tests.py`**. The first is to set up the test environment in the **`[shell](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-shell)`**:

```python
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

**`[setup_test_environment()](https://docs.djangoproject.com/en/4.0/topics/testing/advanced/#django.test.utils.setup_test_environment)`** installs a template renderer which will allow us to examine some additional attributes on responses such as **`response.context`** that otherwise wouldn’t be available. Note that this method *does not* set up a test database, so the following will be run against the existing database and the output may differ slightly depending on what questions you already created. You might get unexpected results if your **`TIME_ZONE`** in **`settings.py`** isn’t correct. If you don’t remember setting it earlier, check it before continuing.

Next we need to import the test client class (later in **`tests.py`** we will use the **`[django.test.TestCase](https://docs.djangoproject.com/en/4.0/topics/testing/tools/#django.test.TestCase)`** class, which comes with its own client, so this won’t be required):

```python
>>> from django.test import Client
>>> # create an instance of the client for our use
>>> client = Client()
>>> # get a response from '/'
>>> response = client.get('/')
Not Found: /
>>> # we should expect a 404 from that address; if you instead see an
>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably
>>> # omitted the setup_test_environment() call described earlier.
>>> response.status_code
404
>>> # on the other hand we should expect to find something at '/polls/'
>>> # we'll use 'reverse()' rather than a hardcoded URL
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#x27;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context['latest_question_list']
<QuerySet [<Question: What's up?>]>
```

**Testing our new view**

Now you can satisfy yourself that this behaves as expected by firing up **`runserver`**, loading the site in your browser, creating **`Questions`** with dates in the past and future, and checking that only those that have been published are listed. You don’t want to have to do that *every single time you make any change that might affect this* - so let’s also create a test, based on our **`[shell](https://docs.djangoproject.com/en/4.0/ref/django-admin/#django-admin-shell)`** session above.

Add the following to **`polls/tests.py`**:

```python
from django.urls import reverse
....

def create_question(question_text, days):
    """
    Create a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)

class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        If no questions exist, an appropriate message is displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_past_question(self):
        """
        Questions with a pub_date in the past are displayed on the
        index page.
        """
        question = create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            [question],
        )

    def test_future_question(self):
        """
        Questions with a pub_date in the future aren't displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        are displayed.
        """
        question = create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            [question],
        )

    def test_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        question1 = create_question(question_text="Past question 1.", days=-30)
        question2 = create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            [question2, question1],
        )
```

Let’s look at some of these more closely.

First is a question shortcut function, **`create_question`**, to take some repetition out of the process of creating questions.

**`test_no_questions`** doesn’t create any questions, but checks the message: “No polls are available.” and verifies the **`latest_question_list`** is empty. Note that the **`[django.test.TestCase](https://docs.djangoproject.com/en/4.0/topics/testing/tools/#django.test.TestCase)`** class provides some additional assertion methods. In these examples, we use **`[assertContains()](https://docs.djangoproject.com/en/4.0/topics/testing/tools/#django.test.SimpleTestCase.assertContains)`** and **`[assertQuerysetEqual()](https://docs.djangoproject.com/en/4.0/topics/testing/tools/#django.test.TransactionTestCase.assertQuerysetEqual)`**.

In **`test_past_question`**, we create a question and verify that it appears in the list.

In **`test_future_question`**, we create a question with a **`pub_date`** in the future. The database is reset for each test method, so the first question is no longer there, and so again the index shouldn’t have any questions in it.

And so on. In effect, we are using the tests to tell a story of admin input and user experience on the site, and checking that at every state and for every new change in the state of the system, the expected results are published.

**Customize your *app’s* look and feel**

Within the **`static`** directory you have just created, create another directory called **`polls`** and within that create a file called **`style.css`**. In other words, your stylesheet should be at **`polls/static/polls/style.css`**. Because of how the **`AppDirectoriesFinder`** staticfile finder works, you can refer to this static file in Django as **`polls/style.css`**, similar to how you reference the path for templates.

> **Static file namespacing**                                                                                         
Just like templates, we *might* be able to get away with putting our static files directly in **`polls/static`** (rather than creating another **`polls`** subdirectory), but it would actually be a bad idea. Django will choose the first static file it finds whose name matches, and if you had a static file with the same name in a *different* application, Django would be unable to distinguish between them. We need to be able to point Django at the right one, and the best way to ensure this is by *namespacing* them. That is, by putting those static files inside *another* directory named for the application itself.
> 

Put the following code in that stylesheet (**`polls/static/polls/style.css`**):

```css
li a {
    color: green;
}
```

Next, add the following at the top of **`polls/templates/polls/index.html`**:

```css
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```

The **`{% static %}`** template tag generates the absolute URL of static files.

Adding a background-image

Next, we’ll create a subdirectory for images. Create an **`images`** subdirectory in the **`polls/static/polls/`** directory. Inside this directory, put an image called **`background.gif`**. In other words, put your image in **`polls/static/polls/images/background.gif`**.

```css
body {
    background: white url("images/background.gif") no-repeat;
}
```

> **Warning**
The **`{% static %}`** template tag is not available for use in static files which aren’t generated by Django, like your stylesheet. You should always use **relative paths** to link your static files between each other, because then you can change **`[STATIC_URL](https://docs.djangoproject.com/en/4.0/ref/settings/#std:setting-STATIC_URL)`** (used by the **`[static](https://docs.djangoproject.com/en/4.0/ref/templates/builtins/#std:templatetag-static)`** template tag to generate its URLs) without having to modify a bunch of paths in your static files as well.
> 

## How to write reusable apps

> **Package? App?**
A Python [package](https://docs.python.org/3/glossary.html#term-package) provides a way of grouping related Python code for easy reuse. A package contains one or more files of Python code (also known as “modules”).
A package can be imported with **`import foo.bar`** or **`from foo import bar`**. For a directory (like **`polls`**) to form a package, it must contain a special file **`__init__.py`**, even if this file is empty.
A Django *application* is a Python package that is specifically intended for use in a Django project. An application may use common Django conventions, such as having **`models`**, **`tests`**, **`urls`**, and **`views`** submodules.
Later on we use the term *packaging* to describe the process of making a Python package easy for others to install. It can be a little confusing, we know.
>