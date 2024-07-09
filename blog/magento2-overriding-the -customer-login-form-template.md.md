---
layout: default
title:  "How to overriding the Customer Login Form Template in Magento 2"
date:   2018-08-22 60:03:25 -0800
tags: [github, Magento 2, supravatm, blog, cms, opensource, wordpress, blogger]
categories: [magento2]
image: magento2.png

---
To successfully replace the Magento_Customer::form/login.phml template with a custom template

1. Create the following directory structure ```app/design/frontend/Vendor/theme/Magento_Customer/templates/form/```
2. Add the template file ```app/design/frontend/Vendor/theme/Magento_Customer/templates/form/login.phtml```
3. Add desired custom code within the ```login.phtml``` file.
4. Verify the custom theme is already in place (or enable it if you haven't already) by going to: ```Admin panel->Content->Design Configuration->Your Store or Store View```. And sure your custom theme shows under ```Applied Theme```
5. If you're not in developer mode, or if you are in developer mode and have manually deployed static content ```(php bin/magento setup:static-content:deploy)** then you'll want to clear your cache and ``pub/static/`` (except the .htaccess) directories. If you are in developer mode and haven't manually deployed static assets skip this step.

6. Refresh your ```http://YOUR_DOMAIN.COM/customer/account/login/``` page, or go visit it, and view the desired changes.
