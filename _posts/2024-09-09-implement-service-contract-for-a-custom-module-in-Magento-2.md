---
layout: default
title:  Implement service contract for a custom module in Magento 2
permalink: /implement-service-contract-for-a-custom-module-in-Magento-2.html
minutes: 5
---
**[Home](https://supravatm.github.io/) >> [Blog](/blogs.html)**

##  {{ page.title }}

<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> {{ page.minutes }} min read
</small>

#### Create the repository and data model interfaces:
<p>Create the folders <span class="inlinecode">Api/Data/</span> in your module. This is just convention, you could use a different location, but you should not. The repository goes into the <span class="inlinecode">Api/</span> folder. The <span class="inlinecode">Data/</span> subdirectory is for later.</p>

<p>In <span class="inlinecode">Api/</span>, create a PHP interface with the methods you want to expose. According to Magento 2 conventions all interface names end in the suffix Interface.</p>

<p>For example, for a <span class="inlinecode">Post</span> entity, I would create the interface <span class="inlinecode">Api/PostRepositoryInterface</span>.</p>

<p>Create the repository interface Magento 2 repositories are part of the domain logic of a module. That means, there is no fixed set of methods a repository has to implement. It depends entirely on the purpose of the module.</p>

<p>However, in practice all repositories are quite similar. They are wrappers for CRUD functionality.
Most have the methods <span class="inlinecode">getById</span>, <span class="inlinecode">save</span>, <span class="inlinecode">delete</span> and <span class="inlinecode">getList</span>.
There may be more, for example the CustomerRepository has a method get, which fetches a customer by email, whereby <span class="inlinecode">getById</span> is used to retrieve a customer by entity ID.</p>

**Repository interface**:

<p>Here is an example repository interface for a Post entity:</p>

```php
<?php
namespace SMG\Blog\Api;
use Magento\Framework\Api\SearchCriteriaInterface;
use SMG\Blog\Api\Data\PostInterface;
interface PostRepositoryInterface
{
    /**
    * @param int $id
    * @return \SMG\Blog\Api\Data\PostInterface
    * @throws \Magento\Framework\Exception\NoSuchEntityException
    */
    public function getById($id);
    /**
    * @param \SMG\Blog\Api\Data\PostInterface $post
    * @return \S\Blog\Api\Data\PostInterface
    */
    public function save(PostInterface $post);
    /**
    * @param \SMG\Blog\Api\Data\PostInterface $post
    * @return void
    */
    public function delete(PostInterface $post);
    /**
    * @param \Magento\Framework\Api\SearchCriteriaInterface $searchCriteria
    * @return \SMG\Blog\Api\Data\PostSearchResultInterface
    */
    public function getList(SearchCriteriaInterface $searchCriteria);
}
?>
```
**Data Model or DTO interface**:

```php
<?php
namespace SMG\Blog\Api\Data;
use Magento\Framework\Api\ExtensibleDataInterface;
interface PostInterface extends ExtensibleDataInterface
{
    /**
    * @return int|null
    */
    public function getId();
    /**
    * @return string|null
    */
    public function getTitle();
    public function getExtensionAttributes();
    /**
    * @param \SMG\Blog\Api\Data\PostExtensionInterface $extensionAttributes
    * @return void
    */
    public function setExtensionAttributes(PostExtensionInterface $extensionAttributes);
}
?>
```
**ExtensibleDataInterface?**
<p>In the example above the <span class="inlinecode">PostInterface</span> extends the <span class="inlinecode">ExtensibleDataInterface</span>.Technically this is only required if you want other modules to be able to add attributes to your entity.</p>

<p> If you do not want your entity to be extensible, then the DTO interface does not have to extend any other interface, and the <span class="inlinecode">getExtensionAttributes()</span> and <span class="inlinecode">setExtensionAttributes()</span> methods can be omitted.</p>


**Search result interface**

<p>Here is the example post search result interface:</p>

```php
<?php
namespace SMG\Blog\Api\Data;
use Magento\Framework\Api\SearchResultsInterface;
/**
* @api
*/
interface PostSearchResultsInterface extends SearchResultsInterface
{
    /**
    * @return \SMG\Blog\Api\Data\PostInterface[]
    */
    public function getItems();
    /**
    * @param \SMG\Blog\Api\Data\PostInterface[] $items
    * @return $this
    */
    public function setItems(array $items);
}
```
#### Create the repository and data model implementations:

<p>The next step is to create the implementations of the three interfaces.</p>

**The Repository**

<p>in essence, the repository uses the ORM to do it's job. </p>

```php
<?php
namespace SMG\BLog\Model;
use SMG\BLog\Api\PostRepositoryInterface;
use SMG\BLog\Api\Data\PostInterface;
use SMG\BLog\Model\PostFactory;
use SMG\BLog\Model\ResourceModel\Item\CollectionFactory;
use SMG\BLog\Model\ResourceModel\Item as ObjectResourceModel;
use Magento\Framework\Api\SearchCriteriaInterface;
use Magento\Framework\Exception\CouldNotSaveException;
use Magento\Framework\Exception\NoSuchEntityException;
use Magento\Framework\Exception\CouldNotDeleteException;
use Magento\Framework\Api\SortOrder;
use Magento\Framework\Api\SearchResultsInterfaceFactory;

class CustomRepository implements CustomRepositoryInterface
{
    protected $postFactory;
    protected $objectResourceModel;
    protected $collectionFactory;
    protected $searchResultsFactory;
    public function __construct(
        PostFactory $postFactory,
        ObjectResourceModel $objectResourceModel,
        CollectionFactory $collectionFactory,
        SearchResultsInterfaceFactory $searchResultsFactory
    ) {
        $this->postFactory        = $postFactory;
        $this->objectResourceModel  = $objectResourceModel;
        $this->collectionFactory    = $collectionFactory;
        $this->searchResultsFactory = $searchResultsFactory;
    }
    public function save(PostInterface $object)
    {
        try {
            $this->objectResourceModel->save($object);
        } catch (Exception $e) {
            throw new CouldNotSaveException(__($e->getMessage()));
        }
        return $object;
    }
    /**
    * @inheritdoc
    */
    public function getById($id)
    {
        $object = $this->postFactory->create();
        $this->objectResourceModel->load($object, $id);
        if (!$object->getId()) {
            throw new NoSuchEntityException(__('Object with id "%1" does not exist.', $id));
        }
        return $object;
    }
    public function delete(CustomInterface $object)
    {
        try {
            $this->objectResourceModel->delete($object);
        } catch (Exception $exception) {
            throw new CouldNotDeleteException(__($exception->getMessage()));
        }
        return true;
    }
    public function deleteById($id)
    {
        return $this->delete($this->getById($id));
    }
    /**
    * @inheritdoc
    */
    public function getList(SearchCriteriaInterface $criteria)
    {
        $searchResults = $this->searchResultsFactory->create();
        $searchResults->setSearchCriteria($criteria);
        $collection = $this->collectionFactory->create();
        foreach ($criteria->getFilterGroups() as $filterGroup) {
            $fields = [];
            $conditions = [];
            foreach ($filterGroup->getFilters() as $filter) {
                $condition = $filter->getConditionType() ? $filter->getConditionType() : 'eq';
                $fields[] = $filter->getField();
                $conditions[] = [$condition => $filter->getValue()];
            }
            if ($fields) {
                $collection->addFieldToFilter($fields, $conditions);
            }
        }
        $searchResults->setTotalCount($collection->getSize());
        $sortOrders = $criteria->getSortOrders();
        if ($sortOrders) {
            /** @var SortOrder $sortOrder */
            foreach ($sortOrders as $sortOrder) {
                $collection->addOrder(
                    $sortOrder->getField(),
                    ($sortOrder->getDirection() == SortOrder::SORT_ASC) ? 'ASC' : 'DESC'
                );
            }
        }
        $collection->setCurPage($criteria->getCurrentPage());
        $collection->setPageSize($criteria->getPageSize());
        $objects = [];
        foreach ($collection as $objectModel) {
            $objects[] = $objectModel;
        }
        $searchResults->setItems($objects);
        return $searchResults;
    }
}
?>
```

**The Model**

<p>the example <span class="inlinecode">PostInterface</span> extends the <span class="inlinecode">ExtensibleDataInterface</span> the <span class="inlinecode">post</span> model extends the <span class="inlinecode">AbstractExtensibleModel</span>, as can be seen here:</p>

```php
<?php
namespace SMG\Blog\Model;
use Magento\Framework\Model\AbstractModel; 
use Magento\Framework\Model\AbstractExtensibleModel;
use SMG\Blog\Api\Data\PostExtensionInterface;
use SMG\Blog\Api\Data\PostInterface;
class Post extends AbstractExtensibleModel implements PostInterface
{
    protected function _construct()
    {
        $this->_init(ResourceModel\Post::class);
    }
    /**
    * @return int
    */
    public function getId()
    {
        return $this->getData(PostInterface::POST_ID);
    }
    /**
    * @return string
    */
    public function getTitle()
    {
        return $this->getData(PostInterface::TITLE);
    }
    /**
    * @return string
    */
    public function getContent()
    {
        return $this->getData(PostInterface::CONTENT);
    }

    /**
    * @return string
    */
    public function getCreationTime()
    {
        return $this->getData(PostInterface::CREATION_TIME);
    }
    /**
    * @return string
    */
    public function getUpdateTime()
    {
        return $this->getData(PostInterface::UPDATE_TIME);
    }
    /**
    * @return bool
    */
    public function isActive()
    {
        return (bool)$this->getData(PostInterface::IS_ACTIVE);
    }
    /**
    * @param int $id
    * @return PostInterface
    */
    public function setId($id)
    {
        return $this->setData(PostInterface::BLOCK_ID, $id);
    }
    /**
    * @param string $title
    * @return PostInterface
    */
    public function setTitle($title)
    {
        return $this->setData(PostInterface::TITLE, $title);
    }
    /**
    * @param string $content
    * @return PostInterface
    */
    public function setContent($content)
    {
        return $this->setData(PostInterface::CONTENT, $content);
    }
    /**
    * @param string $creationTime
    * @return PostInterface
    */
    public function setCreationTime($creationTime)
    {
        return $this->setData(PostInterface::CREATION_TIME, $creationTime);
    }
    /**
    * @param string $updateTime
    * @return PostInterface
    */
    public function setUpdateTime($updateTime)
    {
        return $this->setData(PostInterface::UPDATE_TIME, $updateTime);
    }
    /**
    * @param bool|int $isActive
    * @return PostInterface
    */
    public function setIsActive($isActive)
    {
        return $this->setData(PostInterface::IS_ACTIVE, $isActive);
    }
    public function getExtensionAttributes()
    {
        return $this->getExtensionAttributes();
    }
    public function setExtensionAttributes(PostExtensionInterface $extensionAttributes)
    {
        $this->setExtensionAttributes($extensionAttributes);
    }
}
?>
```

**The SearchResult**
<p>The <span class="inlinecode">SearchResultsInterface</span> is the simplest of the three interfaces to implement, since it can inherit all of it's functionality from a framework class.</p>

```php
    <?php
    namespace SMG\Blog\Model;
    use SMG\Blog\Api\Data\PostSearchResultsInterface;
    use Magento\Framework\Api\SearchResults;
    class PostSearchResults extends SearchResults implements PostSearchResultsInterface
    {
    }
    ?>
```
### Configure the ObjectManager preferences:

<p> We need to add an <span class="inlinecode">etc/di.xml</span> configuration for with the</p>

```xml
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <preference for="SMG\Blog\Api\PostRepositoryInterface" type="SMG\Blog\Model\PostRepository" />
    <preference for="SMG\Blog\Api\Data\PostInterface" type="SMG\Blog\Model\Post" />
    <preference for="SMG\Blog\Api\Data\PostSearchResult" type="SMG\Blog\Model\PostSearchResult" />
</config>
```

### The repository be exposed as an API resource:

<p>All we need to do is create an <span class="inlinecode">etc/webapi.xml</span> file.</p>

```xml
<?xml version="1.0"?>
<routes xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Webapi:etc/webapi.xsd">
    <route method="GET" url="/V1/smg_post/:id">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="getById"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
    <route method="GET" url="/V1/smg_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="getList"/>
        <resources>
            <resource ref="anonymouns"/>
        </resources>
    </route>
    <route method="POST" url="/V1/smg_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="save"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
    <route method="PUT" url="/V1/smg_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="save"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
    <route method="DELETE" url="/V1/smg_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="delete"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
</routes>
```


<p>For this example I've set the accessability to <span class="inlinecode">&lt;resource ref="anonymous"&#47;&gt;</span>. This means the resource is exposed publically without any restriction! </p>


### Inspired by:
[magento.stackexchange](https://magento.stackexchange.com/questions/115269/how-to-implement-service-contract-for-a-custom-module-in-magento-2")

[APIs and Service Contracts blog](https://meetmagentoacademy.github.io/magento2-training-resources/backend/modules/service_contracts.html")

> Download the complete code from [Github](https://github.com/supravatm/magento2-sample-modules/tree/master/05.smaple-service-contract-with-rest-api) .