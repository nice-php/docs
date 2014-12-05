Caching Generated Data
======================

Sometimes it can be worthwhile to cache generated data to be used across requests. There are two main methods of
caching data, each suited to a specific need.

*   `Doctrine Cache` is suitable for highly dynamic data.

    Cache times should be relatively small to ensure that data remains fresh. Examples of this data include
    database results, entity hydration results, and fully rendered content that is ready to serve.

*   `ConfigCache` caches static data in files to be included by PHP at runtime.

    These files are generated at time of deployment and should not change during their lifetime. Examples of
    suitable applications here is cached routing and URL generator data.
    
> Note: See the [advanced section](../advanced/config-cache.md) for more information on using `ConfigCache`.
    

### Doctrine Cache

Nice supports [Doctrine's Cache component](https://github.com/doctrine/cache) through an extension. This allows
for easy storage and retrieval of generated information, using any number of cache backends.

The first step is requiring the Nice Doctrine Cache bridge in your project. You can do this by updating your 
`composer.json` or running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "1.0.x-dev",
            "nice/doctrine-cache": "1.0.x-dev"
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require nice/doctrine-cache:1.0.x-dev
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
    $r->map('/hello/{name}', null, function (Application $app, Request $request, $name) {
        $cache = $app->get('cache.default');
        $cache->save('last-hello', $name);

        return new Response('Hello, ' . $name . '!');
    });

    $r->map('/last-hello', null, function (Application $app) {
        $cache = $app->get('cache.default');
        $name = $cache->fetch('last-hello');

        if (!$name) {
            return new Response('I have not said "Hello" to anyone :(');
        }

        return new Response('Last said hello to: ' . $name);
    });
});
```