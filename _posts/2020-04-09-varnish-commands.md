---
layout: default
title: Varnish CLI Commands, Tips and Tricks
description: Varnish CLI Commands, Tips and Tricks | Supravat Mondal
permalink: /varnish-commands.html
---
**[Home](https://supravatm.github.io/) >> [Blog](https://supravatm.github.io/blogs.html) >> Varnish Commands**

### Varnish Commands
Varnish  as  a  command  line  interface  (CLI)  which  can control and change most of the operational parameters and the configuration of Varnish, without interrupting the  running service.


#### Install Varnish ####

```ssh
sudo apt install varnish -y
```

#### Start Varnish ####

```ssh
sudo systemctl start varnish
```

#### Stop Varnish ### 

```ssh
sudo systemctl stop varnish
```

#### Restart Varnish ####

```ssh
sudo systemctl restart varnish
```

In order to restart varnish I have to kill the process:

```console
sudo killall varnishd
```

#### Status Check ####

```ssh
sudo /etc/init.d/varnish status
```
  OR
```ssh
sudo service varnish status
```

#### Debug Varnish #### 

```ssh
sudo varnishd -d -f /etc/varnish/default.vcl
```


#### Default VCL file location #### 

> /etc/varnish/default.vcl

#### netstat #### 

Log in to the Varnish server and enter the following command:

```ssh
sudo netstat -tulpn
```

Look for the following output in particular:

```ssh
tcp        0      0 0.0.0.0:80                  0.0.0.0:*               LISTEN      32614/varnishd
tcp        0      0 127.0.0.1:58484             0.0.0.0:*               LISTEN      32604/varnishd
tcp        0      0 :::8080                     :::*                    LISTEN      26822/httpd
tcp        0      0 ::1:48509                   :::*                    LISTEN      32604/varnishd
```


#### Put Varnish on port 80 #### 

Until now we've been running with Varnish on a high port, for testing purposes. You should test your application and if it works OK we can switch, so Varnish will be running on port 80 and your web server on a high port.

#### First we kill off varnishd: #### 

```ssh
sudo pkill varnishd
```

and stop your web server. Edit the configuration for your web server and make it bind to port 8080 instead of 80. Now open the Varnish default.vcl and change the port of the default backend to 8080.

### Start up your web server and then start varnish: ### 

```ssh
sudo varnishd -f /usr/local/etc/varnish/default.vcl -s malloc,1G -T 127.0.0.1:2000
```

#### Uninstall ####

```bash
sudo apt-get remove --auto-remove varnish
sudo apt-get purge --auto-remove varnish
```

## Reference link

[Installing and configuring Varnish](https://www.varnish-software.com/wiki/content/tutorials/varnish/varnish_ubuntu.html)

