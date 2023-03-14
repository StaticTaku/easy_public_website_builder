# easy_public_website_builder

## Installation
```sh
git clone https://github.com/StaticTaku/easy_public_website_builder.git
```

## Setup 

### 1.
Set environment variables for mysql in mysql_enviroonment.env, which will be set as environment variables in the db and python server (see docker-compose.yml if you are not sure what those servers are).

Dont change variable names, just change values. 

For some security reasons, it is better to erase what you wrote in this file after all of the setups.
```sh
MYSQL_ROOT_PASSWORD=
MYSQL_DATABASE=
MYSQL_USER=
MYSQL_PASSWORD=
```

### 2.
If you want to change the public ports of nginx, just change like this:

in docker-compose.yml
```sh
ports:
        - "80:{docker side http port number you want to use}"
        - "443:{docker side https port number you want to use}"
```

in nginx/conf/app_nginx.conf
```sh
server {
        listen      {docker side http port number you want to use};
        listes [::] {docker side http port number you want to use};
```
### 3.
If you have domain, change the server name in nginx/conf/app_nginx.conf like this:
```sh
server {
        server_name {domain you want to use};
```

If you dont have it, just input your server's ip address at the server_name.

### 4.
```sh
docker-compose run python django-admin.py startproject app . && \
sudo chowm $USER:$USER src/ static/
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

and in src/app/settings.py, change ALLOWED_HOSTS
```sh
ALLOWED_HOSTS = ["{domain you want to use}"]
```
and, you can access your web site by http://{domain you want to use} and http:/{domain you want to use}/admin.

## Activate https
### 1.
To activate https,
```sh
docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d {domain you want to use}
```

### 2.
in nginx/conf/app_nginx.conf, add this:
```sh
server {
    listen {docker side https port number you want to use} default_server ssl http2;
    listen [::]:{docker side https port number you want to use} ssl http2;

    ssl_certificate /etc/nginx/ssl/live/{domain you want to use}/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/{domain you want to use}/privkey.pem;
    
    server_name {domain you want to use};
    charset     utf-8;

    location /static {
        alias /static;
    }

    location / {
        uwsgi_pass  django;
        include     /etc/nginx/uwsgi_params;
    }
}
```

and
```sh
docker-compose restart
```
You can now accese your site by https://{domain you want to use} and https:/{domain you want to use}/admin.


## Frequently used commands
```sh
#start <command> in <service name> container
docker-compose exec -it <service name> <command>
#ex.
docker-compose exec -it python bash

#list all containers in compose project
docker-compose ps -a

#start container
#ex.
docker-compose start db

#stop container
#ex.
docker-compose start python

#make image
docker-compose build

#create container from image
docker-compose create
```
