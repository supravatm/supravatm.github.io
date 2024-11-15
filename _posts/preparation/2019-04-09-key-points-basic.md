---
layout: default
title: Magento 2 Esensial topics for Developer
permalink: /magento-2-essential-topics-notes-for-developer.html
minutes: 5
---
**[Home](https://supravatm.github.io/) >> [Blog](/blogs.html)**

##  {{ page.title }}

<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> {{ page.minutes }} min read
</small>

**Dependency injection**:

Dependency injection.....

**Virtual types**:

<span class="inlinecode">Virtual types</span> are a way to inject different dependencies into existing classes without affecting other classes.

Reference - [https://magento.stackexchange.com/a/33140/3368](https://magento.stackexchange.com/a/33140/3368){:target="_blank"}

**types**:

<span class="inlinecode">types</span> lets us manipulate the dependencies injected to class constructors

**Proxies**:

<span class="inlinecode">Proxy</span> classes use for lazy creation of object. 

Proxy classes are intended to increase object creation speed. For example you want to create an object of the class where a lot of another classes are created in the constructor via dependency injection. Maybe you won't use those classes but they are created and some of them can take some time to be created. To avoid such a scenario Proxy objects exists.

Reference - [Recommend to read Magento 2 Object Manager: Proxy Objects ](https://alanastorm.com/magento_2_object_manager_proxy_objects/){:target="_blank"}

**Factories**:

Factories are service classes that instantiate non-injectable classes, that is, models that represent a database entity. They create a layer of abstraction between the ObjectManager and business code.

Reference - [Illustration on Developer Docs ](https://alanastorm.com/magento_2_object_manager_proxy_objects/){:target="_blank"}

**Plugins (interceptors)**:

 A plugin, or interceptor, is a class that modifies the behavior of public class functions by intercepting a function call and running code before, after, or around that function call. This allows you to substitute or extend the behavior of original, public methods for any class or interface.


 Reference - [Illustration on Developer Docs ](https://developer.adobe.com/commerce/php/development/components/plugins/){:target="_blank"}
