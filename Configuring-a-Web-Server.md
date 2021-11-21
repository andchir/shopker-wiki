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
``example.com`` - your domain name.
For a redirect from the ``www`` subdomain, add one more "server" block at the very top:
~~~
server {
    server_name www.example.com;
    return 301 $scheme://example.com$request_uri;
}
~~~

An example of the contents of a .htaccess file for an Apache server:
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

``.htaccess`` file must be in the ``public`` folder.

Additional materials
------------------------
- [Configuring a Web Server](https://symfony.com/doc/current/setup/web_server_configuration.html)

