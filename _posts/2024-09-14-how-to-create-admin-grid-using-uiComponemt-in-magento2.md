---
layout: artical
title:  How To Create An Admin Grid Using uiComponemt In Magento 2
permalink: /create-admin-grid-using-uiComponemt-in-magento2.html
---


#### Basic Usage of uiComponent Grid

Admin Grids are represents Listing, Filter, and sort various data. They are also used to perform mass actions such as updates and deletes. 

#### To Create Admin Grid using UiComponent

- [1: Define The Admin Grid](#define-the-admin-grid)
- [2: Define The Data Source Class](#define-the-data-source-class)
- [3: Data Source Collection](#data-source-collection)
- [4: Column Actions Class](#column-actions-class)
- [5: Backend Controllers](#backend-controllers)


#### 1: Define The Admin Grid {#define-the-admin-grid}

The page layout file is <span class="inlinecode">view/adminhtml/layout/productcomment_index_index.xml</span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
  <body>
    <referenceContainer name="content">
      <uiComponent name="product_comment_listing" />
    </referenceContainer>
  </body>
</page>

```

The UI component <span class="inlinecode">product_comment_listing</span> must be defined separately in a file with the same name, ending with <span class="inlinecode">.xml</span> - <span class="inlinecode">view/adminhtml/ui_component/product_comment_listing.xml</span>:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<listing xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Ui:etc/ui_configuration.xsd">
  <argument name="data" xsi:type="array">
    <item name="js_config" xsi:type="array">
      <item name="provider" xsi:type="string">product_comment_listing.product_comment_listing_data_source</item>
      <item name="deps" xsi:type="string">product_comment_listing.product_comment_listing_data_source</item>
    </item>
    <item name="spinner" xsi:type="string">product_comment_grid_columns</item>
    <item name="buttons" xsi:type="array">
      ...
      <!--   add Buttons  -->
    </item>
  </argument>
  <dataSource name="product_comment_listing_data_source">
    <argument name="dataProvider" xsi:type="configurableObject">
      <argument name="class" xsi:type="string">SMG\RestApiProductComment\Ui\DataProvider\ListingDataProvider</argument>
      <argument name="name" xsi:type="string">product_comment_listing_data_source</argument>
      <argument name="primaryFieldName" xsi:type="string">comment_id</argument>
      <argument name="requestFieldName" xsi:type="string">comment_id</argument>
      <argument name="data" xsi:type="array">
        <item name="config" xsi:type="array">
          <item name="update_url" xsi:type="url" path="mui/index/render" />
          <item name="storageConfig" xsi:type="array">
            <item name="indexField" xsi:type="string">comment_id</item>
          </item>
        </item>
      </argument>
    </argument>
    <argument name="data" xsi:type="array">
      <item name="js_config" xsi:type="array">
        <item name="component" xsi:type="string">Magento_Ui/js/grid/provider</item>
      </item>
    </argument>
  </dataSource>
  <listingToolbar name="listing_top">
    <!-- mass actions and filters are configure here -->
  </listingToolbar>
  <columns name="product_comment_grid_columns">
    ...
  </columns>
</listing>
```

This file consists of several sections:

- **dataSource** - references the class that is responsible for getting the requested data.
- **listingToolbar** - where mass actions and filters are defined.
- **columns** - lists the columns to be displayed


#### 2: Define The Data Source Class {#define-the-data-source-class}
The UI references <span class="inlinecode">SMG\RestApiProductComment\Ui\DataProvider\ListingDataProvider</span> as the data source class. The corresponding file is <span class="inlinecode">app/code/SMG/RestApiProductComment/Ui/DataProvider/ListingDataProvider.php</span>

```php
<?php
namespace SMG\RestApiProductComment\Ui\DataProvider;
use Magento\Framework\View\Element\UiComponent\DataProvider\DataProvider;
class ListingDataProvider extends DataProvider
{
}
```

#### 3: Data Source Collection {#data-source-collection}
The dataSource name <span class="inlinecode">product_comment_listing_data_source</span> links to <span class="inlinecode">SMG\RestApiProductComment\Ui\DataProvider\Listing\Collection</span> in 
<span class="inlinecode">app/code/SMG/RestApiProductComment/etc/di.xml</span>.

<span class="inlinecode">di.xml</span> also sets the <span class="inlinecode">mainTable</span> and <span class="inlinecode">resourceModel</span>:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
<type name="Magento\Framework\View\Element\UiComponent\DataProvider\CollectionFactory">
    <arguments>
    <argument name="collections" xsi:type="array">
        <item name="product_comment_listing_data_source" xsi:type="string">ProductCommentCollection</item>
    </argument>
    </arguments>
</type>
<virtualType name="ProductCommentCollection" type="SMG\RestApiProductComment\Ui\DataProvider\Listing\Collection">
    <arguments>
        <argument name="mainTable" xsi:type="string">smg_product_comment</argument>
        <argument name="resourceModel" xsi:type="string">SMG\RestApiProductComment\Model\ResourceModel\ProductComment</argument>
    </arguments>
</virtualType>
```

The collection class translates to
<span class="inlinecode">app/code/SMG/RestApiProductComment/Ui/DataProvider/Listing/Collection.php</span>

```php
<?php
namespace SMG\RestApiProductComment\Ui\DataProvider\Listing;
use Magento\Framework\View\Element\UiComponent\DataProvider\SearchResult;
class Collection extends SearchResult
{
}
```


#### 4: Column Actions Class {#column-actions-class}
The UI grid file defines a column actions class <span class="inlinecode">SMG\RestApiProductComment\Ui\Component\Listing\Column\Actions</span>. The corresponding file is <span class="inlinecode">app/code/SMG/RestApiProductComment/Ui/Component/Listing/Column/Actions.php</span>

```php
<?php
namespace SMG\RestApiProductComment\Ui\Component\Listing\Column;

class Actions extends Column
{
    const URL_PATH_EDIT = 'productcomment/index/edit';
    const URL_PATH_DELETE = 'productcomment/index/delete';
    public function __construct(
        ContextInterface $context,
        UiComponentFactory $uiComponentFactory,
        UrlInterface $urlBuilder,
        $viewUrl = '',
        array $components = [],
        array $data = []
    ) {
        $this->_urlBuilder = $urlBuilder;
        $this->_viewUrl    = $viewUrl;
        parent::__construct($context, $uiComponentFactory, $components, $data);
    }

    public function prepareDataSource(array $dataSource)
    {
        if (isset($dataSource['data']['items'])) {
            foreach ($dataSource['data']['items'] as &$item) {
                if (isset($item['comment_id'])) {
                    $item[$this->getData('name')] = [
                        'edit' => [
                            'href' => $this->_urlBuilder->getUrl(
                                static::URL_PATH_EDIT,
                                [
                                    'id' => $item['comment_id'],
                                ]
                            ),
                            'label' => __('Edit'),
                        ],
                        'delete' => [
                            'href' => $this->_urlBuilder->getUrl(
                                static::URL_PATH_DELETE,
                                [
                                    'id' => $item['comment_id'],
                                ]
                            ),
                            'label' => __('Delete'),
                            'confirm' => [
                                'title' => __('Delete Record ?'),
                                'message' => __('Are you sure you wan\'t to delete a record?'),
                            ],
                            'post' => true
                        ],
                    ];
                }
            }
        }
        return $dataSource;
    }
}
```

#### 5: Backend Controllers {#backend-controllers}
The main route defined in <span class="inlinecode">app/code/SMG/RestApiProductComment/etc/adminhtml/menu.xml</span> as <span class="inlinecode">productcomment/index/index</span> translates to <span class="inlinecode">app/code/SMG/RestApiProductComment/Controller/Adminhtml/Index/Index.php</span>:

```php
<?php
namespace SMG\RestApiProductComment\Controller\Adminhtml\Index;

class Index extends Action implements HttpGetActionInterface
{
    public function __construct( Context $context, PageFactory $rawFactory ) 
    {
        $this->pageFactory = $rawFactory;
        parent::__construct($context);
    }
    public function execute(): Page
    {
        $resultPage = $this->pageFactory->create();
        $resultPage->setActiveMenu('SMG_RestApiProductComment::productcomment');
        $resultPage->getConfig()->getTitle()->prepend(__('Comments'));
        return $resultPage;
    }
}
```
The Ui grid file defines the custom route <span class="inlinecode">productcomment/index/massDelete</span> (mass delete) and translates to <span class="inlinecode">app/code/SMG/RestApiProductComment/Controller/Adminhtml/Index/MassDelete.php</span>:

```php
<?php
namespace SMG\RestApiProductComment\Controller\Adminhtml\Index;

use SMG\RestApiProductComment\Model\ResourceModel\ProductComment\CollectionFactory;
use SMG\RestApiProductComment\Api\ProductCommentRepositoryInterface;

class MassDelete extends Action implements HttpPostActionInterface
{
    const ADMIN_RESOURCE = 'SMG_RestApiProductComment::productcomment';
    public function __construct(
        Context $context,
        Filter $filter,
        CollectionFactory $collectionFactory,
        PoroductCommentRepositoryInterface $productCommentRepository
    ) {
        $this->filter                   = $filter;
        $this->collectionFactory        = $collectionFactory;
        $this->productCommentRepository = $productCommentRepository;
        parent::__construct($context);
    }
    public function execute(): Redirect
    {
        if (!$this->getRequest()->isPost()) {
            throw new NotFoundException(__('Page not found'));
        }
        $collection = $this->filter->getCollection($this->collectionFactory->create());
        $pCommentDeleted = 0;
        foreach ($collection->getItems() as $productComment) {
            $this->productCommentRepository->delete($productComment);
            $pCommentDeleted++;
        }
        if ($categorpCommentDeletedyDeleted) {
            $this->messageManager->addSuccessMessage(
                __('A total of %1 record(s) have been deleted.', $pCommentDeleted)
            );
        }
        return $this->resultFactory->create(ResultFactory::TYPE_REDIRECT)->setPath('productcomment/index/index');
    }
}
```
#### Completed extension
---
Above mentioned classes and files are used for demonstration purpose. The complete extension can be found on GitHub at [Admin Grid Example Extension](https://github.com/supravatm/magento2-sample-modules/tree/master/09.product-comment-module-with-api)
