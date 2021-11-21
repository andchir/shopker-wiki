### Ubuntu 18.04.3 LTS

### Initial setup

Login to the server via SSH:
~~~
ssh root@111.222.333.444
~~~
111.222.333.444 - example server IP

View a list of available locales:
~~~
locale -a | grep ru
~~~
Generate English locale:
~~~
locale-gen n_US.UTF-8
~~~
Another option for installing the required locales:
~~~
dpkg-reconfigure locales
~~~

Update packages:
~~~
apt update && apt upgrade
~~~

Select time zone:
~~~
dpkg-reconfigure tzdata
~~~

Install packages that come in handy:
~~~
apt install software-properties-common wget nano unzip
~~~

### Add a new super user

~~~
adduser username
~~~
~~~
echo "username $(hostname)=(root) PASSWD: $(which su)" >> /etc/sudoers
~~~
Replace "username" with your name.

Now, if we log out and log in as **username**, then we will not be able to execute any commands with super-user (sudo) rights, except **su**.

~~~
exit
~~~

We generate keys on our computer, if they are not there: 
**Linux:**
~~~
ssh-keygen -t rsa
~~~
**~/.ssh/id_rsa.pub** - public key

On **Windows** you can use **PuTTYgen**, which is installed with **PuTTY** ([https://www.putty.org/](https://www.putty.org/)).
Select the key type **SSH2-RSA** and click **Generate**.
After generation, enter the secret phrase and save the private and public key files.

~~~
ssh username@111.222.333.444
~~~

Add your public ssh key:

~~~
mkdir .ssh
~~~
~~~
echo "ssh-rsa AAAAB3NzaC1yc2EAAAAD....." > ~/.ssh/authorized_key
~~~

Now, if you log out and log back in as **username**, you will not be prompted for a password. You will be prompted for a password only after entering the **su** command - to become a super-user.

Become a super user using your password:
~~~
sudo su -
~~~
"-" - set the environment variables for the root user.

Now you can disable password login and **root** login:
~~~
nano /etc/ssh/sshd_config
~~~
Uncomment the lines:
~~~
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no
~~~
Note that the "PasswordAuthentication" line must be set to "no" (default is yes).

In the line "PermitRootLogin" change the value to "no":
~~~
PermitRootLogin no
~~~
Press the **"Ctrl + x"** keys and then the **"y"** key (confirm the changes).

Restart sshd:
~~~
/etc/init.d/ssh restart
~~~

If you don't want to use SSH keys, you can add a user to the **sudo** group with the following command:
~~~
usermod -aG sudo username
~~~
Adding to the **www-data** group:
~~~
usermod -aG www-data username
~~~
Check user groups:
~~~
groups username
~~~

### Installing a firewall

~~~
apt install ufw
~~~
Let's check the status:
~~~
ufw app list
~~~
You should see something like:
~~~
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
~~~
Allow SSH connections:
~~~
ufw allow OpenSSH
~~~

Enable firewall:
~~~
ufw enable
~~~
Check status:
~~~
ufw status
~~~

### Installing a Nginx

~~~
apt install nginx
~~~

Allow traffic:
~~~
ufw allow 'Nginx HTTP'
~~~
~~~
ufw allow 'Nginx HTTPS'
~~~
Check status:
~~~
ufw status
~~~

### Installing PHP 7.4

~~~
add-apt-repository ppa:ondrej/php
~~~
~~~
apt update
~~~
Copy all lines together:
~~~
apt install php-pear php7.4 php7.4-common php7.4-cli \
php7.4-curl php7.4-dev php7.4-gd php7.4-mbstring php7.4-zip \
php7.4-xml php7.4-fpm php7.4-imagick php7.4-recode php7.4-tidy \
php7.4-xmlrpc php7.4-intl php7.4-mongodb
~~~
~~~
pecl install mongodb
~~~

~~~
nano /etc/php/7.4/fpm/php.ini
~~~
Find a line, remove ";" and set the value to "0":
~~~
cgi.fix_pathinfo=0
~~~
~~~
memory_limit = 256M
upload_max_filesize = 100M
post_max_size = 100M
~~~

Restart PHP-fpm:
~~~
/etc/init.d/php7.4-fpm restart
~~~

### Installing MongoDB 4.2

A source:
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
Set values for parameters in the file (leave the order of parameters as they were):
~~~
bindIp: 0.0.0.0
security:
  authorization: "enabled"
~~~
Database start:
~~~
service mongod start
~~~
Checking the status (you should see "Active: active (running)"):
~~~
service mongod status
~~~

**Creating database users**
~~~
mongo
use admin
~~~
Create a root user:
~~~
db.createUser(
    {
        user: "root",
        pwd: "PASSWORD",
        roles: [{role:"root", db:"admin"}]
    }
)
~~~
Replace "PASSWORD" with your password.  
Log in as root:
~~~
db.auth('root', 'PASSWORD')
~~~
Create a user for the Shopker app:
~~~
db.createUser(
    {
        user: "shopker-user",
        pwd: "PASSWORD",
        roles: [{role: "readWrite", db: "shopker"}]
    }
)
~~~
Done. To exit the mongo shell, press the "Ctrl + c" keys.

