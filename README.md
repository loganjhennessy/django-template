# Django Template

## Setup

### Virtualenv Setup

```
mkvirtualenv django-template
pip install django
```

### Django Setup

```
django-admin startproject template
mv template django-template
cd django-template
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
gcloud kms keyrings create django-template --location global
gcloud kms keys create django-template-key --location global --keyring django-template --purpose encryption
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
sops --encrypt --gcp-kms projects/resilient-tech/locations/global/keyRings/django-template/cryptoKeys/django-template-key prod.env > prod.sops.env
```

## Git Setup

```
git init
```

```
# .gitignore
__pycache__/

*.env
```
