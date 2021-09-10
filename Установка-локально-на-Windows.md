1. Скачать:  
    - https://nginx.org/ru/download.html
    - https://windows.php.net/download#php-8.0
    - https://pecl.php.net/package/mongodb
    - https://www.mongodb.com/try/download/community
    - https://redmine.lighttpd.net/attachments/660/RunHiddenConsole.zip
    - https://shopker.org/catalog/shopker-info
2. Разархивировать nginx в папку "C:\nginx".
3. Разархивировать PHP в папку "C:\nginx\php".
4. Переместить файл "php_mongodb.dll" в папку "C:\nginx\php\ext". Открыть в текстовом редакторе "C:\nginx\php\php.ini" и в блоке "Dynamic Extensions" добавить строку:
    ~~~
    extension=mongodb
    ~~~
5. Переместить файл "RunHiddenConsole.exe" в папку "C:\nginx".
6. В папке "C:\nginx" создать файлы:

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
7. Установить **MongoDB** и **MongoDBCompass**.
8. Разархивировать "shopker-4.x.x" в папку "C:\www".
9. Запустить Блокнот с правами Администратора. Открыть файл "C:\Windows\System32\drivers\etc\hosts" и добавить строку:
    ~~~
    127.0.0.1       shopker.loc
    ~~~
10. Открыть в текстовом редакторе файл "C:\nginx\conf\nginx.conf" и раскомментировать (убрать "#") некоторые строки, чтобы получилось так:
    ~~~
    location / {
        root   "C:\www";
        index  index.html index.htm;
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
    В секции "http" внизу добавить:
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
11. Готово. Теперь можно запустить "start.cmd" двойным кликом. Открыть в браузере адрес "http://shopker.loc/" и продолжить установку Shopker. В поле "Имя базы данных" ввести "shopker" (или любое другое), поля подключения БД (Имя пользователя, Пароль, URI для подключения) оставить пустыми. Ввести Адрес эл. почты администратора и пароль. Нажать кнопку "Установить".
