Use with Twig
=============

Nice can easily support the [Twig](http://twig.sensiolabs.org) templating engine. All that's necessary is 
the installation of the Nice Twig extension and some simple configuration.

First, add the Nice Twig extension to your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "dev-master",
            "nice/twig": "dev-master",
            "nikic/fast-route": "dev-master"
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require nice/twig:dev-master
    ```


With the extension installed, all that's needed is some modifications to your front controller:

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

Once the TwigExtension is registered with your Nice application, the following services will be available:

* `twig` is an instance of `Twig_Environment`. Use this service to render templates.

The parameter `twig.template_dir` is also made available. Its value will be whatever you've passed into the 
constructor of your `TwigExtension` instance.