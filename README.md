# Portfolio site

## How To Use

### 1.
Set environment variable for mysql in mysql_enviroonment.env
dont change variable name, just change values of variables
```sh
MYSQL_ROOT_PASSWORD=
MYSQL_DATABASE=
MYSQL_USER=
MYSQL_PASSWORD=
```

### 2.
If you want to change public port of nginx, just change like this:

in docker-compose.yml
```sh
ports:
        - "{port number you want to use}:{port number you want to use}"
        - "{port number you want to use}:{port number you want to use}"
```

in nginx/conf/app_nginx.conf
```sh
server {
        listen      {port number you want to use};
        listes [::] {port number you want to use}
```
### 3.
If you have domain for your server, change server name in nginx/conf/app_nginx.conf like this:
```sh
server {
        server_name {domain you want to use};
```

If you dont have it, just input your server's ip address at server_name

### 4.
```sh
docker-compose run python ./manage.py makemigrations

docker-compose run python ./manage.py migrate
```

### 5.
```sh
docker-compose up -d
```

and in src/app/settings.py, change ALLOWED_HOSTS
```sh
ALLOWED_HOSTS = ["{server_name}"]
```
and, you can access your web site by http://{servername}:{port number you chose} and http:/{servername}:{port number you chose}/admin


### 6.
To activate https,
```sh
docker compose run --rm  certbot certonly --webroot --webroot-path /var/www/certbot/ -d {server_name}
```

### 7.
in nginx/conf/app_nginx.conf, add this
```sh
server {
    listen 443 default_server ssl http2;
    listen [::]:443 ssl http2;

    ssl_certificate /etc/nginx/ssl/live/{server_name}/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/live/{server_name}/privkey.pem;
    
    server_name {server_name};
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
you can now accese your site by https

### 8.
certificates for https expire every three months, so update your certificates every month

open editor for crontab
```sh
crontab -e
```

input crontab command
```sh
0 0 1 * * docker-compose run --rm certbot renew
```
