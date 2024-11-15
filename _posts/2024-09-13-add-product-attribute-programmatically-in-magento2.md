---
layout: default
title:  Adding Product Attribute Programmatically in Magento2
permalink: /add-product-attribute-programmatically-in-magento2.html
---
**[Home](https://supravatm.github.io/) >> [Blog](/blogs.html)**

##  {{ page.title }}
{% assign minutes = content | number_of_words | divided_by: 160 %}
{% if minutes == 0 %}{% assign minutes = 1 %}{% endif %}
<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> {{ minutes }} min read
</small>

#### Overview of Adding Product Attribute Programmatically

1. Create file <span class="inlinecode">InstallData.php</span>
2. Define the <span class="inlinecode">install()</span> method
3. EAV attribute properties
4. Create file <span class="inlinecode">UpgradeData</span> (Optional)

### 1. Create file <span class="inlinecode">InstallData.php</span>

<span class="inlinecode">app/code/SMG/CustomAddProductAttribute/Setup/InstallData.php</span>

```php
<?php
namespace SMG\CustomAddProductAttribute\Setup;
use Magento\Eav\Setup\EavSetup;
use Magento\Eav\Setup\EavSetupFactory;
use Magento\Framework\Setup\InstallDataInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Framework\Setup\ModuleDataSetupInterface;
class InstallData implements InstallDataInterface
{
	private $eavSetupFactory;
	public function __construct(
        EavSetupFactory $eavSetupFactory
    ) {
		$this->eavSetupFactory = $eavSetupFactory;
	}
}
```

### 2. Define the <span class="inlinecode">install()<span> method

```php
<?php
public function install(
    ModuleDataSetupInterface $setup, 
    ModuleContextInterface $context
) {
}
```

### 3. EAV attribute properties

Here are all lines code of <span class="inlinecode">InstallData.php<span> to create product attribute programmically.

```php
<?php
namespace SMG\CustomAddProductAttribute\Setup;
use Magento\Eav\Setup\EavSetup;
use Magento\Eav\Setup\EavSetupFactory;
use Magento\Framework\Setup\InstallDataInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Framework\Setup\ModuleDataSetupInterface;
class InstallData implements InstallDataInterface
{
	private $eavSetupFactory;
	public function __construct(EavSetupFactory $eavSetupFactory)
	{
		$this->eavSetupFactory = $eavSetupFactory;
	}
	public function install(ModuleDataSetupInterface $setup, ModuleContextInterface $context)
	{
		$eavSetup = $this->eavSetupFactory->create(['setup' => $setup]);
		$eavSetup->addAttribute(
			Magento\Catalog\Model\Product::ENTITY,
			'sample_attribute',
			[
				'type' => 'text',
				'backend' => '',
				'frontend' => '',
				'label' => 'SMG Custom Attribute',
				'input' => 'text',
				'class' => '',
				'source' => '',
				'global' => Magento\Eav\Model\Entity\Attribute\ScopedAttributeInterface::SCOPE_GLOBAL,
				'visible' => true,
				'required' => false,
				'user_defined' => false,
				'default' => '',
				'searchable' => false,
				'filterable' => false,
				'comparable' => false,
				'visible_on_front' => false,
				'used_in_product_listing' => true,
				'unique' => false,
				'apply_to' => ''
			]
		);
	}
}
```

Refer the following [link](/eav-attribute-data-type.html) to see the several attribute properties.

### 4. Create file <span class="inlinecode">UpgradeData</span> (Optional)

After created <span class="inlinecode">sample_attribute</span> attribue to update the this attribute's properties if needed. <span class="inlinecode">UpgradeData.php</span> is required to create and upgrade module <span class="inlinecode">schema_version</span>.

```php
<?php
namespace SMG\CustomAddProductAttribute\Setup;
use Magento\Framework\Setup\UpgradeDataInterface;
use Magento\Framework\Setup\ModuleDataSetupInterface;
use Magento\Framework\Setup\ModuleContextInterface;
use Magento\Eav\Setup\EavSetupFactory;
class UpgradeData implements UpgradeDataInterface
{
	private $eavSetupFactory;
	public function __construct(EavSetupFactory $eavSetupFactory)
	{
		$this->eavSetupFactory = $eavSetupFactory;
	}
    public function upgrade(ModuleDataSetupInterface $setup,ModuleContextInterface $context)
    {
        if (version_compare($context->getVersion(), "1.0.1", "<"))
        {
            $eavSetup = $this->eavSetupFactory->create(['setup' => $setup]);
            $eavSetup->updateAttribute(
                Magento\Catalog\Model\Product::ENTITY,
			    'sample_attribute',
                'frontend_label',
                'SMG Custom Attribute'
            );
        }
        if (version_compare($context->getVersion(), "1.0.2", "<"))
        {
            $eavSetup = $this->eavSetupFactory->create(['setup' => $setup]);
            $eavSetup->updateAttribute(
                Magento\Catalog\Model\Product::ENTITY,
			    'sample_attribute',
                'frontend_label',
                'Custom Attribute'
            );
        }
    }
}
```


