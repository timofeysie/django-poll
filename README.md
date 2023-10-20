# MySite

[Created following the official tutorial](https://docs.djangoproject.com/en/4.2/intro/tutorial01/).

## Workflow

```bash
python manage.py runserver
python manage.py migrate
python manage.py makemigrations polls
python manage.py shell
python manage.py createsuperuser
```

Site is served at: http://127.0.0.1:8000/

## The polls app

From the "Writing your first Django app" article, these are notes from the multi step tutorial.

### Step 1

Add a new app:

python manage.py startapp polls

Update the mysitepolls/views.py with a response.

Create a new file polls.urls.py and unclude url patterns.

Update mysite/urls.py to add a path to the polls url.

### Step 2

[Part 2](https://docs.djangoproject.com/en/4.2/intro/tutorial02/) of the official Django tutorial.

To use some of the default apps we need at least one database table.

```bash
python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK
```

Create two models in from django.db import models in polls/models.py

```py
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0): Question and Choice.
```

Add a reference to its configuration class in the INSTALLED_APPS setting in mysite/settings.py.

```py
INSTALLED_APPS = [
    "polls.apps.PollsConfig", # <---
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Run the migrations command:

```bash
python manage.py makemigrations polls
Migrations for 'polls':
  polls\migrations\0001_initial.py
    - Create model Question
    - Create model Choice
```

Run migrate again to create those model tables in your database:

```py
python manage.py migrate
```

invoke the Python interactive Python shell:

```py
python manage.py shell
```

Add

## Register models

In polls/admin.py add the question:

```py
polls/admin.py¶
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

Next, create user, answer the questions and run the server:

```py
python manage.py createsuperuser
python manage.py runserver
```

Goto: http://127.0.0.1:8000/admin/

You can login and see the form which was automatically generated from the Question model.
