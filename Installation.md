Shopkeeper4 application can be installed on a common shared-hosting or VDS. While it is quite difficult to find shared hosting with MongoDB, but they are. You can also use a remote MongoDB database server, for example [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).

System requirements (Symfony 4.x)
----------------------------------
- PHP 7.1+
- MongoDB 3.2+
- PHP extensions: mongodb, json, gd2, iconv

Installation Procedure
-----------------
1. Download ZIP archive from official site [https://shopker.org/](https://shopker.org/) and upload it to the server in the root directory of the site (not public).
2. Unpack the archive. To do this, you can use the file manager, which is usually in the hosting panel.
3. Check [web server settings](https://symfony.com/doc/current/setup/web_server_configuration.html). On a shared hosting, you usually do not need to configure the web server, the settings should already be made. If you can not configure Nginx or Apache, you need to contact the hosting support service.

Usually on shared hosting it’s enough to specify the path to the public folder ``public`` in the domain settings or rename this folder (give the name specified in the domain settings). If the name of the public folder is changed, then you need to open the configuration file ``/config/parameters.yaml`` and edit the folder name in the parameter ``app.web_dir_path``.

4. Open the website address in the browser. Fill in the fields on the install page and click the "Install" button.

5. If all data is entered correctly, then everything necessary will be added to the database, as well as store test data.

For installation on a dedicated server or VDS, for convenience, you can use the [Bash script for installation](https://github.com/andchir/shk4-wiki/blob/master/Bash-script-for-installation.md). It will download the archive of the application, unpack it and set the necessary permissions on the files and folders.

Additional materials
------------------------
- [Web server settings for symfony](https://symfony.com/doc/current/setup/web_server_configuration.html)
