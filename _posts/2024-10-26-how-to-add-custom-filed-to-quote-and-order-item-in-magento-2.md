---
layout: default
title:  How to add custom product attribute to quote and order item in Magento 2
permalink: /how-to-add-custom-filed-to-quote-and-order-item-in-magento-2.html
---


##### 1. Register A Module: 
 Required to register a module by create a <span class="inlinecode">registration.php</span> and a <span class="inlinecode">module.php</span> file.

#####  2. Define catalog_attributes.xml: 

<p>By default custom product attribute not loading when we work with Quote or wishlist item. To load the new attribute, we need to create <span class="inlinecode">catalog_attributes.xml</span>. In this example, a <span class="inlinecode">sample_attribute</span> attribute will be add Magento.</p>

file - <span class="inlinecode">app/code/Vendor/Module/etc/catalog_attributes.xml</span>
```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Catalog:etc/catalog_attributes.xsd">
    <group name="quote_item">
        <attribute name="sample_attribute"/>
    </group>
</config>
```


#####  3. Define a db_schema.xml:
Let's create a declarative schema file (db_schema.xml) to create a field in the quote item table.

File- <span class="inlinecode">app/code/Vendor/Module/etc/db_schema.xml</span>

```xml
<?xml version="1.0"?>
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="quote_item" resource="default" engine="innodb">
        <column name="sample_attribute" nullable="true" xsi:type="varchar" length="100" comment="sample attribute"/>
    </table>
    <table name="sales_order_item" resource="default" engine="innodb">
        <column name="sample_attribute" nullable="true" xsi:type="varchar" length="100" comment="sample attribute"/>
    </table>
</schema>
```

##### 4. Define an Event and Observer:

Observe the event name : <span class="inlinecode">sales_quote_item_set_product</span> to save product custom attribute in the quote table.

FIle- <span class="inlinecode">app/code/Vendor/Module/events.xml</span>

```php
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Event/etc/events.xsd">
    <event name="sales_quote_item_set_product">
        <observer name="set_item_dropdown_attribute" instance="SMG\CustomFieldToQuoteItem\Observer\SetItemSampleAttribute" />
    </event>
</config>
```

add observer class <span class="inlinecode">SetItemSampleAttribute</span>. This will set Custom attribute to the qoute item.

File- <span class="inlinecode">app/code/Vendor/Module/Observer/SetItemSampleAttribute.php</span>

```php
<?php
declare(strict_types=1);
namespace SMG\CustomFieldToQuoteItem\Observer;

use Magento\Framework\Event\ObserverInterface;
use Magento\Framework\Event\Observer;

class SetItemSampleAttribute implements ObserverInterface
{
    public function execute(Observer $observer)
    {
        $quoteItem = $observer->getQuoteItem();
        $product = $observer->getProduct();
        $quoteItem->setSampleAttribute($product->getSampleAttribute());
    }
}
```

##### 5. Define a Plugin:

In this step, attribute will be copy to Order Item table form Quote Item Table. For this copy we are adding a plugin. 
<span class="inlinecode">app/code/Vendor/Module/etc/di.xml</span>
```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <type name="Magento\Quote\Model\Quote\Item\ToOrderItem">
        <plugin name="sample_attribute_quote_to_order_item" type="SMG\CustomFieldToQuoteItem\Plugin\SampleAttributeQuoteToOrderItem"/>
    </type>
</config>
```

Plugin Class- <span class="inlinecode">app/code/Vendor/Module/Plugin/SampleAttributeQuoteToOrderItem.php</span> 

```php
<?php
declare(strict_types=1);
namespace SMG\CustomFieldToQuoteItem\Plugin;

class SampleAttributeQuoteToOrderItem
{
    public function aroundConvert(
        \Magento\Quote\Model\Quote\Item\ToOrderItem $subject, 
        \Closure $proceed, 
        \Magento\Quote\Model\Quote\Item\AbstractItem $item,
        $additional = []
    )
    {
         /** @var $orderItem \Magento\Sales\Model\Order\Item */
         $orderItem = $proceed($item, $additional);
         $orderItem->setSampleAttribute($item->getSampleAttribute());
         return $orderItem;
    }
}
```