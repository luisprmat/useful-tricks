# Useful Tricks
Some programming tricks for develop apps.
## FakerPHP doesn't work!
Sometimes [FakerPHP](https://fakerphp.github.io/formatters/image) doesn't generate images properly. In this case I use a dirty trick (from a [Laravel](https://github.com/laravel/laravel) App):
- Search in the file [`vendor/fakerphp/src/Faker/Provider/Image.php`](https://github.com/FakerPHP/Faker/blob/main/src/Faker/Provider/Image.php) the line with the variable `BASE_URL` and change its value by `https://placehold.jp`

```diff
class Image extends Base
{
    /**
     * @var string
     */
-   public const BASE_URL = 'https://via.placeholder.com';
+   public const BASE_URL = 'https://placehold.jp';

    public const FORMAT_JPG = 'jpg';
    public const FORMAT_JPEG = 'jpeg';
    public const FORMAT_PNG = 'png';
    
    // ...
}
```

- In the same file search the line `curl_setopt($ch, CURLOPT_FILE, $fp);` (approx line [144](https://github.com/FakerPHP/Faker/blob/main/src/Faker/Provider/Image.php#L144)) inside the static method `image` and *add* two new lines as in the code fragment.

```diff
class Image extends Base
{
    // ...
    
    public static function image(
        // parameters
    ) {
    
    // ...
    
    // save file
        if (function_exists('curl_exec')) {
            // use cURL
            $fp = fopen($filepath, 'w');
            $ch = curl_init($url);
            curl_setopt($ch, CURLOPT_FILE, $fp);
+           curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
+           curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
            $success = curl_exec($ch) && curl_getinfo($ch, CURLINFO_HTTP_CODE) === 200;
            // ...
        }
        // ...
    }
    
    // ...
}
```
- Ready!
