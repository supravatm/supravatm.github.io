---
layout: default
title:  How to add custom column in cart page in Magento 2
permalink: /how-to-add-custom-column-in-cart-page-in-magento-2.html
tag: magento2,cart-checkout,how-to 
---

<p>
By default, customer can be see all the Product name, Image, choose options, price, quantity and subtotal columns display inside the grid after he add product to the cart.
</p>
<p>
Now, if you want to add more details inside the grid on the checkout cart page then, you need to customize for that. Let’s see the steps below for that. In this example, I added the product <strong>sku</strong> column on the shopping cart page.
</p>

#### Key points to remember:

- 1: [Register Module](#create-module)
- 2: [Override <span class="inlinecode">checkout.cart.form</span>](#checkout-cart-index)
- 3: [Override <span class="inlinecode">checkout.cart.item.renderers</span>](#checkout-cart-item-renderers)


#### 1: Register Module {#create-module}
<p>Let's say, we have created a module <span class="inlinecode">SMG_CustomCheckoutRenderer</span> with a <span class="inlinecode">registration.php</span> and <span class="inlinecode">module.xml</span>.</p>

##### registration.php <span class="inlinecode">app/code/SMG/CustomCheckoutRenderer/registration.php</span>

```php
<?php
declare(strict_types=1);
use Magento\Framework\Component\ComponentRegistrar;
ComponentRegistrar::register(
    ComponentRegistrar::MODULE,
    'SMG_CustomCheckoutRenderer',
    __DIR__
);
```

##### module.xml <span class="inlinecode">app/code/SMG/CustomCheckoutRenderer/etc/module.xml </span>

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="SMG_CustomCheckoutRenderer">
        <sequence>
            <module name="Magento_Checkout" />
        </sequence>
    </module>
</config>
```

#### 2: Override <span class="inlinecode">checkout.cart.form</span> {#checkout-cart-index}

Create layout file <span class="inlinecode">checkout_cart_index.xml</span> inside the module and paste the below code for add column name inside grid :

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceBlock class="Magento\Checkout\Block\Cart" name="checkout.cart.form">
            <action method="setTemplate">
                <argument name="template" xsi:type="string">SMG_CustomCheckoutRenderer::cart/form.phtml</argument>
            </action>
            <block class="Magento\Framework\View\Element\RendererList" name="checkout.cart.item.renderers.override" as="renderer.list.custom"/>
            <arguments>
                <argument name="renderer_list_name" xsi:type="string">checkout.cart.item.renderers.override</argument>
            </arguments>
        </referenceBlock>
    </body>
</page>
```

Copy <span class="inlinecode">vendor/magento/module-checkout/view/frontend/templates/cart/form.phtml</span> and paste inside your module respectively. Then place the below code in <th> tag.

```php
<th class="col sku" scope="col"><span><?= $block->escapeHtml(__('SKU')) ?></span></th>
```

#### 3: Override <span class="inlinecode">checkout.cart.item.renderers</span> {#checkout-cart-index}

Then, Create <span class="inlinecode">checkout_cart_item_renderers.xml</span> file in your module layout folder and paste the below code for add column value inside grid.

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <update handle="checkout_item_price_renderers"/>
    <body>
        <referenceBlock name="checkout.cart.item.renderers.override">
            <block class="Magento\Checkout\Block\Cart\Item\Renderer" as="default" template="SMG_CustomCheckoutRenderer::cart/item/default.phtml" />
            <block class="Magento\Checkout\Block\Cart\Item\Renderer" as="simple" template="SMG_CustomCheckoutRenderer::cart/item/default.phtml" />
        </referenceBlock>
    </body>
</page>
```
<p>Now, for add product sku value dynamic inside product grid create default.phtml and paste the below code. Before paste the code copy file from <span class="inlinecode">vendor/magento/module-checkout/view/frontend/templates/cart/item/default.phtml</span> and paste on <span class="inlinecode">app/code/RH/CustomCheckoutRenderer/view/frontend/templates/cart/item/</span> path : </p>

```php
<td class="col sku" data-th="Sku">
    <span class="td-span-sku">
        <span class="sku-val"><?= $_item->getProduct()->getSku(); ?></span>
    </span>
</td>
```
Now, edit <span class="inlinecode">default.phtml</span> file inside your module and then, you need to paste the above code be before <span class="inlinecode"><?php if ($canApplyMsrp) :?></span> this if condition. 
Then run the following command:

<span class="inlinecode">bin/magento setup:upgrade</span>