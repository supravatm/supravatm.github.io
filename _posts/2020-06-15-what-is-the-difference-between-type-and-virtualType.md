---
layout: default
title: What is the difference between type and virtualType
description: What is the difference between type and virtualType
image: /assets/img/supravat-mondal-logo-picture.webp
permalink: /what-is-the-difference-between-type-and-virtualType.html
minutes: 5
---
**[Home](https://supravatm.github.io/) >> [Blog](/blogs.html)**

##  {{ page.title }}

<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> {{ page.minutes }} min read
</small>

<p class='blog-text-font-property'>Virtual types are a way to inject different dependencies into existing classes without affecting other classes.
</p>
<p class='blog-text-font-property'>
For example, the <mark>Magento\Framework\Session\Storage</mark> class takes a namespace argument in its constructor, which defaults to the value 'default', and you could use the type definition to change the namespace to 'core'.
</p>

<type name="Magento\Framework\Session\Storage">
    <arguments>
        <argument name="namespace" xsi:type="string">core</argument>
    </arguments>
</type>

<p class='blog-text-font-property'>
The above config would make it so that all instances of <mark>Magento\Framework\Session\Storage</mark> have a namespace of 'core'.  Using a virtual type allows for the equivalent of a sub-class to be created, where only the sub-class has the altered argument values.
</p>


<p class='blog-text-font-property'>
In the codebase we see the following two configurations:
</p>
<?php

    <virtualType name="Magento\Core\Model\Session\Storage" type="Magento\Framework\Session\Storage">
        <arguments>
            <argument name="namespace" xsi:type="string">core</argument>
        </arguments>
    </virtualType>

    <type name="Magento\Framework\Session\Generic">
        <arguments>
            <argument name="storage" xsi:type="object">Magento\Core\Model\Session\Storage</argument>
        </arguments>
    </type>
    
?>
<p class='blog-text-font-property'>
The first snippet creates a virtual type for <mark>Magento\Core\Model\Session\Storage</mark> which alters the namespace, and the second inject the virtual type into <mark>Magento\Framework\Session\Generic</mark>  This allows <mark>Magento\Framework\Session\Generic</mark> to be customized without affecting other classes that also declare a dependency on <mark>Magento\Framework\Session\Storage</mark>
</p>

_________________

