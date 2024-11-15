---
layout: artical
title:  Offline Payment Method In Magento 2
permalink: /how-to-create-a-custom-offline-payment-method-in-magento2.html
---

#### Offline Payment Method
Here we are about to see how to create an Offline Payment Method in Magento 2.

#### Key points to remember


- 1: Payment method module configuration

    
- 1: [Create Module](#create-module)
- 2: [Declare Payment Method](#declare-payment-method)
- 3: [Payment Method Model Implement](#payment-method-model-implement)
- 4: [Display Payment Method in Checkout Page ](#display-payment-method-in-checkout-page)
- 5: [Payment Method Settings in Admin](#payment-method-settings-in-admin)

#### 1: Create Module {#create-module}
---
##### 1.1: registration.php <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/registration.php</span>

```php
<?php
Magento\Framework\Component\ComponentRegistrar::register(
	Magento\Framework\Component\ComponentRegistrar::MODULE,
	'SMG_OfflinePaymentMethod',
	__DIR__
);
```

##### 2: module.xml <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/etc/module.xml </span>

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="SMG_OfflinePaymentMethod">
        <sequence>
            <module name="Magento_Sales"/>
            <module name="Magento_Payment"/>
            <module name="Magento_Checkout"/>
        </sequence>
    </module>
</config>
```

#### 2: Declare Payment Method {#declare-payment-method}

##### 2.1: payment.xml <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/etc/payment.xml </span>

```xml
<?xml version="1.0"?>
<payment xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Payment:etc/payment.xsd">
    <groups>
        <group id="offline">
            <label>Offline Payment Methods</label>
        </group>
    </groups>
    <methods>
        <method name="offlinepay">
            <allow_multiple_address>1</allow_multiple_address>
        </method>
    </methods>
</payment>
```
##### 2.2: config.xml <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/etc/config.xml </span>

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Store:etc/config.xsd">
    <default>
    <payment>
        <offlinepay>
            <active>1</active>
            <model>SMG\OfflinePaymentMethod\Model\OfflinePay</model>
            <order_status>pending</order_status>
            <title>Offline Pay</title>
            <allowspecific>0</allowspecific>
            <group>offline</group>
        </offlinepay>
        </payment>
    </default>
</config>
```

#### 3: Payment Method Model Implement {#payment-method-model-implement}

```php
<?php
namespace SMG\OfflinePaymentMethod\Model;

class OfflinePay extends Magento\Payment\Model\Method\AbstractMethod
{
    /**
     * Payment Method code
     *
     * @var string
     */
    protected $_code = 'offlinepay';
}
```

#### 4: Display Payment Method in Checkout Page {#display-payment-method-in-checkout-page}

##### 4.1: Declare the payment method in layout

The component for payment renderer that defined in <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/view/frontend/layout/checkout_index_index.xml </span>.

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceBlock name="checkout.root">
            <arguments>
                <argument name="jsLayout" xsi:type="array">
                    <item name="components" xsi:type="array">
                        <item name="checkout" xsi:type="array">
                            <item name="children" xsi:type="array">
                                <item name="steps" xsi:type="array">
                                    <item name="children" xsi:type="array">
                                        <item name="billing-step" xsi:type="array">
                                            <item name="children" xsi:type="array">
                                                <item name="payment" xsi:type="array">
                                                    <item name="children" xsi:type="array">
                                                        <item name="renders" xsi:type="array">
                                                            <!-- merge payment method renders here -->
                                                            <item name="children" xsi:type="array"> 
                                                                <item name="SMG-offlinepaymentmethod" xsi:type="array"> <!-- group name of the payment methods-->
                                                                    <item name="component" xsi:type="string">SMG_OfflinePaymentMethod/js/view/payment/offlinepay-renderer</item><!--component_that_registers_payment_renderer -->
                                                                    <item name="methods" xsi:type="array">
                                                                        <!-- Add this if your payment method requires entering a billing address-->
                                                                        <item name="offlinepay" xsi:type="array"><!-- payment_method_code -->
                                                                            <item name="isBillingAddressRequired" xsi:type="boolean">true</item>
                                                                        </item>
                                                                    </item>
                                                                </item>
                                                            </item>
                                                        </item>
                                                    </item>
                                                </item>
                                            </item>
                                        </item>
                                    </item>
                                </item>
                            </item>
                        </item>
                    </item>
                </argument>
            </arguments>
        </referenceBlock>
    </body>
</page>
```

##### 2. offlinepay-renderer.js

Create the <span class="inlinecode">offlinepay-renderer.js</span> UI component that registers the payment method renderer in the renderers list. It must be located under the <span class="inlinecode">your_module_dir/view/frontend/web/js/view/</span> directory. For example in the modules, the payment methods renderers are stored in the <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/view/frontend/web/js/view/payment/</span> directory.

```js
define(
    [
        'uiComponent',
        'Magento_Checkout/js/model/payment/renderer-list'
    ],
    function (
    Component,
    rendererList
    ) {
    'use strict';
    rendererList.push(
    {
        type: 'offlinepay', // type: '%payment_method_code%'
        component: 'SMG_OfflinePaymentMethod/js/view/payment/method-renderer/offlinepay-method' //'%js_renderer_component%'
    },
    // other payment method renderers if required
    );
    /** Add view logic here if needed */
    return Component.extend({});
});
```

##### 3. offlinepay-method.js

Create the <span class="inlinecode">.js</span> renderer component as <span class="inlinecode">offlinepay-method.js</span>. The corresponding file is <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/view/frontend/web/js/view/payment/method-renderer/offlinepay-method.js</span>

```js
define(
    [
      'Magento_Checkout/js/view/payment/default'
    ],
    function (Component) {
      'use strict';
  
      return Component.extend({
        defaults: {
          template: 'SMG_OfflinePaymentMethod/payment/offlinepay'
        },
      
        /** Returns send check to info */
        getMailingAddress: function() {
          return window.checkoutConfig.payment.customtemplate.mailingAddress;
        }
      
      });
    }
  );
```

##### 4. offlinepay.html 

Create the template for the payment method component as <span class="inlinecode">offlinepay.html</span>. The corresponding file is <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/view/frontend/web/template/payment/offlinepay.html</span>

```html
<div class="payment-method" data-bind="css: {'_active': (getCode() == isChecked())}">
    <div class="payment-method-title field choice">
      <input type="radio"   name="payment[method]" class="radio" data-bind="attr: {'id': getCode()}, value: getCode(), checked: isChecked, click: selectPaymentMethod, visible: isRadioButtonVisible()"/>
      <label data-bind="attr: {'for': getCode()}" class="label"><span data-bind="text: getTitle()"></span></label>
    </div>
    <div class="payment-method-content">
      <!-- ko foreach: getRegion('messages') -->
      <!-- ko template: getTemplate() --><!-- /ko -->
      <!--/ko-->
      <div class="payment-method-billing-address">
      <!-- ko foreach: $parent.getRegion(getBillingAddressFormName()) -->
      <!-- ko template: getTemplate() --><!-- /ko -->
      <!--/ko-->
      </div>
      <div class="checkout-agreements-block">
      <!-- ko foreach: $parent.getRegion('before-place-order') -->
      <!-- ko template: getTemplate() --><!-- /ko -->
      <!--/ko-->
      </div>
      <div class="actions-toolbar">
        <div class="primary">
          <button class="action primary checkout" type="submit" data-bind="   click: placeOrder,  attr: {title: $t('Place Order')}, css: {disabled: !isPlaceOrderActionAllowed()}, enable: (getCode() == isChecked()) " disabled>
          <span data-bind="i18n: 'Place Order'"></span>
          </button>
        </div>
      </div>
    </div>
  </div>
```

#### 5. Payment Method Settings in Admin {#payment-method-settings-in-admin}

Create a <span class="inlinecode">system.xml</span> file to Display settings on the Payment methods tab on the Configuration page. The corresponding file is <span class="inlinecode">app/code/SMG/OfflinePaymentMethod/etc/adminhtml/system.xml</span>

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Config:etc/system_file.xsd">
  <system>
    <section id="payment" type="text" sortOrder="400" showInDefault="1" showInWebsite="1" showInStore="1">
      <group id="offlinepay" translate="label" type="text" sortOrder="30" showInDefault="1" showInWebsite="1" showInStore="1">
        <label>Offline Pay</label>
        <field id="active" translate="label" type="select" sortOrder="1" showInDefault="1" showInWebsite="1" canRestore="1">
            <label>Enabled</label>
            <source_model>Magento\Config\Model\Config\Source\Yesno</source_model>
        </field>
        <field id="title" translate="label" type="text" sortOrder="10" showInDefault="1" showInWebsite="1" showInStore="1" canRestore="1">
            <label>Title</label>
        </field>
        <field id="order_status" translate="label" type="select" sortOrder="20" showInDefault="1" showInWebsite="1" canRestore="1">
            <label>New Order Status</label>
            <source_model>Magento\Sales\Model\Config\Source\Order\Status\NewStatus</source_model>
        </field>
        <field id="allowspecific" translate="label" type="allowspecific" sortOrder="50" showInDefault="1" showInWebsite="1" canRestore="1">
            <label>Payment from Applicable Countries</label>
            <source_model>Magento\Payment\Model\Config\Source\Allspecificcountries</source_model>
        </field>
        <field id="specificcountry" translate="label" type="multiselect" sortOrder="51" showInDefault="1" showInWebsite="1">
            <label>Payment from Specific Countries</label>
            <source_model>Magento\Directory\Model\Config\Source\Country</source_model>
            <can_be_empty>1</can_be_empty>
        </field>
        <field id="instructions" translate="label" type="textarea" sortOrder="62" showInDefault="1" showInWebsite="1" showInStore="1">
            <label>Instructions</label>
        </field>
        <field id="min_order_total" translate="label" type="text" sortOrder="98" showInDefault="1" showInWebsite="1">
            <label>Minimum Order Total</label>
            <validate>validate-number validate-zero-or-greater</validate>
        </field>
        <field id="max_order_total" translate="label" type="text" sortOrder="99" showInDefault="1" showInWebsite="1">
            <label>Maximum Order Total</label>
            <validate>validate-number validate-zero-or-greater</validate>
        </field>
        <field id="sort_order" translate="label" type="text" sortOrder="100" showInDefault="1" showInWebsite="1">
            <label>Sort Order</label>
            <validate>validate-number</validate>
        </field>
      </group>
    </section>
  </system>
</config>
```

#### Completed extension
---
Above mentioned classes and files are used for demonstration purpose. The complete extension can be found on GitHub at [Custom Offline Payment Module Extension](https://github.com/supravatm/magento2-sample-modules/){:target="_blank"}

#### Reference :

- [https://developer.adobe.com/commerce/php/development/payments-integrations/base-integration/](https://developer.adobe.com/commerce/php/development/payments-integrations/base-integration/){:target="_blank"}
- [https://developer.adobe.com/commerce/php/tutorials/frontend/custom-checkout/add-payment-method/](https://developer.adobe.com/commerce/php/tutorials/frontend/custom-checkout/add-payment-method/){:target="_blank"}