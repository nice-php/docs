Caching Generated Data
======================

Sometimes it can be worthwhile to cache generated data to be used across requests. There are two main methods of
caching data, each suited to a specific need.

*   `ConfigCache` caches static data in files to be included by PHP at runtime.

    These files are generated at time of deployment and should not change during their lifetime. Examples of
    suitable applications here is cached routing and URL generator data.
    
*   `Doctrine Cache` is suitable for highly dynamic data.

    Cache times should be relatively small to ensure that data remains fresh. Examples of this data include
    database results, entity hydration results, and fully rendered content that is ready to serve.


### ConfigCache

Coming soon.


### Doctrine Cache

Nice comes with built-in integration with [Doctrine's Cache component](https://github.com/doctrine/cache). This allows
for easy storage and retrieval of generated information, using any number of cache backends.


The first step is requiring Doctrine Cache in your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "1.0.x-dev",
            "doctrine/cache": "~2.3"
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require doctrine/dbal:~2.4
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

Using the Doctrine Cache is straightforward. Data can be stored using the `save` method and retrieved using the 
`fetch` method.

```php

// ...
$app = new Application();
// ...

// Configure your routes
$app->set('routes', function (RouteCollector $r) {
    $r->addRoute('GET', '/hello/{name}', function (Application $app, Request $request, $name) {
        $cache = $app->get('cache.default');
        $cache->save('last-hello', $name);

        return new Response('Hello, ' . $name . '!');
    });

    $r->addRoute('GET', '/last-hello', function (Application $app) {
        $cache = $app->get('cache.default');
        $name = $cache->fetch('last-hello');

        if (!$name) {
            return new Response('I have not said "Hello" to anyone :(');
        }

        return new Response('Last said hello to: ' . $name);
    });
});
```