---
layout: default
permalink: /Useful-Ubuntu-Commands

***Create symbolic link***

To create a symlink at `/home/user/Desktop` which references the original file ``/home/name/Downloads`, use:

```
ln -s /home/name/Downloads /home/name/Desktop
```
As Example, `ln -s /etc/nginx/sites-available/b2bbtech /etc/nginx/sites-enabled/`

***Remove symbolic link***

You will use the following to remove the symbolic link

```
rm /home/user/Desktop/Downloads

```

To correct it simply navigate to the folder where the link is and unlink


```
cd /home/user/Desktop/
unlink Downloads
```


***Find the php.ini file used by the command line?***

```
php -i | grep 'Configuration File'
```

Returned:

```
Configuration File (php.ini) Path => /etc/php/7.1/cli
Loaded Configuration File => /etc/php/7.1/cli/php.ini
```

***Logon to MariaDB database server***

```
sudo mysql -u root -p
```
***Creaet database***

Create a database user called `magentouser` with new password

```
CREATE USER 'magentouser'@'localhost' IDENTIFIED BY 'new_password_here';
```
***Grant the user full access to the database**

```
GRANT ALL ON magento.* TO 'magentouser'@'localhost' IDENTIFIED BY 'user_password_here' WITH GRANT OPTION;
```

After, save your changes:

```
FLUSH PRIVILEGES;

```


***Wget command***

```
wget https://wordpress.org/latest.zip
```

You may want to save the file under a different name. To do this, you can use the -O option like this:

```
wget -O wordpress.zip https://wordpress.org/latest.zip
```

To download the file and save it in a different directory, you can use the -P option, for example:

```
wget -P /opt/wordpress https://wordpress.org/latest.zip
```
