Building Your First Nice App
============================

In your project root, create three directories: `cache`, `logs`, `web`. 

> `cache` and `logs` must be writable by your webserver

In your `web` directory, create `index.php` and add:

```php
<?php

use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;

require __DIR__ . '/../vendor/autoload.php';

// Enable Symfony debug error handlers
Symfony\Component\Debug\Debug::enable();

$app = new Application();

// Configure your routes
$app->set('routes', function (RouteCollector $r) {
    $r->map('/', null, function () {
        return new Response('Hello, world');
    });

    $r->map('/hello/{name}', null, function ($name) {
        return new Response('Hello, ' . $name . '!');
    });
});

// Run the application
$app->run();
```

Visit `index.php` in your browser and you'll see the message "Hello, world".

Visit `index.php/hello/Tyler` and you will see "Hello, Tyler!".

>   Tip: Your controller actions' parameters are automatically resolved. Named parameters in your routes can be 
    passed into your actions by defining a parameter with the same name. Additionally, `Request $request` and 
    `Application $app` can be automatically passed into your actions.
    
```php
$r->map('/foo', null, function (Request $request, Application $app) {
    // Use $request or $app ...
});
```
    
The full signature of RouteCollector's `map` method is:

```php
    /**
     * Map a handler to the given methods and route
     *
     * @param string          $route    The route to match against
     * @param string          $name     The name of the route
     * @param string|callable $handler  The handler for the route
     * @param array|string[]  $methods  The HTTP methods for this handler
     */
    public function map($route, $name, $handler, array $methods = array('GET'));
```

> Note: See the section on [Routing](routing.md) for more information on using named routes to generate URLs.


Configuring Apache mod_rewrite
------------------------------

If using Apache, place the following in `web/.htaccess` file to enable pretty URLs without `index.php`:

```
<IfModule mod_rewrite.c>
    RewriteEngine On

    RewriteCond %{ENV:REDIRECT_STATUS} ^$
    RewriteRule ^index\.php(/(.*)|$) %{CONTEXT_PREFIX}/$2 [R=301,L]

    RewriteCond %{REQUEST_FILENAME} -f
    RewriteRule .? - [L]

    RewriteCond %{REQUEST_URI}::$1 ^(/.+)(.+)::\2$
    RewriteRule ^(.*) - [E=BASE:%1]
    RewriteRule .? %{ENV:BASE}index.php [L]
</IfModule>
```
