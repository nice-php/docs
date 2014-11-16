Configuring the Cache extension
===============================

Nice comes with built-in integration with [Doctrine's Cache component](https://github.com/doctrine/cache). This allows
for easy storage and retrieval of generated information, using any number of cache backends.


The first step is requiring Doctrine Cache in your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "dev-master",
            "nikic/fast-route": "dev-master",
            "doctrine/cache": "~2.3"
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    php composer.phar require doctrine/dbal:~2.4
    ```

With Doctrine Cache is installed, the next step is registering the `CacheExtension` with your Nice Application.

```php
use Nice\Extension\CacheExtension;

// ...

$app = new Application();
$app->appendExtension(new CacheExtension(array(
    'connections' => array(
        'default' => array(
            'driver' => 'redis',
            'host' => '127.0.0.1'
        )
    )
)));
```


With the above configuration, Nice will create the following service:

* `cache.default` is an instance of 
[Doctrine\Common\Cache](http://www.doctrine-project.org/api/common/2.4/class-Doctrine.Common.Cache.Cache.html).