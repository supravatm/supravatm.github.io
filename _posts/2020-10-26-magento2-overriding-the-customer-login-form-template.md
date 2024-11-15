---
layout: default
title: How to overriding the Customer Login Form Template in Magento 2
permalink: /magento2-overriding-the-customer-login-form-template.html
---
**[Home](https://supravatm.github.io/) >> [Blog](/blogs.html)**

##  {{ page.title }}
{% assign minutes = content | number_of_words | divided_by: 160 %}
{% if minutes == 0 %}{% assign minutes = 1 %}{% endif %}
<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> {{ minutes }} min read
</small>

To successfully replace the <b>Magento_Customer::form/login.phml</b> template with a custom template

1. <p>Create the following directory structure <b>app/design/frontend/Vendor/theme/Magento_Customer/templates/form/</b> </p>
2. <p>Add the template file <b>app/design/frontend/Vendor/theme/Magento_Customer/templates/form/login.phtml</b></p>
3. <p>Add desired custom code within the <b>login.phtml</b> file.</p>
4. <p>Verify the custom theme is already in place (or enable it if you haven't already) by going to: <b>Admin panel->Content->Design Configuration->Your Store or Store View</b>. And sure your custom theme shows under <b>Applied Theme</b></p>
5. <p>If you're not in developer mode, or if you are in developer mode and have manually deployed static content <b>(php bin/magento setup:static-content:deploy)</b> then you'll want to clear your cache and <mark>pub/static/</mark> (except the .htaccess) directories. If you are in <b>developer</b> mode and haven't manually deployed static assets skip this step.</p>
6. <p>Refresh your <span style="color:#267CB9">http://YOUR_DOMAIN.COM/customer/account/login/</span> page, or go visit it, and view the desired changes.</p>
_________________

