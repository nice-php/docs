Use with HttpKernel middlewares
================================

Nice uses Symfony's `HttpKernelInterface` under the hood. This means that Nice is compatible 
with [stack middlewares](http://stackphp.com).

Add your favorite middlewares to your project:

```bash
php composer.phar require stack/builder:dev-master stack/run:dev-master
```

Then, in your front controller:

```php
<?php

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpKernel\HttpCache\HttpCache;
use Symfony\Component\HttpKernel\HttpCache\Store;
use Nice\Application;
use Nice\Router\RouteCollector;

require __DIR__ . '/../vendor/autoload.php';

// Create your app
$app = new Application();

// ..add your routes
$app->set('routes', function (RouteCollector $r) {
    // ...
});

// ..and then create the stack
$stack = new Stack\Builder();
$stack->push(function ($app) {
        return new HttpCache($app, new Store(__DIR__.'/cache'));
    });

$app = $stack->resolve($app);

// Run the app
Stack\run($app);
```


Custom Middlewares
------------------

Check out the [Stack](http://stackphp.com/) website for more information on custom middlewares.