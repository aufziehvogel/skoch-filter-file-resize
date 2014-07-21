Skoch_Filter_File_Resize
========================

Resize utility for the Zend Framework
-------------------------------------
`Skoch_Filter_File_Resize` is a utility for integrating image resizing into the Zend Framework's `Zend_Form` structure. It is implemented as a filter which you can attach to `Zend_Form_Element_File` instances.

Installation
------------

* Download the folder Skoch from github and add it to your library files
* Add `autoloaderNamespaces[] = "Skoch_"` to your `application.ini`

### OR with Composer

Add to your composer.json
(we need to change repository url from josecoelho to aufziehvogel after/if Pull request be Accepted)

```
    "repositories": [
        {
            "url": "https://github.com/josecoelho/skoch-filter-file-resize.git",
            "type": "git"
        }
    ],
    "require": {
        "aufziehvogel/skoch-filter-file-resize": "dev-master",
    },
```


Examples
--------
The folders `application` and `public` contain examples for some basic usage. The most interesting file is `application/forms/Image.php`.
You will need to have Zend-Framework installed to run the example.


Basic Usage
-----------

You can add the filter to your `Zend_Form_Element_File` instance.

```php
$photo->addFilter(new Skoch_Filter_File_Resize(array(
    'width' => 200,
    'height' => 300,
    'keepRatio' => true,
)));
```

This will scale the image to a maximum of 200px length and a maximum of 300px height. One of both sides will probably be a bit smaller, because we set **keepRatio** to `true` meaning that the aspect ratio (e.g. 3:4) shall be maintained. There also is a implicit defition of **keepSmaller** set to `true` meaning that if the image is already smaller, it shall not be enlarged (because that would lead to a lot of strange pixels).

Multiple thumbnails
-------------------
Often you want to create several thumbnails in different sizes. This can be done by using a so called **filter chain** and the **directory** option of the `Skoch_Filter_File_Resize`.

If you specify **directory**, the value of `setDestination()` will not be considered anymore. Thus, you have to pass the full path to the **directory** option.

```php
$filterChain = new Zend_Filter();
// Create one big image with at most 600x300 pixel
$filterChain->appendFilter(new Skoch_Filter_File_Resize(array(
    'width' => 600,
    'height' => 300,
    'keepRatio' => true,
)));
// Create a medium image with at most 500x200 pixels
$filterChain->appendFilter(new Skoch_Filter_File_Resize(array(
    'directory' => '/var/www/skoch/upload/medium',
    'width' => 500,
    'height' => 200,
    'keepRatio' => true,
)));
// Rename the file, of course this should not be a fixed string in real applications
$multiResize->addFilter('Rename', 'users_upload');
// Add the filter chain with both resize rules
$multiResize->addFilter($filterChain);
```

This will create two thumbnails, one of maximum 600px length and 300px height and the other of 500px length and 200px height. In each case, the aspect ratio will be kept. The smaller thumbnail will be saved to the folder `/var/www/skoch/upload/medium` while the larger one will use the default option set via Zend's `setDestination()` method.


Options / Arguments
-------------------
You may specify different options for the Resize Filter:

* width: The maximum width of the resized image
* height: The maximum height of the resized image
* keepRatio: Keep the aspect ratio and do not resize to both width and height (usually expected).
* keepSmaller: Do not resize if the image is already smaller than the given sizes.
* cropToFit: If the image dimensions do not match the given image, crop off some borders
* directory: Set a directory to store the thumbnail in. If nothing is given, the normal image will be overwritten.
* adapter: The adapter to use for resizing. You may specify a string or an instance of an adapter.


Further information
-------------------
You can find further information [in the article on my blog](http://eliteinformatiker.de/2011/09/02/thumbnails-upload-and-resize-images-with-zend_form_element_file/).
