1. Download:  
    - https://nginx.org/ru/download.html
    - https://windows.php.net/download#php-8.0
    - https://pecl.php.net/package/mongodb
    - https://www.mongodb.com/try/download/community
    - https://redmine.lighttpd.net/attachments/660/RunHiddenConsole.zip
    - https://shopker.org/catalog/shopker-info
2. Unpack nginx to a folder "C:\nginx".
3. Unpack PHP to a folder "C:\nginx\php".
4. Move the file "php_mongodb.dll" to the folder "C:\nginx\php\ext". Open "C:\nginx\php\php.ini" in a text editor and add the following line in the "Dynamic Extensions" block:
    ~~~
    extension=mongodb
    ~~~
5. Move the file "RunHiddenConsole.exe" to the folder "C:\nginx".
6. Create files in the "C:\nginx" folder:

    start.cmd
    ~~~
    @echo off
    echo Starting servers...
    set PHP_FCGI_MAX_REQUESTS=0
    set SRVPATH=C:\nginx
    start /D%SRVPATH% nginx.exe
    %SRVPATH%\RunHiddenConsole.exe %SRVPATH%\php\php-cgi.exe -b 127.0.0.1:9000 -c %SRVPATH%/php/php.ini
    ~~~

    restart.cmd
    ~~~
    @echo off
    echo Shutting down servers...
    taskkill /IM nginx.exe /F
    taskkill /IM php-cgi.exe /F
    echo Starting servers...
    set PHP_FCGI_MAX_REQUESTS=0
    set SRVPATH=C:\nginx
    start /D%SRVPATH% nginx.exe
    %SRVPATH%\RunHiddenConsole.exe %SRVPATH%\php\php-cgi.exe -b 127.0.0.1:9000 -c %SRVPATH%/php/php.ini
    ~~~

    shutdown.cmd
    ~~~
    @echo off
    echo Shutting down servers...
    taskkill /IM nginx.exe /F
    taskkill /IM php-cgi.exe /F
    ~~~
7. Install **MongoDB** and **MongoDBCompass**.
8. Unpack "shopker-4.x.x" to the folder "C:\www\shopker".
9. Launch Notepad with Administrator rights. Open the file "C:\Windows\System32\drivers\etc\hosts" and add the line:
    ~~~
    127.0.0.1       shopker.loc
    ~~~
10. Open the file "C:\nginx\conf\nginx.conf" in a text editor and uncomment (remove the "#") some lines so that it looks like this:
    ~~~
    location / {
        root   "C:\www";
        index  index.html index.htm index.php;
    }
    ...
    location ~ \.php$ {
        root           "C:\www";
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
    ~~~
    In the "http" section below, add:
    ~~~
    server {
        listen 80;
        server_name shopker.loc;
        root "C:\www\shopker\public";
        index index.html index.php;
        
        location / {
            try_files $uri /index.php$is_args$args;
        }

	location ~ ^/(index|check)\.php(/|$) {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_split_path_info ^(.+\.php)(/.*)$;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            fastcgi_param DOCUMENT_ROOT $realpath_root;
            internal;
        }

        error_log "logs\shopker_error.log";
        access_log "logs\shopker_access.log";
    }
    ~~~
11. Done. Now you can start "start.cmd" by double clicking. Open the address "http://shopker.loc/" in your browser and continue installing Shopker. In the "Database name" field, enter "shopker" (or any other), leave the database connection fields (Username, Password, URI for connection) empty. Enter Email Address administrator email and password. Press the "Install" button.
