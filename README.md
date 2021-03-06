# Mrcore Wiki Website Application

Full Laravel 5.5 application serving the Mrcore Wiki application


# Installation

* `cd /var/www`
* `git clone https://github.com/mrcore5/wiki-laravel55-app wiki`
* `cd wiki`
* `composer install`
* `cp .env.example .env`
* `./artisan key:generate`
* Edit .env to your environment
* Setup of MySQL, Nginx and PHP-FPM are up to you, some helpers below
* Once MySQL and Nginx are up, you can migrate and seed the modules
```
./artisan mrcore:auth:app db:migrate
./artisan mrcore:wiki:app db:migrate
./artisan mrcore:auth:app db:seed
./artisan mrcore:wiki:app db:seed
```
* Now visit your local site, default user/pass is `admin / password`




# Nginx
```
server {
    listen 80;
    server_name vfi.sandbox;
    root /var/www/wiki/public/;
    index index.php;
    charset utf-8;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }
    access_log off;
    error_log off;
    sendfile off;
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
    }
    location ~ /\.ht {
        deny all;
    }
}
```

# MySQL

Create user `mrcore` with full access to database `wiki`
* `CREATE DATABASE wiki`
* `CREATE USER 'mrcore'@'%' IDENTIFIED BY 'techie'; GRAND ALL PRIVILEGES on wiki.* to 'mrcore'@'%';`




# How this was Built
* Fresh Laravel 5.5
* Remove `database/migrations/*`
* Remove `app/User.php`
* Modify `composer.json` with proper mrcore wiki dependencies
* Edit `config/app.php` and add `Mrcore\Foundation\Providers\FoundationServiceProvider::class` and comment out the Laravel app ones
* Add `mrcore/foundation` asset manager manually to both `public/index.php` and `artisan` (can't use bootstrap/app.php, too late for function overrides)
* Publish default `config/modules.php` with `php artisan vendor:publish --tag mrcore.modules.configs`
* Edit modules.php config and enable `auth` and `basetheme`, test site, then enable `wiki` and `parser` (site wont work yet)
* Edit your `config/app.php` and set timezone to `America/Chicago` or whatever your timezone is
* Edit your `config/auth.php` and set the `guards web 'driver' => 'mrcore'` and the `providers users 'model' => Mrcore\Auth\Models\User::class`

