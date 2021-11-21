Example configuration for Nginx:

~~~
server {
    listen 80;

    server_name example.com;
    root /var/www/shopker/public;
    
    client_max_body_size 250m;

    location / {
        try_files $uri /index.php$is_args$args;
    }

    location ~ ^/(index|check)\.php(/|$) {
        fastcgi_pass unix:/run/php/php7.3-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        internal;
    }
    
    location ~ \.php$ {
        return 404;
    }
    
    location = /favicon.ico {
        log_not_found off;
        access_log off;
    }

    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    location ~ /\. {
        deny all;
    }

    error_log /var/log/nginx/shopker_error.log;
    access_log /var/log/nginx/shopker_access.log;
}
~~~
где ``example.com`` - ваше доменное имя.
Для редиректа с субдомена ``www`` добавить в самом верху ещё один блок "server":
~~~
server {
    server_name www.example.com;
    return 301 $scheme://example.com$request_uri;
}
~~~

Пример содержимого файла ``.htaccess`` для сервера Apache:
~~~
<IfModule mod_rewrite.c>
    Options -MultiViews
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^(.*)$ index.php [QSA,L]
</IfModule>

<IfModule !mod_rewrite.c>
    <IfModule mod_alias.c>
        RedirectMatch 302 ^/$ /index.php/
    </IfModule>
</IfModule>
~~~

Файл ``.htaccess`` должен находиться в папке ``public``.

Дополнительные материалы
------------------------
- [Настройки веб-сервера для Symfony](https://symfony.com/doc/current/setup/web_server_configuration.html)

