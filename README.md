BULLETPROOF [![Build Status](https://travis-ci.org/samayo/bulletproof.svg?branch=master)](https://travis-ci.org/samayo/bulletproof.svg?branch=master)
----
A one-file class to **securely upload**, crop, resize and watermark images in PHP.


> enable the `php_exif` extension in your php.ini before using this class.

#### INSTALL
```json
{
    "require": {
        "bullet-proof/image-uploader": ">=1.0.0"
    }
}
```

#### SET UP
````php
// just require the class and create the object
require_once  "src\bulletproof.php";
$image = new ImageUploader\BulletProof;
````


#### (1) Upload with  default settings 
The below code will:
- Only upload images with (jpg, gif, png, jpeg) extension, 
- Only sizes ranging from 0.1kb to 30kb max
- Creates [if it doesn't exist] "uploads/" folder for storing the image 

````php 
if($_FILES){
    $result = $image->upload($_FILES['picture']);
}
````

Also, the variable `$result` will contain the `upload directory/image name`. Which can be useful, for storage in database or render the uploaded image using `<img src=' <?= $result ?> '/>`


#### (2) Customized size, dimension and location. 
````php
$image->fileTypes(["png", "jpeg"])  //only accept png/jpeg image types
    ->uploadDir("pics")  //create folder 'pics' if it does not exist.
    ->limitSize(["min"=>1000, "max"=>55000])  //limit image size (in bytes)
    ->limitDimension(["height"=>100, "width"=>120]);  //limit image dimensions
    ->upload($_FILES['picture']);  // upload to folder 'pics'
````

#### (3) Shrink and upload 
`shrink()` method shrinks/resizes the image according to the given dimensions (in pixels) 

````php
$image->fileTypes(["jpg", "gif", "png", "jpeg"])
    ->uploadDir("small_pics")
    ->shrink(["height"=>100, "width"=>200]) // shrink to 100*200 pixels
    ->upload($_FILES["pictures"]); // upload to small_pics folder
````

#### (4) Watermark and upload. 
`watermark()` create a watermark at the top of the main image, accepts 2 args: 
 1# - The image to use as watermark. (best to use a PNG).
 2# - The Location where to put the  watermark on the image.
Location can be a string 'center', 'bottom-right', 'bottom-left', 'top-left'...

````php
$image->fileTypes(array("png"))
    ->uploadDir("watermarked")
    ->watermark('me.png', 'bottom-right')) // put 'me.png' on center 
    ->upload($_FILES['logo']);
````

#### (5) Crop and upload. 
`crop()` method simply crops the images, by the given cordination
 ````php
$image->fileTypes(array("png"))
    ->crop(array("height"=>40, "width"=>50)) // crop/trim to 40*50 pixels
    ->upload($_FILES['logo']);
````

Please check the `examples/` folder for list of boilerplate codes

#### Renaming images
 The `upload()` method accepts two arguments. First the Image, and second (optional), a new name for that image.
 If you provide a name, then the uploaded image will be renamed, otherwise a new random&unique name will be generated
````php
// Image will be renamed 'cheeez' plus the ext. 
->upload($_FILES['fileName'], 'cheeez');

// Image will be given random name like: 1531e4b0e3bc82_QPIJLMPKQNJGF plus ext. 
->upload($_FILES['fileName']);
````

#### Manipulating uploaded images: 
The `change()` method is different from `upload()` and should not be mixed.
The `change()` will allow you to directly crop/resize/watermark an image that is already uploaded.
Think of it like this: you have a buch of images on your server, you would like to watermark, then 
you can simply do a loop over them using glob() or watever, and watermark/crop/shrink all or 
a specific one. 

```php
// The change method is like accessing any file physically and making change to it. 
//CROP IMAGES
$change = $image
 	->crop(array("height"=>10, "width"=>10))
 	->change("crop", "my_pictures/awesome.gif");

// WATERMARK IMAGES
$change = $image
 	->watermark("logo.png", "center")
 	->change("watermark", "my_pictures/passport.gif");

// SHRINK IMAGES
$change = $image
 	->shrink(array("height"=>30, "width"=>50))
 	->change("shrink", "my_pictures/paris.jpg");
````

#### What makes this secure ?
* It checks any errors thrown from the `$_FILES[]['error']` array. 
* It uses `exif_imagetype()` method to get the **real** `mime/image` type,
* Checks if image type exists in the expected types ie. `array('jpg', 'png', 'gif', 'jpeg')`
* Checks `getimagesize();` to see if the image has a valid width/height measurable in pixels.
* Uses `is_uploaded_file()` to check for a secure upload HTTP Post method, (extra security check).



#### Todo
* <del> Allow Image Resizing </del> Done.
* <del> Allow Image Watermarking </del> Done.
* <del> Allow Image Cropping </del> Done.
* <del> Handle Errors with Exceptions </del> Done.
* <del> Backward compatability for PHP 5.3 </del> Done. 
* [Single Responsibility Principle](http://en.wikipedia.org/wiki/Single_responsibility_principle)




#### License  
[Luke 3:11](http://www.kingjamesbibleonline.org/Luke-3-11/) ( Free; No license! )
