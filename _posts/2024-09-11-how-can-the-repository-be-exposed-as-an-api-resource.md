---
layout: default
title:  How can the repository be exposed as an API resource
permalink: /how-can-the-repository-be-exposed-as-an-api-resource.html
---
**[Home](https://supravatm.github.io/) >> [Blog](https://supravatm.github.io/blogs.html) >> How can the repository be exposed as an API resource**

## How can the repository be exposed as an API resource
<small>
    <i class="fa-regular fa-calendar"></i> {{ page.date | date: "%b %-d, %Y" }}  &nbsp; &nbsp;
    <i class="fa-regular fa-clock"></i> 3 min read
</small>


<p>This part is really simple, it's the reward for going through all the work creating the interfaces, the implementations and wiring them together.</p>

<p>All we need to do is create an <code>etc/webapi.xm</code> file.  </p>


    <?xml version="1.0"?>
    <routes xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Webapi:etc/webapi.xsd">
        <route method="GET" url="/V1/vinaikopp_hamburgers/:id">
            <service class="VinaiKopp\Kitchen\Api\HamburgerRepositoryInterface" method="getById"/>
            <resources>
                <resource ref="anonymous"/>
            </resources>
        </route>
        <route method="GET" url="/V1/vinaikopp_hamburgers">
            <service class="VinaiKopp\Kitchen\Api\HamburgerRepositoryInterface" method="getList"/>
            <resources>
                <resource ref="anonymouns"/>
            </resources>
        </route>
        <route method="POST" url="/V1/vinaikopp_hamburgers">
            <service class="VinaiKopp\Kitchen\Api\HamburgerRepositoryInterface" method="save"/>
            <resources>
                <resource ref="anonymous"/>
            </resources>
        </route>
        <route method="PUT" url="/V1/vinaikopp_hamburgers">
            <service class="VinaiKopp\Kitchen\Api\HamburgerRepositoryInterface" method="save"/>
            <resources>
                <resource ref="anonymous"/>
            </resources>
        </route>
        <route method="DELETE" url="/V1/vinaikopp_hamburgers">
            <service class="VinaiKopp\Kitchen\Api\HamburgerRepositoryInterface" method="delete"/>
            <resources>
                <resource ref="anonymous"/>
            </resources>
        </route>
    </routes>

Note that this configuration not only enables the use of the repository as REST endpoints, it also exposes the methods as part of the SOAP API.

In the first example route, ```<route method="GET" url="/V1/vinaikopp_hamburgers/:id"> ```, the placeholder ```:id``` has to match the name of the argument to the mapped method, public function ```getById($id)```.
The two names have to match, for example ```/V1/vinaikopp_hamburgers/:hamburgerId``` would not work, since the method argument variable name is ```$id```.

For this example I've set the accessability to ```<resource ref="anonymous"/>```. This means the resource is exposed publically without any restriction!
To make a resource only available to a logged in customer, use ```<resource ref="self"/>```. In this case the special word me in the resource endpoint URL will be used to populate an argument variable ```$id``` with the ID of the currently logged in customer.
Have a look at the Magento Customer  ```etc/webapi.xml``` and ```CustomerRepositoryInterface``` if you need that.

Finally, the ```<resources>``` can also be used to restrict access to a resource to an admin user account. To do this set the ```<resource>``` ref to an identifier defined in an ```etc/acl.xml``` file.
For example, ```<resource ref="Magento_Customer::manage"/>``` would restrict access to any admin account who is privileged to manage customers.

<p>An example API query using curl could look like this:  </p>

    $ curl -X GET http://example.com/rest/V1/vinaikopp_hamburgers/123

https://github.com/supravatm/magento2-sample-modules/tree/master/05.smaple-service-contract-with-rest-api
### Inspired by:
[magento.stackexchange](https://magento.stackexchange.com/questions/115269/how-to-implement-service-contract-for-a-custom-module-in-magento-2")

[APIs and Service Contracts blog](https://meetmagentoacademy.github.io/magento2-training-resources/backend/modules/service_contracts.html")


> Download the complete code from [Github](https://github.com/supravatm/m2-sample/tree/master/admin-crud-module) .