---
layout: artical
title:  How to create custom product attribute using Data Patch in Magento 2
permalink: /how-to-create-custom-product-attribute-using-data-patch-in-magento-2.html
---


<p>Magento 2 provides the provision to create custom product attribute programmatically.</p>
<p>We can create custom product attributes using the InstallData setup script as well as using Data Patch. After the release of Magento version 2.3, a data patch is a standard approach to managing database modifications. Using data patch we don’t need to manage <span class="inlinecode">setup_version</span> in <span class="inlinecode">etc/module.xml</span>. So in this blog, we cover the concept of creating custom product attributes using Data Patch with full code. In the following example, we are creating dropdown attribute</p>




<p>A data patch is a class that contains data modification instructions. It is defined in a <span class="inlinecode">Vendor/Module_Name/Setup/Patch/Data/Patch_Name.php</span> file and implements <span class="inlinecode">\Magento\Framework\Setup\Patch\DataPatchInterface</span>.</p>

#### Key points to remember:

- 1: [Register Module](#create-module)
- 2: [Data Patch](#data-patch)
- 3: [Source model](#source-model)
- 4: [Frontend model ](#frontend-model)
- 5: [Backend model](#backend-model)


#### 1: Register Module {#create-module}

##### registration.php <span class="inlinecode">app/code/SMG/CustomProductAttribute/registration.php</span>

```php
<?php
Magento\Framework\Component\ComponentRegistrar::register(
	Magento\Framework\Component\ComponentRegistrar::MODULE,
	'SMG_CustomProductAttribute',
	__DIR__
);
```

##### module.xml <span class="inlinecode">app/code/SMG/CustomProductAttribute/etc/module.xml </span>

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="SMG_CustomProductAttribute">
        <sequence>
            <module name="Magento_Eav"/>
        </sequence>
    </module>
</config>
```

#### 2: Data Patch {#data-patch}

##### File - <span class="inlinecode">app/code/SMG/CustomProductAttribute/Setup/Patch/Data/AddDropdownProductAttribute.php</span>

```php
<?php

declare(strict_types=1);

namespace SMG\CustomProductAttribute\Setup\Patch\Data;

use Magento\Eav\Model\Entity\Attribute\ScopedAttributeInterface;
use Magento\Eav\Setup\EavSetup;
use Magento\Eav\Setup\EavSetupFactory;
use Magento\Framework\Setup\ModuleDataSetupInterface;
use Magento\Framework\Setup\Patch\DataPatchInterface;
use Magento\Catalog\Model\Product;
use Magento\Catalog\Model\Product\Type;
use Psr\Log\LoggerInterface;

class AddDropdownProductAttribute implements DataPatchInterface
{
    /**
     * @var ModuleDataSetupInterface
     */
    private ModuleDataSetupInterface $moduleDataSetup;

    /**
     * @var EavSetupFactory
     */
    private EavSetupFactory $eavSetupFactory;

    /**
     * @var LoggerInterface
     */
    private LoggerInterface $logger;

    /**
     * @param ModuleDataSetupInterface $moduleDataSetup
     * @param EavSetupFactory $eavSetupFactory
     * @param LoggerInterface $logger
     */
    public function __construct(
        ModuleDataSetupInterface $moduleDataSetup,
        EavSetupFactory $eavSetupFactory,
        LoggerInterface $logger
    ) {
        $this->moduleDataSetup = $moduleDataSetup;
        $this->eavSetupFactory = $eavSetupFactory;
        $this->logger = $logger;
    }

    /**
     * @return void
     */
    public function apply()
    {
        try {
            $productTypes = implode(',', [Type::TYPE_SIMPLE, Type::TYPE_VIRTUAL]);

            /** @var EavSetup $eavSetup */
            $eavSetup = $this->eavSetupFactory->create(['setup' => $this->moduleDataSetup]);

            $eavSetup->addAttribute(Product::ENTITY, 'sample_dropdown_attribute', [
                'type' => 'int',
                'backend' => \SMG\CustomProductAttribute\Model\Entity\Attribute\Backend\TestBackend::class,
                'frontend' => \SMG\CustomProductAttribute\Model\Entity\Attribute\Frontend\TestFrontend::class,
                'label' => 'Sample Dropdown Attribute',
                'input' => 'select',
                'class' => '',
                'source' => \SMG\CustomProductAttribute\Model\Source\Config\Options::class,
                'global' => ScopedAttributeInterface::SCOPE_GLOBAL,
                'visible' => true,
                'required' => false,
                'user_defined' => false,
                'default' => '0',
                'searchable' => false,
                'filterable' => false,
                'comparable' => false,
                'visible_on_front' => true,
                'used_in_product_listing' => true,
                'unique' => false,
                'apply_to' => $productTypes
            ]);

        } catch (\Exception $e) {
            $this->logger->critical($e);
        }
    }

    /**
     * @return array
     */
    public static function getDependencies(): array
    {
        return [];
    }

    /**
     * @return array
     */
    public function getAliases(): array
    {
        return [];
    }
}
```

<p> For creating <b>multiselect</b> type product attribute, we just need to change the input value to <b>multiselect</b> insted of <b>select</b> in the above sample code. </p> 

#### 3: Source model {#source-model}

To populate dropdown options, we need to define a custom source model. The main purpose of a source model is to provide options for select-type attributes (select and multiselect). A source model must implement <span class="inlinecode">Magento\Eav\Model\Entity\Attribute\Source\SourceInterface</span> or extend <span class="inlinecode">Magento\Eav\Model\Entity\Attribute\Source\AbstractSource</span>.

File - <span class="inlinecode">app/code/SMG/CustomProductAttribute/Model/Source/Config/Options.php</span>

```php
<?php
declare(strict_types=1);
namespace SMG\CustomProductAttribute\Model\Source\Config;
use Magento\Eav\Model\Entity\Attribute\Source\AbstractSource;
class Options extends AbstractSource
{
    public function getAllOptions(): array
    {
        $this->_options = [
            ['label' => '', 'value' => '0'],
            ['label' => 'Small', 'value' => '1'],
            ['label' => 'Medium', 'value' => '2'],
            ['label' => 'Large', 'value' => '3'],
            ['label' => 'Test', 'value' => '4'],

        ];
        return $this->_options;
    }
}
```

![product view page](../assets/img/post/catalog-product-attribute.png)

#### 4: Frontend model {#frontend-model}

<p>The value of the attribute's frontend_model property must be set to a class that implements <span class="inlinecode">Magento\Eav\Model\Entity\Attribute\Frontend\FrontendInterface</span>(or extends <span class="inlinecode">Magento\Eav\Model\Entity\Attribute\Frontend\AbstractFrontend</span> , which is more meaningful). The key method to implement is <span class="inlinecode">getValue()</span> which takes an entity model as a parameter. </p>

file - <span class="inlinecode">app/code/SMG/CustomProductAttribute/Model/Entity/Attribute/Frontend/TestFrontend.php</span>

```php

<?php
declare(strict_types=1);
namespace SMG\CustomProductAttribute\Model\Entity\Attribute\Frontend;

use Magento\Eav\Model\Entity\Attribute\Frontend\AbstractFrontend;
use Magento\Framework\DataObject;
/**
 * Frontend model class inherited from AbstractFrontend
 */
class TestFrontend extends AbstractFrontend
{
    /**
     * override getValue method and set frontend_model
     *
     * @param \Magento\Framework\DataObject $object
     * @return void
     */
    public function getValue(\Magento\Framework\DataObject $object)
    {
        $attribute_code = $this->getAttribute()->getAttributeCode();
        $value = $object->getData($attribute_code);

        if($value) {
            $valueOption = $this->getOption($value); 
            return nl2br(htmlspecialchars($valueOption));
        }
    } 
}
```

<p>The main purpose of the frontend model is to render an attribute on the storefront, on the product view page:</p>

![product view page](../assets/img/post/product-page.png)

#### 5: Backend model {#backend-model}
Backend model must implement <span class="inlinecode">Magento\Eav\Model\Entity\Attribute\Backend\BackendInterface</span> or extend <span class="inlinecode">Magento\Eav\Model\Entity\Attribute\Backend\AbstractBackend</span>

<p>Backend modules are created with a purpose to upload / save / delete / validate attribute values. </p>
<p>Example of attribute value validation:</p>


```php
<?php
declare(strict_types=1);
namespace SMG\CustomProductAttribute\Model\Entity\Attribute\Backend;

use Magento\Eav\Model\Entity\Attribute\Backend\AbstractBackend;
use Magento\Framework\DataObject;
/**
 * Backend model class inherited from AbstractBackend
 */
class TestBackend extends AbstractBackend
{
    /**
     * Validate object
     *
     * @param \Magento\Framework\DataObject $object
     * @return bool
     * @throws LocalizedException
     * @SuppressWarnings(PHPMD.CyclomaticComplexity)
     */
    public function validate($object)
    {
    	$attribute_code = $this->getAttribute()->getAttributeCode();
        $value = $object->getData($attribute_code);

        if ($value == 4) {
            $valueOption = $this->getOption($value);
        	throw new \Magento\Framework\Exception\LocalizedException(__("Can't set value %1", $valueOption));
        }
        return true;
    }

    public function getOption($optionId)
    {
        $source = $this->getAttribute()->getSource();
        if ($source) {
            return $source->getOptionText($optionId);
        }
        return false;
    }
}
```
Then run the following command:

> bin/magento setup:upgrade