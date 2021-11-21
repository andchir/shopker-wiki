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

