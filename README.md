# docker-laravel-swoole

Deployment Docker image for Laravel Octane (Swoole)
适用于部署 Laravel Octane (Swoole) 的 docker 镜像

### Useage

#### Configure Laravel Octane
1. Follow the [official documentation](https://laravel.com/docs/10.x/octane) to configure Laravel Octane

```
$ composer require laravel/octane
```
2. Run Octane install commend and choose "swoole"
```
$ php artisan octane:install

Which application server you would like to use?:
  [0] roadrunner
  [1] swoole
 > 1 
 
  INFO  Octane installed successfully.
```
3. If you didn't install swoole on host machine, it may says: 
```
 WARN  The Swoole extension is missing.
```
You can ignore this warning and use "serve" commend to develop.  


#### Create Dockerfile for Deployment

```
# Dockerfile

FROM xxutianyi/laravel-swoole:latest

COPY . /var/www/html
RUN cd /var/www/html \
    && composer install --optimize-autoloader --no-dev \
    && php artisan route:cache

RUN chmod 777 /var/www/html -R

```
Then build your app

```
$ docker build -t your-app-name:latest .
```

#### Run use docker compose on production machine

```
# docker-compose.yml

version: "3"
services:
    app:
        image: "your-app-name:latest"
        extra_hosts:
            - "host.docker.internal:host-gateway"
        ports:
            - "${APP_PORT:-8000}:8000"
            - "${VITE_PORT:-5173}:${VITE_PORT:-5173}"
        env_file:
            - ./.env
        environment:
            LARAVEL_SAIL: 1
            XDEBUG_MODE: "${SAIL_XDEBUG_MODE:-off}"
            XDEBUG_CONFIG: "${SAIL_XDEBUG_CONFIG:-client_host=host.docker.internal}"
        networks:
            - sail  
    ...
    
networks:
    sail:
        driver: bridge

    ...
```

and run it

```
$ docker compose up
```
