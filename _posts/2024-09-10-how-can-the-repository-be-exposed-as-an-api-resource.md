---
layout: default
title:  How can the repository be exposed as an API resource
permalink: /how-can-the-repository-be-exposed-as-an-api-resource.html
---


This part is really simple, it's the reward for going through all the work creating the interfaces, the implementations and wiring them together.

All we need to do is create an <span class="inlinecode">etc/webapi.xm</span> file.

```xml
<?xml version="1.0"?>
<routes xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Webapi:etc/webapi.xsd">
    <route method="GET" url="/V1/blog_post/:id">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="getById"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
    <route method="GET" url="/V1/blog_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="getList"/>
        <resources>
            <resource ref="anonymouns"/>
        </resources>
    </route>
    <route method="POST" url="/V1/blog_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="save"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
    <route method="PUT" url="/V1/blog_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="save"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
    <route method="DELETE" url="/V1/blog_post">
        <service class="SMG\Blog\Api\PostRepositoryInterface" method="delete"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
</routes>
```

Note that this configuration not only enables the use of the repository as REST endpoints, it also exposes the methods as part of the SOAP API.

In the first example route, <span class="inlinecode">&lt;route method="GET" url="/V1/blog_post/:id" &#47;&gt;</span>, the placeholder <span class="inlinecode">:id</span> has to match the name of the argument to the mapped method, public function <span class="inlinecode">getById($id)</span>.
The two names have to match, for example <span class="inlinecode">/V1/blog_post/:postId</span> would not work, since the method argument variable name is <span class="inlinecode">$id</span>.

For this example I've set the accessability to <span class="inlinecode">&lt;resource ref="anonymous" &#47;&gt;</span>. This means the resource is exposed publically without any restriction!
To make a resource only available to a logged in customer, use <span class="inlinecode">&lt;resource ref="self" &#47;&gt;</span>. In this case the special word me in the resource endpoint URL will be used to populate an argument variable ```$id``` with the ID of the currently logged in customer.
Have a look at the Magento Customer  <span class="inlinecode">etc/webapi.xml</span> and <span class="inlinecode">CustomerRepositoryInterface</span> if you need that.

Finally, the <span class="inlinecode">resources</span> can also be used to restrict access to a resource to an admin user account. To do this set the <span class="inlinecode">&lt;resource&gt;</span> ref to an identifier defined in an <span class="inlinecode">etc/acl.xml</span> file.
For example, <span class="inlinecode">&lt;resource ref="Magento_Customer::manage"&#47;&gt;</span> would restrict access to any admin account who is privileged to manage customers.

An example API query using curl could look like this:

    $ curl -X GET http://example.com/rest/V1/blog_post/123

https://github.com/supravatm/magento2-sample-modules/tree/master/05.smaple-service-contract-with-rest-api
### Inspired by:
[magento.stackexchange](https://magento.stackexchange.com/questions/115269/how-to-implement-service-contract-for-a-custom-module-in-magento-2")

[APIs and Service Contracts blog](https://meetmagentoacademy.github.io/magento2-training-resources/backend/modules/service_contracts.html")


> Download the complete code from [Github](https://github.com/supravatm/magento2-sample-modules/tree/master/05.smaple-service-contract-with-rest-api) .
