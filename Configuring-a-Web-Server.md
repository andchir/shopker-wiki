Example configuration for Nginx:

~~~
server {
    listen 80;

    server_name shopkeeper4;
    root /var/www/shopkeeper4/public;
    
    client_max_body_size 200m;

    location / {
        try_files $uri /index.php$is_args$args;
    }

    location ~ ^/(index|check)\.php(/|$) {
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
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

    error_log /var/log/nginx/shopkeeper4_error.log;
    access_log /var/log/nginx/shopkeeper4_access.log;
}
~~~

An example of the contents of the .htaccess file for the Apache server:
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

Additional
------------------------
- [Configuring a Web Server for Symfony](https://symfony.com/doc/current/setup/web_server_configuration.html)

