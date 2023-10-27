# The Django Voting Poll App

Created following the [official tutorial](https://docs.djangoproject.com/en/4.2/intro/tutorial01/) for learning [Django](https://www.djangoproject.com/).

## Workflow

```bash
python manage.py runserver
python manage.py migrate
python manage.py makemigrations polls
python manage.py shell # Use quit() or Ctrl-Z plus Return to exit
python manage.py createsuperuser
```

Site is served at: http://127.0.0.1:8000/

 http://127.0.0.1:8000/admin/

## Routes

```txt
/polls/
/polls/34
/polls/34/results/
/polls/34/vote/
```

## The polls app

From the "Writing your first Django app" article on the [official Django docs site](https://docs.djangoproject.com/), these are notes from the four step tutorial.

I will just provide notes on the steps here for review purposes and reference.  The article itself should be used as the full guide.

## Step 1

Add a new app:

python manage.py startapp polls

Update the mysitepolls/views.py with a response.

Create a new file polls.urls.py and unclude url patterns.

Update mysite/urls.py to add a path to the polls url.

## Step 2

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
    "polls.apps.PollsConfig",
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

### Register models

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

## Step 3

Add views to polls/views.py such as this:

```py
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)
```

Add the new view to the urlpatterns array in the polls/urls.py like this:

```py
# ex: /polls/5/
path("<int:question_id>/", views.detail, name="detail"),
```

Then in polls/view.py, update the index() view to displays the latest 5 poll questions:

```py
def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    output = ", ".join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```

Django will look for templates in a directory within apps, se we create this file: polls/templates/polls/index.html

```html
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

Next update polls/views.py to use the template:

```py
from django.http import HttpResponse
from django.template import loader
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    template = loader.get_template("polls/index.html")
    context = {
        "latest_question_list": latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

### The render() shortcut

There is a render() shortcut to load a template, fill a context and return an HttpResponse object with the result of the rendered template.

```py
from django.shortcuts import render

def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    context = {"latest_question_list": latest_question_list}
    return render(request, "polls/index.html", context)
```

There is also a shortcut called get_object_or_404.  We go from this detail page:

```py
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, "polls/detail.html", {"question": question})
```

To this:

```py
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/detail.html", {"question": question})
```

This helps de-couple the model layer to the view layer.

### Template functions

This code snippet is shown to show the choices for a poll:

```html
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

That is actually the function question.choice_set.all() that returns an iterable of Choice objects and is suitable for use in the {% for %} tag.

There is a link to the [templates](https://docs.djangoproject.com/en/4.2/topics/templates/) documents also shown.

### The {% url %} template tag

Remove a reliance on specific URL paths defined in your url configurations by using the {% url %} template tag.

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

In the urls.py we can add an app name to be used to namespace the url.  Then update the url tag like this:

```html
{% url 'polls:detail' question.id %}
```
