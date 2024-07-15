---
layout: default
title: How to call Owl Slider in Magento2
permalink: /how-to-call-owl-slider-in-magento2.html
---
**[Home](https://supravatm.github.io/) >> [Blog](https://supravatm.github.io/blogs.html) >> How to call Owl Slider in Magento2**

##### How to call Owl Slider in Magento2

We will learn how to integrate owl carousel slider in Magento 2 for product listings.

You have to created one **requirejs-config.js** file inside your theme,

First Add **owlcarousel.js** file inside,

```
app/design/frontend/pakage_name/theme_name/Magento_Theme/web/js
```

Add your css inside,

```
app/design/frontend/pakage_name/theme_name/Magento_Theme/web/css
```
Call css inside your tempalte file using,

```javascript
<link rel="stylesheet" type="text/css" href="<?php echo $block->getViewFileUrl('Magento_Catalog::css/owlcarousel.css')?>">
```

Now create **requirejs-config.js** file

```
app/design/frontend/pakage_name/theme_name/Magento_Theme/requirejs-config.js
```

Define your slider,

```javascript
var config = {
    paths: {            
            'owlcarousel': "Magento_Catalog/js/owlcarousel"
        },   
    shim: {
        'owlcarousel': {
            deps: ['jquery']
        }
    }
};
```


Now you can use owlcarousel under any phtml file,

```html
<div class="products list items product-items owlslider">
   <ul>
     <li>Item 1</li>
     <li>Item 2</li>
     <li>Item 3</li>
     <li>Item 4</li>
     <li>Item 5</li>       
  </ul>
</div>
```

And add javascript in bootom of the same page

```javascript
(function  () {
    require(["jquery","owlcarousel"],function($) {
        $(document).ready(function() {
            $("#owlslider").owlCarousel({
                navigation : true, // Show next and prev buttons
                autoPlay: false, //Set AutoPlay to 3 seconds
                items : 5
            });
        });
    });
})();
```

#### Note:

> Remove **pub/static** folder content and run following command from terminal <mark>php bin/magento setup:static-content:deploy</mark> .
