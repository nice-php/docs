Building Your First Nice App
============================

In your project root, create three directories: `cache`, `logs`, `web`. 

> **Notice:** `cache` and `logs` must be writable by your webserver

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

>   **Tip:** Your controller actions' parameters are automatically resolved. Named parameters in your routes can be 
    passed into your actions by defining a parameter with the same name. Additionally, `Request $request` and 
    `Application $app` can be automatically passed into your actions.
    
```php
$r->map('/foo/{bar}', null, function (Request $request, Application $app, $bar) {
    // Use $request or $app or $bar ...
});
```
    
The full signature of RouteCollector's `map` method is:

```php
interface RouteMapperInterface
{
    /**
     * Map a handler to the given methods and route
     *
     * @param string          $route    The route to match against
     * @param string          $name     The name of the route
     * @param string|callable $handler  The handler for the route
     * @param array|string[]  $methods  The HTTP methods for this handler
     * @return void
     */
    public function map($route, $name, $handler, array $methods = array('GET'));
}
```

> **Note:** See the section on [Routing](routing.md) for more information on using named routes to generate URLs.


Configuring Apache mod_rewrite
------------------------------

If using Apache, place the following in `web/.htaccess` file to enable pretty URLs without `index.php`:

```bash
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


Configuring Nginx
-----------------

Use the following as a starting point for your Nginx configuration. Change `fastcgi_pass`
to match your PHP-FPM configuration, and `root` to your document root.

```bash
server {
    listen       80;
    server_name  localhost;
    root 	     /path/to/project/web;

    location / {
        try_files $uri @rewriteindex;
    }

    location @rewriteindex {
        rewrite ^((?!\/index\.php).*)$ /index.php/$1 last;
    }

    location ~ \.php(/|$) {
        fastcgi_pass   unix:/tmp/php55-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include        fastcgi_params;
    }
}
```


Next up
-------

Nice is extremely customizable. Check out [Routing](routing.md) for information on how to fully utilize Nice's routing
features.
