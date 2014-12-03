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
            "nice/framework": "1.0.x-dev",
            "nice/twig": "1.0.x-dev"
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require nice/twig:1.0.x-dev
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
    $r->map('/hello/{name}', null, function (Application $app, Request $request, $name) {
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


Generating URLs
---------------

The Nice Twig extension includes integration with Nice's router, exposing the following useful functions:

*   `path` Generates a URL. It takes a name and optionally, an array of parameters.

    For example:
    ```twig
    <a href="{{ path('user_edit', { id: 1 }) }}">Edit User</a>
    ```
    
*   `url` Generates an absolute URL. It takes a name and optionally, an array of parameters.
