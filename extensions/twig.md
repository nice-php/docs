Use with Twig
=============

Nice comes with built-in [Twig](http://twig.sensiolabs.org) support. All that's necessary is 
some simple configuration.

First, add Twig to your project:

```bash
php composer.phar require twig/twig:dev-master
```

Then, in your front controller:

```php
<?php

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;
use Nice\Extension\TwigExtension;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();

// Register the Twig extension
$app->appendExtension(new TwigExtension(__DIR__ . '/../views'));

$app->set('routes', function (RouteCollector $r) {
    $r->addRoute('GET', '/hello/{name}', function (Application $app, Request $request, $name) {
            // Use the Twig service to render templates
            $rendered = $app->get('twig')->render('index.html.twig', array(
                    'name' => $name
                ));
            
            return new Response($rendered);
        });
});

// Run the application
$app->run();
```
