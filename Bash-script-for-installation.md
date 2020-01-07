Bash script for installing the application on the server.

Create the file "shk4-install.sh" with the following content:
~~~
#!/bin/bash

DIR="$(pwd)"
DOWNLOAD_PATH="http://modx-shopkeeper.ru/assets/files/shk4/"
DOWNLOAD_FILENAME="shk4-latest.zip"
OWNER_USER="www-data"
OWNER_GROUP="www-data"

echo ""
echo -e "\\e[94m\\e[1m=== SHOPKEEPER 4 INSTALLER ===\\e[0m"
echo ""
read -n1 -r -p "Press ENTER to continue..." key
echo ""

if [ "$key" = '' ]; then

    echo -e "\\e[2m...Downloading archive...\\e[0m"
    echo ""
    
    if wget $DOWNLOAD_PATH$DOWNLOAD_FILENAME >> "$DIR"/shk4-install.log 2>> "$DIR"/shk4-install.log; then
        echo -e "\\e[32mDownloaded Successfully!\\e[0m"
        echo ""
    else
        echo -e "\\e[31mERROR: Unable to download archive file\\e[0m"
        echo ""
        exit 1
    fi
    
    echo -e "\\e[2m...Unpacking the archive into the current directory...\\e[0m"
    echo ""
    
    if unzip $DOWNLOAD_FILENAME >> "$DIR"/shk4-install.log 2>> "$DIR"/shk4-install.log; then
        echo -e "\\e[32mUnpacked successfully!\\e[0m"
        echo ""
    else
        echo -e "\\e[31mERROR: Unable to unpack the archive file.\\e[0m"
        echo ""
        exit 1
    fi
    
    sudo chown -R $OWNER_USER:$OWNER_GROUP "$DIR"
    sudo find "$DIR" -type d -exec chmod 755 {} \;
    sudo find "$DIR" -type f -exec chmod 644 {} \;
    
    echo -e "\\e[32mNow you can open your site in the browser and continue with the installation.\\e[0"
    echo ""

else
    echo ""
    echo -e "\\e[31mCANCELED"
    echo ""
fi
~~~

Upload the file to the server in the folder where you want to install the application.

Set the right to execute: ``sudo chmod +x shk4-install.sh``.

Run the script: ``./shk4-install.sh``.