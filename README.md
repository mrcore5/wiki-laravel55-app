# Mrcore Wiki Website Application

Full Laravel 5.5 application serving the Mrcore Wiki application


# Installation




# Nginx
```
cat > /etc/nginx/sites-available/mrcore.wiki << "EOF"
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
EOF
```



# How this was Built
* Fresh Laravel 5.5
* Remove `database/migrations/*`
* Remove `app/User.php`

* ???Edit `app/Http/Kernel.php` and comment out the `VerifyCsrfToken`

* Modify `composer.json` with proper mrcore wiki dependencies
* Edit `config/app.php` and add `Mrcore\Foundation\Providers\FoundationServiceProvider::class` and comment out the Laravel app ones
* Add `mrcore/foundation` asset manager manually to both `public/index.php` and `artisan` (can't use bootstrap/app.php, too late for function overrides)

* Publish default `config/modules.php` with `php artisan vendor:publish --tag mrcore.modules.configs`
* Edit modules.php config and enable `auth` and `basetheme`, test site, then enable `wiki` and `parser` (site wont work yet)
* Edit your `config/app.php` and set timezone to `America/Chicago` or whatever your timezone is
* Edit your `config/auth.php` and set the `guards web 'driver' => 'mrcore'` and the `providers users 'model' => Mrcore\Auth\Models\User::class`
* Visit all `config/*` and add `env()` as needed and double check from Dynatron's last version
* Edit `.env` to Dynatron standards
* Copy over `.env.example`, `.gitignore` etc...
* Run new auth and wiki migrations and seeders
```
./artisan mrcore:auth:app db:migrate
./artisan mrcore:wiki:app db:migrate
./artisan mrcore:auth:app db:seed
./artisan mrcore:wiki:app db:seed
```
* VISIT NEW WIKI! (admin / password)
* Now add all the dynatron specific stuff back, you know what to do from here!

