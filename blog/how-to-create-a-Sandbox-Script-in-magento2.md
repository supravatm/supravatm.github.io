---
layout: default
permalink: /how-to-create-a-Sandbox-Script-in-magento2
---
Just like the script from Magento 1.x, if you need to do some basic sandbox testing of functionality, you can do it within this simple sandbox script.

{% highlight ruby %}
<?php
require_once 'app/bootstrap.php';
$bootstrap = \Magento\Framework\App\Bootstrap::create(BP, $_SERVER);

// any code you want here....
```

Now you have access to the entire Magento application.

If you want to work with classes, you can instantiate new classes from the object manager from within bootstrap.

```
$class = $bootstrap->getObjectManager()->create('Namespace\Module\Model\Test');
{% endhighlight %}

I know that when I need to tinker with a new platform, a bootstrap is always good to figure things out.  Hopefully this will help you as well.
