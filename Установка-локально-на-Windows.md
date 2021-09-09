1. Скачать:  
  - https://nginx.org/ru/download.html
  - https://windows.php.net/download#php-8.0
  - https://pecl.php.net/package/mongodb
  - https://www.mongodb.com/try/download/community
  - https://redmine.lighttpd.net/attachments/660/RunHiddenConsole.zip
  - https://shopker.org/catalog/shopker-info
2. Разархивировать nging в папку "C:\nginx".
3. Разархивировать PHP в папку "C:\nginx\php".
4. Переместить файл "php_mongodb.dll" в папку "C:\nginx\php\ext".
5. Переместить файл "RunHiddenConsole.exe" в папку "C:\nginx".
6. Создать файлы:

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

