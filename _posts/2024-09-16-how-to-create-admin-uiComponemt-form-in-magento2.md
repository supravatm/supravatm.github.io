---
layout: artical
title:  How to Create An Admin uiComponemt Form In Magento 2
permalink: /how-to-create-admin-uiComponemt-form-in-magento2.html
---


#### Basic Usage of Form component

The Form component is a collection of fields that can be grouped in tabs and fieldsets. It enables CRUD operations.

#### To Create Form component

- [1: Define the Form](#define-the-form)
- [2: Define the Data Source](#define-the-data-source)
- [3: Button Actions Classes](#button-actions-classes)
- [4: Backend Controllers](#backend-controllers)


#### 1: Define the Form {#define-the-form}

The page layout file is <span class="inlinecode">Dev/Form/view/adminhtml/layout/dev_form_index_edit.xml</span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
  xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
  <body>
    <referenceContainer name="content">
      <uiComponent name="dev_edit_form" />
    </referenceContainer>
  </body>
```
The UI component <span class="inlinecode">dev_edit_form</span> must be defined separately in a file with the same name, ending with <span class="inlinecode">.xml</span> - <span class="inlinecode">Dev/Form/view/adminhtml/ui_component/dev_edit_form.xml</span>:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<form xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Ui:etc/ui_configuration.xsd">
    <argument name="data" xsi:type="array">
        <item name="js_config" xsi:type="array">
            <item name="provider" xsi:type="string">dev_edit_form.dev_edit_form_data_source</item>
            <item name="deps" xsi:type="string">dev_edit_form.dev_edit_form_data_source</item>
        </item>
        <item name="label" xsi:type="string" translate="true">Form Information</item>
        <item name="config" xsi:type="array">
            <item name="dataScope" xsi:type="string">data</item>
            <item name="namespace" xsi:type="string">dev_edit_form</item>
        </item>
        <item name="template" xsi:type="string">templates/form/collapsible</item>
    </argument>
    <settings>
        <buttons>
            <button name="save" class="Dev\Form\Block\Adminhtml\Comment\SaveButton"/>
            <button name="delete" class="Dev\Form\Block\Adminhtml\Block\Edit\DeleteButton"/>
            <button name="back" class="Dev\Form\Block\Adminhtml\Block\Edit\BackButton"/>
        </buttons>
        <dataScope>data</dataScope>
        <deps>
            <dep>dev_edit_form.dev_edit_form_data_source</dep>
        </deps>
    </settings>
    <dataSource name="dev_edit_form_data_source">
        <argument name="data" xsi:type="array">
            <item name="js_config" xsi:type="array">
                <item name="component" xsi:type="string">Magento_Ui/js/form/provider</item>
            </item>
        </argument>
        <settings>
            <submitUrl path="*/*/save"/>
        </settings>
        <dataProvider class="Dev\Form\Ui\DataProvider\EditFormDataProvider" name="dev_edit_form_data_source">
            <settings>
                <requestFieldName>entity_id</requestFieldName>
                <primaryFieldName>entity_id</primaryFieldName>
            </settings>
        </dataProvider>
    </dataSource>
    <fieldset name="general">
    <!-- Hide Fieldset header header-->
      <settings>
            <label/>
      </settings>
        <field name="entity_id" formElement="input">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="source" xsi:type="string">data</item>
                </item>
            </argument>
            <settings>
                <dataType>text</dataType>
                <visible>false</visible>
                <dataScope>entity_id</dataScope>
            </settings>
        </field>
        <field name="title" sortOrder="10" formElement="input">
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="source" xsi:type="string">data</item>
                </item>
            </argument>
            <settings>
                <validation>
                    <rule name="required-entry" xsi:type="boolean">true</rule>
                </validation>
                <dataType>text</dataType>
                <label translate="true">Comment Title</label>
                <dataScope>title</dataScope>
            </settings>
        </field>
        <field name="comment" sortOrder="20" >
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="formElement" xsi:type="string">textarea</item>
                    <item name="cols" xsi:type="number">15</item>
                    <item name="rows" xsi:type="number">5</item>
                    <item name="label" translate="true" xsi:type="string">Comment Details</item>
                    <item name="dataType" translate="true" xsi:type="string">text</item>
                </item>
            </argument>
        </field>
        <field name="status" formElement="select">
            <settings>
                <dataType>text</dataType>
                <label translate="true">Comment Status</label>
                <dataScope>status</dataScope>
            </settings>
            <formElements>
                <select>
                    <settings>
                        <options>
                            <option name="0" xsi:type="array">
                                <item name="value" xsi:type="string">0</item>
                                <item name="label" xsi:type="string">Pending</item>
                            </option>
                            <option name="1" xsi:type="array">
                                <item name="value" xsi:type="string">1</item>
                                <item name="label" xsi:type="string">Approve</item>
                            </option>
                            <option name="2" xsi:type="array">
                                <item name="value" xsi:type="string">2</item>
                                <item name="label" xsi:type="string">Reject</item>
                            </option>
                        </options>
                        <caption translate="true">-- Please Select --</caption>
                    </settings>
                </select>
            </formElements>
        </field>
    </fieldset>
</form>
```

This file consists of several sections:

- <span class="inlinecode">argument</span> - references the class that is responsible for getting the requested data.
- <span class="inlinecode">dataSource</span> - where DB record fetch for Form.
- <span class="inlinecode">fieldset</span> - set of form elements


#### 2: Define the Data Source {#define-the-data-source}

The UI references <span class="inlinecode">Dev\Form\Ui\DataProvider\EditFormDataProvider</span> as the data source class. The corresponding file is <span class="inlinecode">app/code/Dev/Form/Ui/DataProvider/ListingDataProvider.php</span>
```php
<?php
namespace Dev\Form\Ui\DataProvider;
use Dev\Form\Model\ResourceModel\ProductComment\CollectionFactory;
class EditFormDataProvider extends Magento\Ui\DataProvider\AbstractDataProvider
{
    protected $commentCollectionFactory;
    protected $_loadedData;
    public function __construct(
        $name,
        $primaryFieldName,
        $requestFieldName,
        CollectionFactory $commentCollectionFactory,
        array $meta = [],
        array $data = []
    ) {
        $this->collection = $commentCollectionFactory->create();
        parent::__construct($name, $primaryFieldName, $requestFieldName, $meta, $data);
    }
    public function getData()
    {
        if (isset($this->_loadedData)) {
            return $this->_loadedData;
        }
        $items = $this->collection->getItems();
        foreach ($items as $comment) {
            $this->_loadedData[$comment->getId()] = $comment->getData();
        }
        return $this->_loadedData;
    }
}
```

#### 3: Button Classes {#button-actions-classes}
The UI references <span class="inlinecode">Dev\Form\Block\Adminhtml\Comment\SaveButton</span>. The corresponding file is <span class="inlinecode">app/code/Dev/Form/Block/Adminhtml/Comment/SaveButton.php</span>

```php
namespace Dev\Form\Block\Adminhtml\Comment;
use Magento\Framework\View\Element\UiComponent\Control\ButtonProviderInterface;
class SaveButton implements ButtonProviderInterface
{
    public function getButtonData()
    {
        return [
            'label' => __('Save'),
            'class' => 'save primary',
            'data_attribute' => [
                'mage-init' => [
                    'buttonAdapter' => [
                        'actions' => [
                            [
                                'targetName' => 'dev_edit_form.dev_edit_form',
                                'actionName' => 'save',
                                'params' => [
                                    false,
                                ],
                            ],
                        ],
                    ],
                ],
            ],
        ];
    }
}
```

#### 4: Backend Controllers {#backend-controllers}

The main route defined in <span class="inlinecode">app/code/Dev/Form/etc/adminhtml/menu.xml</span> as <span class="inlinecode">devform/index/edit</span> translates to <span class="inlinecode">app/code/Dev/Form/Controller/Adminhtml/Index/Edit.php</span>:
```php
<?php

namespace Dev\Form\Controller\Adminhtml\Index;

use Magento\Backend\App\Action;
use Magento\Backend\App\Action\Context;
use Magento\Framework\View\Result\Page;
use Magento\Framework\View\Result\PageFactory;
use Magento\Framework\App\Action\HttpGetActionInterface;
use Magento\Framework\Registry;
use Dev\Form\Model\ProductCommentFactory as CommentFactory;

class Edit extends Action implements HttpGetActionInterface
{
    /**
     * Authorization level of a basic admin session
     *
     * @see _isAllowed()
     */
    const ADMIN_RESOURCE = 'Dev_Form::manager';

    /**
     * @var PageFactory
     */
    private $pageFactory;
    /**
     * Core registry
     *
     * @var \Magento\Framework\Registry
     */
    protected $_coreRegistry;
    /**
     * ProductCommentFactory
     *
     * @var Dev\Form\Model\ProductCommentFactory
     */
    protected $commentFactory;

    /**
     * Constructor
     *
     * @param Context $context
     * @param PageFactory $rawFactory
     */
    public function __construct(
        Context $context,
        PageFactory $rawFactory,
        Registry $coreRegistry,
        CommentFactory $commentFactory
    ) {
        $this->pageFactory = $rawFactory;
        $this->_coreRegistry = $coreRegistry;
        $this->commentFactory = $commentFactory;
        parent::__construct($context);
    }
    /**
     * Add the main Admin Form page
     * @return Page
     */
    public function execute(): Page
    {
        // 1. Get ID and create model
        $id = $this->getRequest()->getParam('comment_id');
        $model = $this->commentFactory->create();
        // 2. Initial checking
        if ($id) {
            $model->load($id);
            if (!$model->getId()) {
                $this->messageManager->addErrorMessage(__('This Comment no longer exists.'));
                /** @var \Magento\Backend\Model\View\Result\Redirect $resultRedirect */
                $resultRedirect = $this->resultRedirectFactory->create();
                return $resultRedirect->setPath('*/*/');
            }
        }
        $this->_coreRegistry->register('comment_data', $model);
        // 5. Build edit form
        /** @var \Magento\Backend\Model\View\Result\Page $resultPage */
        $resultPage = $this->pageFactory->create();
        $this->initPage($resultPage)->addBreadcrumb(
            $id ? __('Edit Information') : __('New Information'),
            $id ? __('Edit Information') : __('New Information')
        );
        $resultPage->getConfig()->getTitle()->prepend(__('Information'));
        $resultPage->getConfig()->getTitle()->prepend($model->getId() ? $model->getTitle() : __('New Information'));
        return $resultPage;
    }
    /**
     * Init page
     *
     * @param \Magento\Backend\Model\View\Result\Page $resultPage
     * @return \Magento\Backend\Model\View\Result\Page
     */
    protected function initPage($resultPage)
    {
        $resultPage->setActiveMenu('Dev_Form::manager')
            ->addBreadcrumb(__('Dev Form'), __('Dev Form'))
            ->addBreadcrumb(__('Form information'), __('Form Information'));
        return $resultPage;
    }
}
```

Also the main route defined in <span class="inlinecode">app/code/Dev/Form/etc/adminhtml/menu.xml</span> as <span class="inlinecode">devform/index/save</span> to save form data and translates to <span class="inlinecode">app/code/Dev/Form/Controller/Adminhtml/Index/Save.php</span>:

```php
<?php
namespace Dev\From\Controller\Adminhtml\Index;

use Magento\Framework\App\Action\HttpPostActionInterface;
use Magento\Backend\App\Action\Context;
use Dev\From\Api\ProductCommentRepositoryInterface;
use Dev\From\Model\Block;
use Dev\From\Model\ProductCommentFactory as CommentFactory;
use Magento\Framework\App\Request\DataPersistorInterface;
use Magento\Framework\Exception\LocalizedException;
use Magento\Framework\Registry;

/**
 * Save action.
 */
class Save extends  Magento\Backend\App\Action implements HttpPostActionInterface
{
    /**
     * @var DataPersistorInterface
     */
    protected $dataPersistor;

    /**
     * @var CommentFactory
     */
    private $commentFactory;

    /**
     * @var ProductCommentRepositoryInterface
     */
    private $commentRepository;

    /**
     * @param Context $context
     * @param Registry $coreRegistry
     * @param DataPersistorInterface $dataPersistor
     * @param CommentFactory|null $commentFactory
     * @param ProductCommentRepositoryInterface|null $commentRepository
     */
    public function __construct(
        Context $context,
        Registry $coreRegistry,
        DataPersistorInterface $dataPersistor,
        CommentFactory $commentFactory = null,
        ProductCommentRepositoryInterface $commentRepository = null
    ) {
        $this->dataPersistor = $dataPersistor;
        $this->commentFactory = $commentFactory
            ?: Magento\Framework\App\ObjectManager::getInstance()->get(commentFactory::class);
        $this->commentRepository = $commentRepository
            ?: Magento\Framework\App\ObjectManager::getInstance()->get(ProductCommentRepositoryInterface::class);
        parent::__construct($context, $coreRegistry);
    }

    /**
     * Save action
     *
     * @SuppressWarnings(PHPMD.CyclomaticComplexity)
     * @return \Magento\Framework\Controller\ResultInterface
     */
    public function execute()
    {
        /** @var \Magento\Backend\Model\View\Result\Redirect $resultRedirect */
        $resultRedirect = $this->resultRedirectFactory->create();
        $data = $this->getRequest()->getPostValue();
        if ($data) {
            if (isset($data['is_active']) && $data['is_active'] === 'true') {
                $data['is_active'] = Block::STATUS_ENABLED;
            }
            if (empty($data['comment_id'])) {
                $data['comment_id'] = null;
            }

            /** @var \Dev\From\Model\Block $model */
            $model = $this->commentFactory->create();

            $id = $this->getRequest()->getParam('comment_id');
            if ($id) {
                try {
                    $model = $this->commentRepository->getById($id);
                } catch (LocalizedException $e) {
                    $this->messageManager->addErrorMessage(__('This block no longer exists.'));
                    return $resultRedirect->setPath('*/*/');
                }
            }

            $model->setData($data);

            try {
                $this->commentRepository->save($model);
                $this->messageManager->addSuccessMessage(__('You saved the block.'));
                $this->dataPersistor->clear('product_comment');
                return $this->processBlockReturn($model, $data, $resultRedirect);
            } catch (LocalizedException $e) {
                $this->messageManager->addErrorMessage($e->getMessage());
            } catch (Exception $e) {
                $this->messageManager->addExceptionMessage($e, __('Something went wrong while saving the block.'));
            }

            $this->dataPersistor->set('product_comment', $data);
            return $resultRedirect->setPath('*/*/edit', ['comment_id' => $id]);
        }
        return $resultRedirect->setPath('*/*/');
    }

    /**
     * Process and set the block return
     *
     * @param \Dev\From\Model\ProductComment $model
     * @param array $data
     * @param \Magento\Framework\Controller\ResultInterface $resultRedirect
     * @return \Magento\Framework\Controller\ResultInterface
     */
    private function processBlockReturn($model, $data, $resultRedirect)
    {
        $redirect = $data['back'] ?? 'close';

        if ($redirect ==='continue') {
            $resultRedirect->setPath('*/*/edit', ['comment_id' => $model->getId()]);
        } elseif ($redirect === 'close') {
            $resultRedirect->setPath('*/*/');
        } elseif ($redirect === 'duplicate') {
            $duplicateModel = $this->commentFactory->create(['data' => $data]);
            $duplicateModel->setId(null);
            $duplicateModel->setIdentifier($data['identifier'] . '-' . uniqid());
            $duplicateModel->setIsActive(Block::STATUS_DISABLED);
            $this->commentRepository->save($duplicateModel);
            $id = $duplicateModel->getId();
            $this->messageManager->addSuccessMessage(__('You duplicated the block.'));
            $this->dataPersistor->set('product_comment', $data);
            $resultRedirect->setPath('*/*/edit', ['comment_id' => $id]);
        }
        return $resultRedirect;
    }
}
```

#### Completed extension
---
Above mentioned classes and files are used for demonstration purpose. The complete extension can be found on GitHub at [Admin Grid & Form Example Extension](https://github.com/supravatm/magento2-sample-modules/tree/master/13.admin-grid-and-form-component){:target="_blank"}
