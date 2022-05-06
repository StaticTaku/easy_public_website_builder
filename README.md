# Template for django + nginx + mysql web application docker repository

## How To Use

### 1.
Set environment variable for mysql in mysql_enviroonment.env
dont change variable name, just change values of variables

### 2.
If you want to change public port of nginx, just change like this:

in docker-compose.yml
```sh
ports:
        - "{port number you want to use}:8000"
```

### 3.
```sh
docker-compose run python django-admin.py startproject app .
```

### 4.
If you want to write/execute file under src directory outside the python container, you have to change owner of all files under /src
```sh
sudo chown -R {user name}:{group name} ./src
```

### 5.
Change DATABASE in src/app/settings.py
```sh
import os

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': os.environ.get("MYSQL_DATABASE"),
        'USER': os.environ.get("MYSQL_USER"),
        'PASSWORD': os.environ.get("MYSQL_PASSWORD"),
        'HOST': 'db',
        'PORT': '3306',
    }
}
```

### 6.
Add STATIC_ROOT in src/app/settings.py
```sh
STATIC_ROOT = '/static'
```

### 7.
```sh
docker-compose run python ./manage.py collectstatic
```

### 8.
```sh
docker-compose run python ./manage.py makemigrations

docker-compose run python ./manage.py migrate
```

### 9.
```sh
docker-compose up -d
```
and, you can access your web site by http://localhost:{port number you chose} and http://localhost:{port number you chose}/admin
if you dont change pubilc port, you can just access your web site by http://localhost:8000 and http://localhost:8000/admin
