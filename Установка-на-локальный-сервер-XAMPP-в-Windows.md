1. Скачать и установить XAMPP: [https://www.apachefriends.org/ru/index.html](https://www.apachefriends.org/ru/index.html)

2. Скачать и установить MongoDB Community Server: [https://www.mongodb.com/download-center/community](https://www.mongodb.com/download-center/community)

3. Скачать и установить MongoDB Compass: [https://www.mongodb.com/download-center/compass](https://www.mongodb.com/download-center/compass)

4. Скачать PHP MongoDB расширение: [https://pecl.php.net/package/mongodb/1.7.2/windows](https://pecl.php.net/package/mongodb/1.7.2/windows)  
Например для 64-битного процессора и PHP 7.4:  
[https://windows.php.net/downloads/pecl/releases/mongodb/1.7.2/php_mongodb-1.7.2-7.4-ts-vc15-x64.zip](https://windows.php.net/downloads/pecl/releases/mongodb/1.7.2/php_mongodb-1.7.2-7.4-ts-vc15-x64.zip)

5. В папке ``C:\xampp\apache\conf`` открыть в блокноте файл **httpd.conf**.
Добавить внизу:
~~~
<Directory "C:/xampp/apps">
    Options Indexes FollowSymLinks Includes ExecCGI
    AllowOverride All
    Require all granted
</Directory>
~~~

6. В папке ``C:\xampp\apache\conf\extra`` открыть в блокноте файл **httpd-vhosts.conf**
Добавить внизу:
~~~
<VirtualHost *:80>
       DocumentRoot "C:/xampp/htdocs/"
       ServerName localhost
</VirtualHost>
<VirtualHost *:80>
       DocumentRoot "C:\xampp\apps\shopker\public"
       ServerName shopker.localhost
</VirtualHost>
~~~

7. Открыть блокнот от имени администратора. В файл ``C:\windows\system32\drivers\etc\hosts``
добавить строку:
~~~
127.0.0.1 shopker.localhost
~~~
Сохранить.

7. Открыть папку ``C:\xampp\php\ext``
Скопировать в неё файл **php_mongodb.dll** из архива (php_mongodb-1.7.2-7.4-ts-vc15-x64.zip).

8. Открыть в блокноте файл ``C:\xampp\php\php.ini``
Найти раздел "Dynamic Extensions", в самом низу этого раздела добавить строку:
~~~
extension=mongodb
~~~

9. Создать папку по адресу: ``C:\xampp\apps\shopker``  
и загрузить в неё файлы приложения **Shopker**.

10. Запустить XAMPP ``C:\xampp\xampp-control.exe``  
Проверить, чтобы Apache был запущен.

11. Создать папки ``C:\data\db``  
Нажать кнопку "Shell" и вставить команду запуска **MongoDB**:
~~~
"C:\Program Files\MongoDB\Server\4.2\bin\mongod.exe" --dbpath="C:\data\db"
~~~
Запустить команду нажатием клавиши Enter на клавиатуре.

12. Открыть в браузере [http://shopker.localhost/](http://shopker.localhost/)  
Если конфигурационный файл редактировался после открытия этого адреса, то нужно очистить кэш:  
Удалить папку ``C:\xampp\apps\shopker\var\cache``

13. Откроется установщик приложения **Shopker**.  
Переключить язык на русский. Заполнить поля: **Адрес эл. почты администратора**, **Пароль**, **Подтвердите пароль**.  
В поле **"Имя базы данных"** ввести любое имя базы данных, например: **shopker-dev**  
В разделе **"Настройки MongoDB"** оставить пустыми поля: **Имя пользователя**, **Пароль**, **URI для подключения**.  
Нажать кнопку **"Установить"**.  
Готово.
