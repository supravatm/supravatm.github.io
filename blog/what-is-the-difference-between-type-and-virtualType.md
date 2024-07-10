---
layout: default
title: What is the difference between type and virtualType
description: What is the difference between type and virtualType
image: /assets/img/supravat-mondal-logo-picture.webp
permalink: /what-is-the-difference-between-type-and-virtualType
---
**[Home](https://supravatm.github.io/) >> [Blog](https://supravatm.github.io/blogs.html) >> What is the difference between type and virtualType**

##### What is the difference between type and virtualType:

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

<p class='blog-text-font-property'>
The first snippet creates a virtual type for <mark>Magento\Core\Model\Session\Storage</mark> which alters the namespace, and the second inject the virtual type into <mark>Magento\Framework\Session\Generic</mark>  This allows <mark>Magento\Framework\Session\Generic</mark> to be customized without affecting other classes that also declare a dependency on <mark>Magento\Framework\Session\Storage</mark>
</p>

```php
<?php
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

Next to here

```php
<?php

class MyMarkdown extends \cebe\markdown\Markdown
{
	protected function identifyFencedCode($line, $lines, $current)
	{
		// if a line starts with at least 3 backticks it is identified as a fenced code block
		if (strncmp($line, '```', 3) === 0) {
			return true;
		}
		return false;
	}

	// ...
}
```
