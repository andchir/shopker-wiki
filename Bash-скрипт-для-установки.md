Bash скрипт для установки приложения на сервере.

Создать файл "shk4-install.sh" с таким содержимым:
~~~
#!/bin/bash

DIR="$(pwd)"
DOWNLOAD_PATH="https://modx-shopkeeper.ru/assets/files/shk4/"
DOWNLOAD_FILENAME="shk4-latest.zip"
OWNER_USER="www-data"
OWNER_GROUP="www-data"

echo ""
echo -e "\\e[94m\\e[1m=== УСТАНОВКА SHOPKEEPER 4 ===\\e[0m"
echo ""
read -n1 -r -p "Нажмите клавишу ENTER для продолжения..." key
echo ""

if [ "$key" = '' ]; then

    echo -e "\\e[2m...Скачивание архива...\\e[0m"
    echo ""
    
    if wget $DOWNLOAD_PATH$DOWNLOAD_FILENAME >> "$DIR"/shk4-install.log 2>> "$DIR"/shk4-install.log; then
        echo -e "\\e[32mСкачивание прошло успешно!\\e[0m"
        echo ""
    else
        echo -e "\\e[31mERROR: Невозможно скачать файл.\\e[0m"
        echo ""
        exit 1
    fi
    
    echo -e "\\e[2m...Распаковка архива в текущую папку...\\e[0m"
    echo ""
    
    if unzip $DOWNLOAD_FILENAME >> "$DIR"/shk4-install.log 2>> "$DIR"/shk4-install.log; then
        echo -e "\\e[32mРаспаковка прошла успешно!\\e[0m"
        echo ""
    else
        echo -e "\\e[31mОШИБКА: Невозможно распаковать архив.\\e[0m"
        echo ""
        exit 1
    fi
    
    sudo chown -R $OWNER_USER:$OWNER_GROUP "$DIR"
    sudo find "$DIR" -type d -exec chmod 755 {} \;
    sudo find "$DIR" -type f -exec chmod 644 {} \;
    
    echo -e "\\e[32mТеперь Вы можете открыть сайт в браузере и продолжить установку.\\e[0"
    echo ""

else
    echo ""
    echo -e "\\e[31mОТМЕНЕНО"
    echo ""
fi
~~~

Проверить и отредактировать URL скачивания файла и имя пользователя, при необходимости.

Загрузить файл на сервер в папку, в которой Вы хотите установить приложение.

Установить права на исполнение: ``sudo chmod +x shk4-install.sh``.

Запустить скрипт: ``./shk4-install.sh``.
