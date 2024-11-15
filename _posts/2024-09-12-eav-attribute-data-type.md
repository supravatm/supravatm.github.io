---
layout: artical
title:  EAV attribute Data Types in Magento 2
permalink: /eav-attribute-data-type.html
---

#### Explanation of EAV Data Type

- **type**: define format of attributes (static, varchar, int, decimal, datetime). Static type is a little bit different because attribute data with this input type is stored in the main table (catalog_product_entity, customer_entity, customer_address_entity, …), not in the text, varchar, decimal, int, datetime tables.
- **group**: define the group of attributes (see more in the  eav_attribute_group table). Magento 2 classifies attributes into attribute groups and attribute sets corresponding to product lines or each product. If you don’t declare an attribute, it will belong to the default group depending on attribute types.
- **label**: specify the display label of the attribute.
- **input**: text, int, select, multiselect, date, hidden, boolean, multiline,…
- **backend**: another name is Backend Model which is used to modify attribute data when saving attribute, including validate, afterLoad, beforeSave, afterSave, beforeDelete, afterDelete (see more at Magento\Eav\Model\Entity\Attribute\Backend\AbstractBackend).
- **frontend**: you use it to change data as you want after getting data from database ( see more at Magento\Eav\Model\Entity\Attribute\Frontend\AbstractFrontend).
- **required**: there are two values as True and False.
- **global**: specifies the scope of the attribute. 3 scopes as website, store, global are declared at \Magento\Eav\Model\Entity\Attribute\ScopedAttributeInterface. The default scope is global.
- **is_used_in_grid**: enter True or False to determine whether the attribute uses grid or not (depending on entity types to have corresponding grids).
- **is_visible_in_grid**: enter True or False to determine whether the attribute is visible in the grid or not (depending on entity types to have corresponding grids).
- **is_filterable_in_grid**: enter True or False to determine whether the attribute is filterable in the grid or not (depending on entity types to have corresponding grids).
- **visible**: enter True or False to determine whether the attribute is visible in both frontend and backend or not.
- **user_defined**: enter True or False to determine whether the admin can change/remove the attribute or not.
- **searchable**: enter True or False to determine whether customers can use attribute for searching in the frontend or not.
- **filterable**: enter True or False to determine whether customers can use attribute for filtering in the frontend or not.
- **comparable**: enter True or False to determine whether customers can use attribute for comparing in the frontend or not.
- **filterable_in_search**: enter True or False to determine whether the attribute is used to filter in the frontend layer navigation or not. (only applying to attributes with input type as int).
- **is_html_allowed_on_front**: enter True or False to determine whether the attribute value is formatted by HTML tags or not.

- **used_for_promo_rules**: enter True or False to determine whether the attribute is used in promotion rules or not.
- **visible_on_front**: enter True or False to determine whether the attribute is shown in the Additional Information tab of the frontend product page or not. (only applying to Simple Product or Virtual Product and also depending on visible mentioned above).
- **used_for_sort_by**: enter True or False to determine whether the attribute is used to sort on the category page or not.
- **unique**: enter True or False to determine whether the attribute value is unique (no repeat) or not.
- **default**: the default value of the attribute. After saving, if the attribute has no value, it takes this default value.
- **used_in_product_listing**: enter Yes or No (Yes: the attribute is added when loading the collection of product, category,…)
- **source**: it is used to change display data of the attribute. (mainly change attribute options for input types as select, multiselect, dropdown, radio – you need to use class to modify).
- **option**: you can use option as the way you use source, but you need to declare option in the setup file.  For example: ‘option’ => [‘values’ => [‘Test1’, ‘Test2’, ‘Test3’]]
- **sort_order**: the order of the current attribute versus others
- **apply_to**: determine which product type to apply