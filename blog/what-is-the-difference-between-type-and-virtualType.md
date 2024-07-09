---
layout: default
title:  "What is the difference between type and virtualType"
date:   2019-02-01 60:03:25 -0800
tags: [github, Magento 2, supravatm, blog, cms, opensource, wordpress, blogger]
categories: [magento2]
image: magento2.png
---

Virtual types are a way to inject different dependencies into existing classes without affecting other classes.

For example, the **Magento\Framework\Session\Storage** class takes a **$namespace** argument in its constructor, which defaults to the value 'default', and you could use the **type** definition to change the namespace to 'core'.
{% highlight ruby %}
<type name="Magento\Framework\Session\Storage">
    <arguments>
        <argument name="namespace" xsi:type="string">core</argument>
    </arguments>
</type>
{% endhighlight %}

The above config would make it so that **all** instances of **Magento\Framework\Session\Storage** have a namespace of 'core'.  Using a virtual type allows for the equivalent of a sub-class to be created, where only the sub-class has the altered argument values.

In the codebase we see the following two configurations:

{% highlight ruby %}
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
{% endhighlight %}

The first snippet creates a virtual type for **Magento\Core\Model\Session\Storage** which alters the namespace, and the second inject the virtual type into **Magento\Framework\Session\Generic**.  This allows **Magento\Framework\Session\Generic** to be customized without affecting other classes that also declare a dependency on **Magento\Framework\Session\Storage**
