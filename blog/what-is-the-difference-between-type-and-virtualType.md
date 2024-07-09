---
layout: default
permalink: /what-is-the-difference-between-type-and-virtualType
---
**[Home](https://supravatm.github.io/) >> [Blog](https://supravatm.github.io/blogs.html) >> What is the difference between type and virtualType**

##### What is the difference between type and virtualType:

Virtual types are a way to inject different dependencies into existing classes without affecting other classes.

For example, the **Magento\Framework\Session\Storage** class takes a **$namespace** argument in its constructor, which defaults to the value 'default', and you could use the **type** definition to change the namespace to 'core'.


<type name="Magento\Framework\Session\Storage">
    <arguments>
        <argument name="namespace" xsi:type="string">core</argument>
    </arguments>
</type>

The above config would make it so that **all** instances of **Magento\Framework\Session\Storage** have a namespace of 'core'.  Using a virtual type allows for the equivalent of a sub-class to be created, where only the sub-class has the altered argument values.

In the codebase we see the following two configurations:

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


The first snippet creates a virtual type for **Magento\Core\Model\Session\Storage** which alters the namespace, and the second inject the virtual type into **Magento\Framework\Session\Generic**.  This allows **Magento\Framework\Session\Generic** to be customized without affecting other classes that also declare a dependency on **Magento\Framework\Session\Storage**

```lua
use App\Markdown\Extensions\WarningExtension;
use League\CommonMark\Environment\Environment;
use League\CommonMark\Extension\CommonMark\CommonMarkCoreExtension;
use League\CommonMark\Extension\HeadingPermalink\HeadingPermalinkExtension;
use League\CommonMark\Extension\TableOfContents\TableOfContentsExtension;
use League\CommonMark\MarkdownConverter;

$environment = new Environment();

// Register the core CommonMark parsers and renderers...
$environment->addExtension(new CommonMarkCoreExtension());

// Register our new WarningExtension
$environment->addExtension(new WarningExtension());

$output = (new MarkdownConverter($environment))
    ->convert(file_get_contents(__DIR__.'/markdown/article.md'))
    ->getContent();
```
