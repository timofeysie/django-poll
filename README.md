# MySite

[Created following the official tutorial](https://docs.djangoproject.com/en/4.2/intro/tutorial01/).

## Workflow

```bash
python manage.py runserver
```

Site is served at: http://127.0.0.1:8000/

## The polls app

### Step 1

Add a new app:

python manage.py startapp polls

Update the mysitepolls/views.py with a response.

Create a new file polls.urls.py and unclude url patterns.

Update mysite/urls.py to add a path to the polls url.
