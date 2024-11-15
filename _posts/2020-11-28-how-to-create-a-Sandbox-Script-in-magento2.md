---
layout: default
title: How to Create A Sandbox Script-in-magento2
permalink: /how-to-create-a-Sandbox-Script-in-magento2.html
minutes: 5
---
**[Home](https://supravatm.github.io/) >> [Blog](/blogs.html)**

##  {{ page.title }}

<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> {{ page.minutes }} min read
</small>

Just like the script from Magento 1.x, if you need to do some basic sandbox testing of functionality, you can do it within this simple sandbox script.

```ruby
require_once 'app/bootstrap.php';
$bootstrap = \Magento\Framework\App\Bootstrap::create(BP, $_SERVER);
#any code you want here....

#Now you have access to the entire Magento application.
 
#If you want to work with classes, you can instantiate new classes
#from the object manager from within bootstrap. 

$class = $bootstrap->getObjectManager()->create('Namespace\Module\Model\Test');
```

I know that when I need to tinker with a new platform, a bootstrap is always good to figure things out.  Hopefully this will help you as well.

_________________

