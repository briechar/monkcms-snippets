# class Church

Provides data and methods for implementing a multi-campus site. Using the _Churches_ module in Ekklesia 360 and the _Content_ class, we build a data model of a `Church` which contains multiple campuses. After instantiating a `Church` object, we can access its data.

```
$Church = new Church();
```

Optionally, we can pass in the slug of a default campus, which would be considered the "current" campus when the cookie is not set.

```
$Church = new Church('north-campus');
```

The slug of the current campus can be accessed with:

```
$Church->campus['slug'];
```

An array containing all campuses can be accessed with:

```
$Church->campuses;
```

This array looks like:

```
Array
(
    [north-campus] => Array
        (
            [slug] => north-campus
            [name] => North Campus
            [description] => Come join us for our Easter Sunrise Service at 6:00 am!
            [customhomepageid] => 603465
        )
    [east-campus] => Array
        (
            [slug] => east-campus
            [name] => East Campus
            [description] => We are a community of believers seeking to build God's kingdom here in East County.
            [customhomepageid] => 465712
        )
)
```

To avoid necessitating multiple API calls for the campus data throughout a page, both the `campus` and `campuses` properties are filled with data from the API call only once - when the `$Church` object is created. Therefore, it's usually not necessary to use `getCampus()` or `getCampuses()` in a site template.

## Building a campus selector

The query string parameter `setCampus` is checked when the `Church` object is created. If the provided campus exists, we'll set the cookie and redirect to the campus homepage. Implementing a working campus selector then, involves creating a list of links using `href="?setCampus=the-campus-slug"`.

```
<ul class="campus-selector">
<?php

  foreach ($Church->campuses as $c) {
    $output  = '<li class="campus">';
    $output .= '<a href="?setCampus=' . $c['slug'] . '">';
    $output .= '<span class="icon-pin"></span>';
    $output .= $c['name'];
    $output .= '</a>';
    $output .= '</li>' . "\n";
    echo $output;
  }

?>
</ul>
```

## Methods

### getCampuses()

_Private._ Makes an API request for all campuses using `getContent()`. 

### getCampus()

_Public._ Get the current campus from the cookie. If `$default == true`, the default campus will be returned if no cookie is set.

### setCampus()

_Public._ Set the desired campus in a cookie.

### setCampusAndRedirect()

_Public._ Set the campus with `setCampus()` and also redirect to the campus homepage. Gets the campus homepage URL based on the Page ID of the campus landing page (custom field needed in Ekklesia).

### getCampusCSS()

_Public._ Get the path to the "override" CSS file for the current campus. If for any reason the campus-specific override file does not exist, a new one will be created from a fresh copy of "override.css", using permissions "775". Helpful when implementing Color Picker functionality for each campus.

## Working with Javascript
It's recommended to include some of the campus information as meta tags for the site, such as:

```
<meta name="campus" content="<?= $Church->campus ?>" />
```

This will allow you to access these values with Javascript/jQuery, like:
```
var campus = $('meta[name=campus]').attr('content');
```
