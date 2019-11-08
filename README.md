# Django Template

## Setup

```
export PROJECT_NAME=<your-project-name>
```

### Virtualenv Setup

```
mkvirtualenv $PROJECT_NAME
pip install django
```

### Django Setup

```
django-admin startproject template
mv template $PROJECT_NAME
cd $PROJECT_NAME
```

```
# requirements.txt
django==2.2.6
```

### SOPS Setup

```
gcloud config configurations activate resilient-tech
gcloud auth login
gcloud auth application-default login
gcloud kms keyrings create $PROJECT_NAME --location global
gcloud kms keys create $PROJECT_NAME-key --location global --keyring $PROJECT_NAME --purpose encryption
```

Hide the secret key.

```
# template/settings.py
# ...
SECRET_KEY = os.environ.get('SECRET_KEY', None)
# ...
```

```
# prod.env
SECRET_KEY=<super-secret-key>
```

Now encrypt it.

```
sops --encrypt --gcp-kms projects/resilient-tech/locations/global/keyRings/$PROJECT_NAME/cryptoKeys/$PROJECT_NAME-key prod.env > prod.sops.env
```

### Git Setup

```
git init
```

```
# .gitignore
__pycache__/

db.sqlite3

*.env
```

```
git add .
git commit -m 'Initial commit'
git add origin git@github.com:loganjhennessy/$PROJECT_NAME.git
git push -u origin master
```

## Basic app

```
python manage.py startapp app
```

### Create the view

```
# app/views.py
from django.shortcuts import HttpResponse

def index(request):
    return HttpResponse("Hello, world!")
```

### Wire it up

```
# app/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

```
# template/urls.py
from django.urls import include, path

urlpatterns = [
    path('', include('template.urls')),
]
```

## Checkpoint

Check to make sure everything is working so far:

```
python manage.py runserver
```

You should see the "Hello, world!" at localhost:8000.
