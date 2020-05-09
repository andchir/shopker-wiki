### Ubuntu 18.04.3 LTS

### Первоначальная настройка

Войти на сервер по SSH:
~~~
ssh root@111.222.333.444
~~~
111.222.333.444 - пример IP-адреса сервера

Посмотреть список доступных локалей:
~~~
locale -a | grep ru
~~~
Сгенерировать русскую локаль:
~~~
locale-gen ru_RU.UTF-8
~~~
Другой вариант установки нужных локалей:
~~~
dpkg-reconfigure locales
~~~

Обновить пакеты:
~~~
apt update && apt upgrade
~~~

Выбрать часовой пояс:
~~~
dpkg-reconfigure tzdata
~~~

Установить пакеты, которые пригодятся:
~~~
apt install software-properties-common wget nano unzip
~~~

### Добавляем нового супер-пользователя

~~~
adduser username
~~~
~~~
echo "username $(hostname)=(root) PASSWD: $(which su)" >> /etc/sudoers
~~~
Заменить "username" на ваше имя.

Теперь, если мы выйдем и войдем под пользователем **username**, то мы не сможем выполнить ни одной команды с правами супер-пользователя (sudo), кроме **su**.

~~~
exit
~~~

На своем компьютере генерируем ключи, если их нет:  
**Linux:**
~~~
ssh-keygen -t rsa
~~~
**~/.ssh/id_rsa.pub** - публичный ключ

На **Windows** можно использовать **PuTTYgen**, которое устанавливается вместе с **PuTTY** ([https://www.putty.org/](https://www.putty.org/)).
Выберите тип ключа **SSH2-RSA** и нажмите **Generate**.
После генерации ввести секретную фразу и сохранить файлы приватного и публичного ключей.

~~~
ssh username@111.222.333.444
~~~

Добавляем свой публичный ssh-ключ:

~~~
mkdir .ssh
~~~
~~~
echo "ssh-rsa AAAAB3NzaC1yc2EAAAAD....." > ~/.ssh/authorized_key
~~~

Теперь, если выйти и снова войти под пользователем **username**, то запроса пароля не будет. Запрос на ввод пароля будет только после ввода команды **su** - стать супер-пользователем.

Cтать супер-пользователем, используя свой пароль:
~~~
sudo su -
~~~
"-" - установить переменные окружения именно пользователя root.

Теперь можно отключить вход по паролю и вход под пользователем **root**:
~~~
nano /etc/ssh/sshd_config
~~~
Раскомментировать строчки:
~~~
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
~~~
Обратите внимание, что в строке "PasswordAuthentication" нужно поставить "no" (по умолчанию - yes).

В строке "PermitRootLogin" меняем значение на "no":
~~~
PermitRootLogin no
~~~
Нажимаем клавиши **"Ctrl+x"** и затем клавишу **"y"** (подтвердить изменения).

Перезапустим sshd:
~~~
/etc/init.d/ssh restart
~~~

### Установка файрвола

~~~
apt install ufw
~~~
Проверим состояние:
~~~
ufw app list
~~~
Вы должны увидеть что-то вроде:
~~~
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
~~~
Разрешить SSH соединения:
~~~
ufw allow OpenSSH
~~~

Включить файрвол:
~~~
ufw enable
~~~
Проверить статус:
~~~
ufw status
~~~

### Установка Nginx

~~~
apt install nginx
~~~

Разрешить трафик:
~~~
ufw allow 'Nginx HTTP'
~~~
~~~
ufw allow 'Nginx HTTPS'
~~~
Проверить статус:
~~~
ufw status
~~~

### Установка PHP 7.3

~~~
apt install software-properties-common

add-apt-repository ppa:ondrej/php
~~~
~~~
apt update
~~~
Копировать все строки вместе:
~~~
apt install php-pear php7.3 php7.3-common php7.3-cli \
php7.3-curl php7.3-dev php7.3-gd php7.3-mbstring php7.3-zip \
php7.3-xml php7.3-fpm php7.3-imagick php7.3-recode php7.3-tidy \
php7.3-xmlrpc php7.3-intl php7.3-mongodb
~~~
~~~
pecl install mongodb
~~~

~~~
nano /etc/php/7.3/fpm/php.ini
~~~
Найти строку, убрать ";" и установить значение "0":
~~~
cgi.fix_pathinfo=0
~~~
~~~
memory_limit = 256M
upload_max_filesize = 100M
post_max_size = 100M
~~~

Перезапуск PHP-fpm:
~~~
/etc/init.d/php7.3-fpm restart
~~~

### Установка MongoDB 4.2

Источник:
[https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

~~~
wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
~~~
~~~
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
~~~
~~~
apt update
apt install -y mongodb-org
~~~

~~~
nano /etc/mongod.conf
~~~
Установить значения для параметров в файле (порядок параметров оставить исходным):
~~~
bindIp: 0.0.0.0
security:
  authorization: "enabled"
~~~
Старт базы данных:
~~~
service mongod start
~~~
Проверка состояния (Вы должны увидеть "Active: active (running)"):
~~~
service mongod status
~~~
**Создание пользователей БД**
~~~
mongo
use admin
~~~
Создаем пользователя root:
~~~
db.createUser(
    {
        user: "root",
        pwd: "PASSWORD",
        roles: [{role:"root", db:"admin"}]
    }
)
~~~
Вместо "PASSWORD" введите свой пароль.
Авторизуемся под пользователем root:
~~~
db.auth('root', 'PASSWORD')
~~~
Создаем пользователя для приложения Shopkeeper4:
~~~
db.createUser(
    {
        user: "shopker-user",
        pwd: "PASSWORD",
        roles: [{role: "readWrite", db: "shopker"}]
    }
)
~~~
Готово. Для выхода из mongo shell нажать клавиши "Ctrl+c".

### Установка Shopker

Создать конфигурацию для Nginx:
~~~
nano /etc/nginx/sites-available/shopker.conf
~~~
Использовать пример конфигурации отсюда [https://github.com/andchir/shk4-wiki/blob/master/Настройка-веб-сервера.md](https://github.com/andchir/shk4-wiki/blob/master/Настройка-веб-сервера.md)
~~~
ln -s /etc/nginx/sites-available/shopker.conf /etc/nginx/sites-enabled/shopker.conf
~~~
~~~
/etc/init.d/nginx restart
~~~

~~~
mkdir /var/www/shopkeeper4 && cd /var/www/shopkeeper4
~~~
Загрузить ZIP-файл приложения Shopker, например, с использованием wget:
~~~
wget https://path/to/shopker-4.1.x.zip
~~~

Распаковать архив:
~~~
unzip shopker-4.1.x.zip
~~~
Настроить права доступа:
~~~
cd /var/www/shopkeeper4
sudo chown -R www-data:www-data .
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;
~~~

Открыть сайт в браузере и продолжить установку. Нужно ввести имя пользователя БД, пароль, имя базы данных (она будет создана автоматически). Указать адрес эл.почты и пароль администратора.
