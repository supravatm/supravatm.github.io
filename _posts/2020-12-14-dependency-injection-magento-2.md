---
layout: artical
title: Dependency Injection Magento2
permalink: /dependency-injection-magento-2.html
---


<p>In programming languages, a dependency is known as the object which is needed by a class to perform some functions. Injection is the passing of that particular dependency to a dependent object/class. The real meaning of Dependency Injection is to inject the dependency into the class from another source.</p>

<p>For example, if you have defined a Class in Magento 2 that fetches some data by using Magento 2 Helper Class, we can say that your Class has a dependency of that Magento 2 Helper Object.</p>

<p>By using Automatic Dependency Injection (ADI), the object doesn’t need to look for another dependent object. There are three types of Automatic Dependency Injections:</p>

* **Constructor Injection**: The most common way to inject dependencies is via a class’s constructor. To do this, you need to add an argument to the constructor signature to accept the dependency.
* **Setter Injection**: Another possible method to inject a dependency into a class is by adding a setter function that takes the dependency.
* **Interface Injection**: The interface injection defines and uses interfaces for the dependency injection.

<p>Magento 2 uses Automatic Dependency Injection (ADI) which is further derived to the Constructor Injection. It means in Magento 2, passing the objects in the constructor’s parameters will help you follow the injection rules. Dependency Injection in Magento 2 is an alternative to the Mage Class method of Magento 1. Below example will give you an idea that how it will look like in Magento 2.</p>

#### Helper Class in Magento 2 (Data.php):

```ruby
namespace Namespace\Module\Helper;
class Data extends Magento\Framework\App\Helper\AbstractHelper
{
    public function HelperFunc()
    {
        return 1;
    }
}
```

#### Calling HelperFunc() using DI:

```ruby
class ClassName
{
    public function __construct(Namespace\Module\Helper\Data $helper)
    {
        $this->helper = $helper;
    }
    public function func()
    {
        $this->helper->HelperFunc();
    }
}
```

<p>In above code class, you can see how Dependency is injected into the constructor of the class. You can use this method anywhere in Magento 2.</p>

_________________


