---
layout: default
permalink: /setup-magento-2-on-nginx-web-server-ubuntu-18.04
---

Hi Everyone,

I think I wanna change apach2 to nginx web server on my localhost. So I first remove Apache2 server and then I installed Nginx. Today I will describe you on how to install Nginx and setup Magento 2 using Nginx Vertual Host.

Lets run below CLI commands one by one on your terminal.


Stop apache2 service
{% highlight ruby %}
sudo service apache2 stop
{% endhighlight %}


Remove apache2 from the system
{% highlight ruby %}
sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
{% endhighlight %}

Once you execute that command,  you need to comfirmation like below

Do you want to continue? [Y/n]
Type **Y** and press **Enter** key


Once you are complete, you need to remove files and directory from the system by help of

{% highlight ruby %}
whereis apache2
{% endhighlight %}

{% highlight ruby %}
rm -rf /usr/lib/apache2 /etc/apache2 /usr/share/man/man8/apache2.8.gz
{% endhighlight %}
