---
layout: default
permalink: /how-to-call-owl-slider-in-magento2
---


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

{% highlight ruby %}
<link rel="stylesheet" type="text/css" href="<?php echo $block->getViewFileUrl('Magento_Catalog::css/owlcarousel.css')?>">
{% endhighlight %}

Now create **requirejs-config.js** file

```
app/design/frontend/pakage_name/theme_name/Magento_Theme/requirejs-config.js
```

Define your slider,

{% highlight ruby %}
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
{% endhighlight %}


Now you can use owlcarousel under any phtml file,

{% highlight ruby %}
<div class="products list items product-items owlslider">
   <ul>
     <li>1</li>
     <li>2</li>
     <li>3</li>
     <li>4</li>
     <li>5</li>       
  </ul>
</div>
{% endhighlight %}

And add javascript in bootom of the same page

{% highlight ruby %}
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
{% endhighlight %}

### Note:

Remove **pub/static** folder content and run following command from terminal ```php bin/magento setup:static-content:deploy ``` .
